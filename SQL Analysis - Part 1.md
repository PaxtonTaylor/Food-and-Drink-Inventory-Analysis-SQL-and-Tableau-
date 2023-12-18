# SQL Analysis

## Part 1

### Create Tables and Exploratory Analysis

In this portion I performed the following SQL statements:

- [CREATE TABLE](#create-table)
- [SELECT](#select)
- [COUNT()](#count())
- [Using aliases](#using-aliases)
- WHERE Clauses
  - [='exact_string_match'](#where-with-exact_string_match)
  - [LOWER](#where-with-lower)
  - [UPPER](#where-with-upper)
  - [ILIKE and % Wildcard](#where-with-ilike-and-%-wildcard)
- [CASE](#case)
- [IS NULL](#is-null)
- [DISTINCT](#distinct)
- [UPDATE table_name SET column_name](#update-and-set)
- [ORDER BY](#order-by)
- [Filtering 2 columns using a subquery and ILIKE](#subquery-and-ilike)
- [Find percentage, CAST, and CROSS JOIN](#find-percent-cast-and-cross-join)


---

### CREATE TABLE

I created 2 tables:
- foods
- drinks


```
CREATE TABLE foods (
	  food_id bigint PRIMARY KEY
	, item_name varchar(255)
	, storage_type varchar(255)
	, package_size int
	, package_size_uom varchar(255)
	, brand_name varchar(255)
	, package_price decimal(14,2)
	, package_price_updated_ts timestamp
)
;
```

---

```
CREATE TABLE drinks (
	  drink_id bigint PRIMARY KEY
	, item_name varchar(255)
	, storage_type varchar(255)
	, package_size int
	, package_size_uom varchar(255)
	, brand_name varchar(255)
	, package_price decimal(14,2)
	, package_price_updated_ts timestamp
)
;
```

Then, I uploaded the already created .csv files into Postgre SQL.


Here is a screenshot of the initial ERD with the food and drinks tables:

<img width="701" alt="foods and drinks tables ERD" src="https://github.com/PaxtonTaylor/Food-and-Drink-Inventory-Analysis-SQL-and-Tableau-/assets/147224800/f3491abf-5af3-4b8a-893a-04cdab38c9cf">

---


### SELECT

Checked tables with SELECT statements for both

```
/*
  check to make sure tables are imported correctly
*/

SELECT
	*
FROM
	foods
;
```

```
SELECT
	*
FROM
	drinks
;
```

---


**The following are more exploratory analysis within Postgre where the task of
each query is within the comments at the beginning of each query.**

### COUNT()

```
/*
	count the number of foods records
*/

SELECT
	COUNT(*)
FROM
	foods
;
```

---

```
/*
	return all columns using explicit column names
*/

SELECT
	  f.food_id
	, f.item_name
	, f.storage_type
	, f.package_size
	, f.package_size_uom
	, f.brand_name
	, f.package_price
	, f.package_price_updated_ts
FROM
	foods f
;
```

---

### Using aliases

```
/*
	change the name of package_size_uom to something
	that is not as cryptic to users
*/

SELECT
	  f.food_id
	, f.item_name
	, f.storage_type
	, f.package_size
	, f.package_size_uom AS package_size_unit_of_measurement
	, f.brand_name
	, f.package_price
	, f.package_price_updated_ts
FROM
	foods f
;
```

---

**Below are 4 different ways to find all the H-E-B private labels:**

### WHERE with 'exact_string_match'

```
/*
	find all the H-E-B private labels
*/

SELECT
	  f.food_id
	, f.item_name
	, f.storage_type
	, f.package_size
	, f.package_size_uom AS package_size_unit_of_measurement
	, f.brand_name
	, f.package_price
	, f.package_price_updated_ts
FROM
	foods f
WHERE
	brand_name =  'H-E-B (private label)’
/* = ‘exact_string_text */
;
```

---

### WHERE with LOWER

```
/*
	find all the H-E-B private labels
*/

SELECT
	  f.food_id
	, f.item_name
	, f.storage_type
	, f.package_size
	, f.package_size_uom AS package_size_unit_of_measurement
	, f.brand_name
	, f.package_price
	, f.package_price_updated_ts
FROM
	foods f
WHERE
	LOWER brand_name =  'h-e-b (private label)'	
/* lower column_name = ‘all_lowercase_column_name’ */
;
```

---

### WHERE with UPPER

```
/*
	find all the H-E-B private labels
*/

SELECT
	  f.food_id
	, f.item_name
	, f.storage_type
	, f.package_size
	, f.package_size_uom AS package_size_unit_of_measurement
	, f.brand_name
	, f.package_price
	, f.package_price_updated_ts
FROM
	foods f
WHERE
	UPPER brand_name =  'H-E-B (PRIVATE LABEL)'
/* UPPER = ‘all_upper_case_column_name’ */
;
```

---

### WHERE with ILIKE and % Wildcard

```
/*
	find all the H-E-B private labels
*/

SELECT
	  f.food_id
	, f.item_name
	, f.storage_type
	, f.package_size
	, f.package_size_uom AS package_size_unit_of_measurement
	, f.brand_name
	, f.package_price
	, f.package_price_updated_ts
FROM
	foods f
WHERE
	brand_name ILIKE ‘h-e-b%'
/* ILIKE ‘column_name_with_%_wildcard’ */
;
```

---

### CASE
```
/*
	create new column that tells whether foods are canned or not
*/

SELECT
	  f.food_id
	, f.item_name
	, f.storage_type
	, f.package_size
	, f.package_size_uom AS package_size_unit_of_measurement
	, f.brand_name
	, f.package_price
	, f.package_price_updated_ts
	, CASE 
			WHEN item_name ILIKE '%canned%' THEN 'yes'
			ELSE 'no'
	  END canned_item
FROM
	foods f
;
```

---

### IS NULL

```
/*
	locate all records where there is a null value in storage_type column
*/

SELECT
	  f.food_id
	, f.item_name
	, f.storage_type
	, f.package_size
	, f.package_size_uom AS package_size_unit_of_measurement
	, f.brand_name
	, f.package_price
	, f.package_price_updated_ts
	, CASE 
			WHEN item_name ILIKE '%canned%' THEN 'yes'
			ELSE 'no'
	  END canned_item	
FROM
	foods f
WHERE
	storage_type IS NULL
;
```

---

```
/*
	count of each brand name
*/

SELECT
	   f.brand_name
	 , COUNT(*) AS record_count
FROM
	foods f
GROUP BY
	f.brand_name
ORDER BY
	f.brand_name
;
```

---

### DISTINCT
```
/*
	return unique/distinct brand names
*/

SELECT
	 DISTINCT
	 	f.brand_name  AS distinct_brand_name
FROM
	foods f
;
```

---

### UPDATE and SET

```
/*
	update records in storage_type column from null to unknown
*/

UPDATE foods
SET storage_type = 'unknown'
WHERE storage_type is null
;


/*
	ran this query to make sure update was successful
*/

SELECT
	DISTINCT
		f.storage_type
FROM
	foods f
;
```

---

### ORDER BY

```
/*
	show records where food_id = 13, 15, and 17
*/

SELECT
	*
FROM
	foods f
WHERE
	   f.food_id = 13
	OR f.food_id = 15
	OR f.food_id = 17
ORDER BY
	f.food_id
;
```

---

### Subquery and ILIKE

```
/*
	show records where food_id = 13, 15, and 17
	and where brand_name = 'H-E-B (private label)'
*/

SELECT
	*
FROM
	foods f
WHERE
		(
		   f.food_id = 13
		OR f.food_id = 15
		OR f.food_id = 17
		)
	AND f.brand_name ILIKE 'h-e-b%'
;
```

---

### Find percent, CAST, and CROSS JOIN

```
/*
	show percent of private label brands
	
	steps:
	1. get total number of records that are H-E-B (private label)
	2. get total number of records across entire table
	3. divide #1 by #2
*/


SELECT
	CAST(n.heb_records AS decimal(10,2) )
	/ CAST(d.total_records AS decimal(10,2) )
		AS percent_of_private_labels  -- both _records were bigint and couldn't be divided.
FROM
		(
			SELECT
				COUNT(*) as heb_records
			FROM
				foods f
			WHERE
				f.brand_name ILIKE 'h-e-b%'
		) n -- numerator
	CROSS JOIN
				(
					SELECT
						COUNT(*) as total_records
					FROM
						foods f
				) d -- denominator
;
```

---

```
/*
	show distinct values for price last updated date
*/

SELECT
	DISTINCT
		f.package_price_updated_ts
FROM
	foods f
;
```
