# Zepto E-commerce Inventory Analysis

A SQL portfolio project analyzing product pricing, discounts, stock availability, and inventory composition using a Zepto product inventory dataset.

I used PostgreSQL to structure the dataset, explore data quality, standardize pricing, and develop eight business-focused analyses.

## Project Objectives

- Explore the product catalog and its category distribution.
- Identify missing values, repeated product names, and invalid pricing records.
- Standardize monetary values from paise to rupees.
- Compare discounts and product value.
- Identify high-priced products that are out of stock.
- Estimate the selling value of available inventory by category.
- Analyze product weights and category-level inventory weight.

## Tools and Technologies

| Tool | Purpose |
|---|---|
| PostgreSQL | Table creation, data cleaning, and analysis |
| SQL | Filtering, aggregation, sorting, and calculated metrics |
| pgAdmin | Database management and CSV import |
| CSV | Source inventory dataset |

## Repository Contents

| File | Description |
|---|---|
| `Zepto_SQL_data_analysis.sql` | Table definition, exploration queries, cleaning statements, and eight business analyses |
| `zepto_v2.csv` | Source product inventory dataset |
| `README.md` | Project documentation and execution instructions |
| `LICENSE` | MIT license |

## Dataset

The dataset contains product listings with category, pricing, discount, availability, and weight information.

