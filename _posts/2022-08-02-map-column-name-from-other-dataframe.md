---
layout: post
title: Map column from different dataframes with Pandas
---

There is a time when 2 dataframes need to be summarized in a single table. 

For example, a fact table needs to get extra descriptions from outer sources. 

Using these tables as an example:

### fact_sales_daily

| item_id | payment |
|---------|---------|
| 123     | 13.89   |
| 345     | 23.45   |
| 123     | 45.00   |
| 123     | 56.77   |
| 789     | 23.76   |
| 101     | 23.43   |
| 789     | 23.76   |

### scraped_items

| item_id | desc         | source     | expiry_date |
|---------|--------------|------------|-------------|
| 123     | French Fries | amazon     | 2022-05-01  |
| 345     | Pizza        | food truck | 2022-05-01  |
| 789     | Fruit        | food truck | 2022-05-01  |
| 101     | Sweets       | shopee     | 2022-06-14  |

Say that you have a fact table that needs extra description. This extra table does not exist in the database. Probably a co-worker gives it to you in an excel file.

A very naive way is to loop and assign by row. But that will take a long time if the table is huge.

You can try this way.

### Step 1: Create dict of item_id and value you want to map

```python
item_source_dict = dict(zip(scraped_items['item_id'], scraped_items['source']))
```

### Step 2: Map the column to the dict

```python
 fact_sales_daily['item_source'] = fact_sales_daily['item_id'].map(item_source_dict)
```

This step maps the value of the item to its value from the dict. And the result is stored in a column item_source. 

You will get something like this

| item_id | payment |   source   |
|---------|---------|------------|
| 123     | 13.89   | amazon     |
| 345     | 23.45   | food truck |
| 123     | 45.00   | amazon     |
| 123     | 56.77   | amazon     |
| 789     | 23.76   | food truck |
| 101     | 23.43   | shopee     |
| 789     | 23.76   | food truck |

Simple and readable.
