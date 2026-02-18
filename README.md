#  World Layoffs: Advanced Data Cleaning in SQL

##  Project Overview
In this project, I performed a comprehensive data cleaning process on a raw dataset containing global company layoffs. The goal was to transform messy, inconsistent data into a structured format ready for exploratory data analysis (EDA).

---

##  Data Cleaning Steps Taken

### 1. Staging Data
Created a `layoffs_staging` table to ensure the raw source data remained untouched. I then created a second staging table (`layoffs_staging2`) with an added `row_num` column to facilitate duplicate removal.

### 2. Duplicate Removal
Identified and removed duplicate records by using **Window Functions** (`ROW_NUMBER`) partitioned across all columns to ensure data uniqueness.

### 3. Standardization
*   **Company Names:** Used `TRIM()` to remove leading/trailing whitespace.
*   **Industry Names:** Standardized varied entries (e.g., merging all "Crypto" variations into one uniform category).
*   **Country Names:** Fixed trailing punctuation issues (e.g., "United States.").
*   **Date Formats:** Converted the `date` column from a text string to a proper `DATE` data type using `STR_TO_DATE()`.

### 4. Handling Nulls & Blanks
*   Populated missing `industry` values by joining the table to itself and matching records based on `company` and `location`.
*   Removed rows where both `total_laid_off` and `percentage_laid_off` were null, as they lacked actionable data for analysis.

### 5. Final Schema Refinement
Dropped the auxiliary `row_num` column used during cleaning to finalize the dataset for production.

---

##  Key SQL Technique: Self-Join for Data Imputation
One of the most complex parts was filling in missing industry data. I used a **Self-Join** to populate null values from existing records of the same company:

```sql
UPDATE layoffs_staging2 t1
JOIN layoffs_staging2 t2
	ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL 
AND t2.industry IS NOT NULL;
