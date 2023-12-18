# SQL Analysis

## Part 2

### Date Functions

In this portion, I primarily deal with date functions in Postgres.

I used the following SQL functions:

- [Current Timestamp AT Time Zone](#current_timestamp-at-time-zone)
- [Difference Between Last Update and Current Timestamp](#difference-between-last-update-and-current-timestamp)
- [CAST Created Timestamp Column as Date for Calculations](#cast-created-timestamp-column-as-date-for-calculations)
- [WHERE to Filter Calculated Column](#where-to-filter-calculated-column)


---

### Current Timestamp AT Time Zone

```
/*
	calculate difference between when each product was last updated
	and today's date to decide whether to update product inventory count
	Step 1
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
	, current_timestamp AT time zone 'America/Chicago' AS price_last_updated_cst_ts
FROM
	foods f
;
```

---

### Difference Between Last Update and Current Timestamp

```
/*
	calculate difference between when each product was last updated
	and today's date to decide whether to update product inventory count
	Step 2
*/

SELECT
	  f.food_id
	, f.item_name
	, f.storage_type
	, f.package_size
	, f.package_size_uom
	, f.brand_name
	, f.package_price
	, f.package_price_updated_ts AT time zone 'America/Chicago' AS price_last_updated_cst_ts
	, current_timestamp
	, (
		current_timestamp  
		  - (f.package_price_updated_ts AT time zone 'America/Chicago')
	  ) AS days_since_last_price_update
FROM
	foods f
;
```

---

### CAST Created Timestamp Column as Date for Calculations

```
/*
	calculate difference between when each product was last updated
	and today's date to decide whether to update product inventory count.
	add new column showing just date instead of full timestamp.
*/

SELECT
	  f.food_id
	, f.item_name
	, f.storage_type
	, f.package_size
	, f.package_size_uom
	, f.brand_name
	, f.package_price
	, f.package_price_updated_ts AT time zone 'America/Chicago' AS price_last_updated_cst_ts
	, current_timestamp
	, (
		current_timestamp  
		  - (f.package_price_updated_ts AT time zone 'America/Chicago')
	  ) AS days_and_time_since_last_price_update
	, current_date
	, (
		current_date
		- (CAST(f.package_price_updated_ts AS date) )
	  ) AS days_since_last_price_update
FROM
	foods f
;
```

---

### WHERE to Filter Calculated Column

```
/*
	now show only records where last update was more than 30 days ago.
*/

SELECT
	*
FROM
	(
		SELECT
			  f.food_id
			, f.item_name
			, f.storage_type
			, f.package_size
			, f.package_size_uom
			, f.brand_name
			, f.package_price
			, f.package_price_updated_ts AT time zone 'America/Chicago' AS price_last_updated_cst_ts
			, current_timestamp
			, (
				current_timestamp  
				  - (f.package_price_updated_ts AT time zone 'America/Chicago')
			  ) AS days_and_time_since_last_price_update
			, current_date
			, (
				current_date
				- (CAST(f.package_price_updated_ts AS date) )
			  ) AS days_since_last_price_update
		FROM
			foods f
	) f -- subquery
WHERE
	f.days_since_last_price_update > 30
;
```