**Source:** [Zepto Inventory Dataset on Kaggle](https://www.kaggle.com/datasets/palvinder2006/zepto-inventory-dataset)

Each imported row receives a generated `sku_id`. Repeated product names are inspected rather than automatically removed because they may represent different product variants or listings.

### Data Dictionary

| Column | Description |
|---|---|
| `sku_id` | Generated primary key for each imported row |
| `category` | Product category |
| `name` | Product name |
| `mrp` | Maximum retail price; converted from paise to rupees |
| `discountPercent` | Listed discount percentage |
| `availableQuantity` | Available inventory units |
| `discountedSellingPrice` | Selling price after discount; converted from paise to rupees |
| `weightInGms` | Listed product weight in grams |
| `outOfStock` | Boolean stock availability flag |
| `quantity` | Source quantity field, whose interpretation may vary by product |

## Work Completed

### 1. Database Setup

Created a PostgreSQL table with:

- A generated primary key using `SERIAL`.
- Numeric fields for prices and discount percentages.
- Integer fields for inventory quantities and weights.
- A Boolean field for stock availability.
- A non-null constraint on product names.

### 2. Exploratory Data Analysis

Used SQL to:

- Count the imported records.
- Inspect sample product listings.
- Check for null values across the source fields.
- List distinct product categories.
- Compare in-stock and out-of-stock listing counts.
- Identify product names associated with multiple listings.

### 3. Data Cleaning

- Inspected records with zero MRP or zero discounted selling price.
- Removed records where MRP was zero.
- Converted MRP and discounted selling prices from paise to rupees.
- Reviewed the converted prices before running business analyses.

The current script identifies zero selling prices but only deletes records with zero MRP. Null values and repeated names are inspected, without automatic deletion or imputation.

### 4. Business Analysis

Developed eight SQL analyses:

| # | Analysis | Business Purpose |
|---|---|---|
| 1 | Top 10 distinct product-price-discount combinations by discount percentage | Compare heavily discounted listings |
| 2 | Out-of-stock products with MRP above ₹300 | Identify high-priced unavailable products |
| 3 | Category-wise available inventory selling value | Compare the monetary value of available inventory |
| 4 | Products with MRP above ₹500 and discounts below 10% | Identify expensive listings with limited discounts |
| 5 | Top 5 categories by average discount percentage | Compare category-level discount patterns |
| 6 | Price per gram for products weighing at least 100 grams | Compare listed value relative to weight |
| 7 | Product classification into Low, Medium, and Bulk weight groups | Segment listings by package weight |
| 8 | Total available inventory weight by category | Compare category-level inventory volume |

## Key Calculations

### Available Inventory Selling Value

```sql
SUM(discountedSellingPrice * availableQuantity)
```

This estimates the selling value of listed available inventory. It does not measure realized revenue, sales volume, or profit.

### Price per Gram

```sql
ROUND(discountedSellingPrice / weightInGms, 2)
```

Calculated for listings weighing at least 100 grams. Lower values indicate a lower listed price per gram, although comparisons are most meaningful between similar products.

### Total Inventory Weight

```sql
SUM(weightInGms * availableQuantity)
```

Calculates inventory weight in grams, assuming the listed weight applies to each available unit.

### Weight Classification

| Group | Rule |
|---|---|
| Low | Less than 1,000 grams |
| Medium | At least 1,000 grams and less than 5,000 grams |
| Bulk | At least 5,000 grams |

## How to Run the Project

### Prerequisites

- PostgreSQL
- pgAdmin or another PostgreSQL client
- Git, or a downloaded copy of the repository

### 1. Clone the Repository

```bash
git clone https://github.com/arunkumar09-bit/Zepto-E-commerce.git
cd Zepto-E-commerce
```

### 2. Create a Database

Create a database in pgAdmin, or run:

```sql
CREATE DATABASE zepto_analysis;
```

Connect to `zepto_analysis` before proceeding.

### 3. Create the Table

Open `Zepto_SQL_data_analysis.sql` and execute only the opening table setup statements, ending with `CREATE TABLE`.

**Run the script in stages:** create the table first, import the CSV next, and then execute exploration, cleaning, and analysis.

The opening `DROP TABLE IF EXISTS zepto` statement removes any existing `zepto` table and its data.

### 4. Import the Dataset

In pgAdmin:

1. Right-click the `zepto` table.
2. Select **Import/Export Data**.
3. Choose `zepto_v2.csv`.
4. Select CSV format and enable the header option.
5. Use UTF-8 encoding.
6. Exclude `sku_id` from the import columns so PostgreSQL generates it.
7. Match the remaining columns to the CSV fields.

Alternatively, from `psql`, run the following with the correct local file path:

```sql
\copy zepto(category, name, mrp, discountPercent, availableQuantity, discountedSellingPrice, weightInGms, outOfStock, quantity) FROM '/absolute/path/zepto_v2.csv' WITH (FORMAT csv, HEADER true, ENCODING 'UTF8');
```

### 5. Run Exploration and Cleaning

Execute the exploration queries, inspect the results, and then run the cleaning statements.

**Run the paise-to-rupees conversion only once per fresh import.** Repeating it divides the already-converted prices by 100 again.

### 6. Run the Business Queries

Execute queries Q1–Q8 to inspect pricing, discounts, stock availability, inventory selling value, and weight distribution.

## SQL Skills Demonstrated

- Relational table creation and data types
- Primary keys and non-null constraints
- Data validation with `IS NULL`
- Filtering with `WHERE`
- Aggregation using `COUNT`, `SUM`, and `AVG`
- Category-level analysis with `GROUP BY`
- Aggregate filtering with `HAVING`
- Sorting and ranking with `ORDER BY` and `LIMIT`
- Listing deduplication with `DISTINCT`
- Conditional classification using `CASE`
- Data modification using `DELETE` and `UPDATE`
- Calculated metrics and numeric rounding

## Interpretation and Limitations

- The dataset represents an inventory snapshot, so it cannot establish sales trends or customer demand.
- Available inventory selling value assumes listed units sell at the listed discounted price.
- High discounts alone do not establish overall product quality or value.
- Repeated product names do not necessarily indicate duplicate records.
- Results depend on the accuracy and consistency of source prices, weights, and quantities.
- The repository contains analysis queries; numerical findings should be reported after executing them on the imported dataset.

## Possible Extensions

- Export query results and build a dashboard.
- Compare price per gram within individual product categories.
- Add validation for negative prices, invalid discounts, and inconsistent stock flags.
- Make the cleaning workflow safe to rerun.
- Analyze inventory changes across multiple dated snapshots.

## Acknowledgments

This project follows the workflow presented in [Amlan Mohanty’s Zepto SQL analysis tutorial](https://www.youtube.com/watch?v=x8dfQkKTyP0), using the publicly available Zepto inventory dataset.

## License

This repository is distributed under the MIT License. See [LICENSE](LICENSE) for details.
