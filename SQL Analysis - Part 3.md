# SQL Analysis

## Part 3

### Unions and Joins

In this portion, I performed the following SQL commands in Postgres

- [UNION ALL to Combine Two Tables](#union-all-to-combine-two-tables)
- [UNION ALL with Created Text Column Showing Source Table](#union-all-with-created-text-column-showing-source-table)
- [Create and Upload New Table Data](#create-and-upload-new-table)
- [MAX Function](#max-function)
- [Subquery Using MAX Function](#subquery-using-max-function)
- [LEFT JOIN Using 2 Subqueries](#left-join-using-2-subqueries)


---

UNION ALL to Combine Two Tables

```
/*
	combine foods and drinks tables into one.
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

UNION ALL

SELECT
	  d.drink_id
	, d.item_name
	, d.storage_type
	, d.package_size
	, d.package_size_uom AS package_size_unit_of_measurement
	, d.brand_name
	, d.package_price
	, d.package_price_updated_ts
FROM
	drinks d
;
```

---

UNION ALL with Created Text Column Showing Source Table

```
/*
	combine foods and drinks tables into one.
	create column that shows which table data is from.
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
	, 'food_data' AS source_table
FROM
	foods f

UNION ALL

SELECT
	  d.drink_id
	, d.item_name
	, d.storage_type
	, d.package_size
	, d.package_size_uom AS package_size_unit_of_measurement
	, d.brand_name
	, d.package_price
	, d.package_price_updated_ts
	, 'drinks_data' AS source_table
FROM
	drinks d
;
```

---

UNION ALL with Created Columns to show ID and Nulls

```
/*
	combine foods and drinks tables into one.
	create column that shows which table data is from.
	create 2 columns that show if records have food_id or drink_id
*/

SELECT
	  f.food_id
	, null AS drink_id
	, f.item_name
	, f.storage_type
	, f.package_size
	, f.package_size_uom AS package_size_unit_of_measurement
	, f.brand_name
	, f.package_price
	, f.package_price_updated_ts
	, 'food_data' AS source_table
FROM
	foods f

UNION ALL

SELECT
	  null as food_id
	, d.drink_id
	, d.item_name
	, d.storage_type
	, d.package_size
	, d.package_size_uom AS package_size_unit_of_measurement
	, d.brand_name
	, d.package_price
	, d.package_price_updated_ts
	, 'drinks_data' AS source_table
FROM
	drinks d
;
```

---

Create and Upload New Table Data

```
/*
	create new table food_inventories and upload csv into table
*/

CREATE TABLE food_inventories(
	  food_inventory_id bigint
	, food_item_id bigint
	, quantity int
	, inventory_last_updated_date date
)
;

SELECT *
FROM food_inventories
;
```

---

MAX Function

```
/*
	show food name and food inventory count with latest inventory update
	for all food products.
	
	1. get max inventory date
*/

SELECT
	MAX(i.inventory_last_updated_date)
FROM
	food_inventories i
;
```

Subquery Using MAX Function

```
/*
	2. get the latest inventory data.
*/

SELECT
	  i.food_inventory_id
	, i.food_item_id
	, i.quantity
	, i.inventory_last_updated_date
FROM
	food_inventories i
WHERE
	i.inventory_last_updated_date =
		(
			SELECT
				MAX(i.inventory_last_updated_date)	
FROM
				food_inventories i
		)
;
```

LEFT JOIN Using 2 Subqueries

```
/*
	3. get all the food table data and join with the latest inventory data.
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
	, i.quantity AS inventory_quantity
FROM
	foods f
LEFT JOIN
		(
				-- latest food inventory data
			SELECT
			 	 i.food_inventory_id
				, i.food_item_id
				, i.quantity
				, i.inventory_last_updated_date
			FROM
				food_inventories i
			WHERE
				i.inventory_last_updated_date =
					(
							-- max inventory date
						SELECT
							MAX(i.inventory_last_updated_date)
						FROM
							food_inventories i	
					) 
		) i
	ON
		f.food_id = i.food_inventory_id
;
```
