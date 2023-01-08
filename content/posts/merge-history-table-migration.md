+++
title = "Merge Two History tables"
description = "Migration script in PostgreSQL to merge two history tables into a single one"
tags = [
    "sql",
	"postgres",
	"migration",
	"docker"
]
date = "2020-04-22"
+++

History tables hold the past state of an entity by saving all the values of the original entity along with a date and time range field that specify when these values were valid, I will refer to this field as *"validity"*.
This pattern is used when you need to remember the changes an entity went through over time, it is often used with, for example, pricing data, so that you can track changes of some price as time passes.

### The problem

At the company I currently work at we wanted to reduce unnecessary complexity that got introduced as our business case evolved, this required merging two tables into a single one, which also required to to merge their corresponding history tables as well. These two history tables have different periods of validity, as they've been changed at different times in history, so the merged history table must properly reflect changes in both tables. This is a bit difficult to explain with words so here's a graph.

Lets say that we have a `products` table (`A`) and a `pricing` table (`B`) and the final result we are looking for after the migration is to merge these two into a single `AB` table.

```
 A1------------A2-----
       B1-------------

 AB1---AB2-----AB3----
```

At `A1` point in time we created our product, then at `B1` we created our price and assigned it to the product `A`, then a bit later we updated the product at `A2`.
After migration, our history table should have show all values from `A` with null fields that belong to values `B` since it has not been created yet, and validity filed holding `[A1, infinity)`.
Then, once `B` is created at `B1` the new history entity should have all values from `A` and `B` and validity field holding `[B1, infinity)` as well as update the previous validity field with `[A1, B1)` meaning this history entity is now only valid in between times `A1` and `B1` or `AB1` and `AB2`.

### The setup

Here is some demo data to explain with code what I fail to explain with words.

```sql
CREATE TABLE "price_history" (
  "id"            INT       NOT NULL,
  "amount"        MONEY     NOT NULL,
  "validity"      TSRANGE   NOT NULL
);

CREATE TABLE "product_history" (
  "id"        INT       NOT NULL,
  "price_id"  INT       NOT NULL,
  "name"      TEXT      NOT NULL,
  "validity"  TSRANGE   NOT NULL
);

INSERT INTO "product_history" VALUES
    (1, 1, 'Apple',        '[2020-01-02 00:00, 2020-01-10 00:00)'),
    (1, 1, 'Green Apple',  '[2020-01-10 00:00, infinity)'),
    (2, 1, 'Ornage',       '[2020-01-20 00:00, 2020-01-23 00:00)'),
    (2, 1, 'Orange',       '[2020-01-23 00:00, 2020-01-29 00:00)'),
    (2, 1, 'Blood Orange', '[2020-01-29 00:00, infinity)');

INSERT INTO "price_history" VALUES
    (1, 20.00, '[2020-01-01 00:00, 2020-01-15 00:00)'),
    (1, 10.00, '[2020-01-15 00:00, 2020-01-25 00:00)'),
    (1, 20.00, '[2020-01-25 00:00, infinity)');
```

And we want to migrate the contents of these two tables into a single table that looks like this
```sql
CREATE TABLE "product_v2_history" (
  "id"        INT       NOT NULL,
  "name"      TEXT      NOT NULL,
  "price"     MONEY     NOT NULL,
  "validity"  TSRANGE   NOT NULL
);
```

At the end of the migration we expect the content of the `product_v2_history` table to be like this
```
id       name            price       validity
1        "Apple"         "20.00"     [2020-01-02 00:00, 2020-01-10 00:00)
1        "Green Apple"   "20.00"     [2020-01-10 00:00, 2020-01-15 00:00)
1        "Green Apple"   "10.00"     [2020-01-15 00:00, 2020-01-25 00:00)
1        "Green Apple"   "20.00"     [2020-01-25 00:00, infinity)
2        "Ornage"        "10.00"     [2020-01-20 00:00, 2020-01-23 00:00)
2        "Orange"        "10.00"     [2020-01-23 00:00, 2020-01-25 00:00]
2        "Orange"        "20.00"     [2020-01-25 00:00, 2020-01-29 00:00]
2        "Blood Orange"  "20.00"     [2020-01-29 00:00, infinity]
```

Take a look at the `validity` field in the new table and how it reflects to `validity` fields old tables `product_history` and `price_history`.

### The solution

So, you can make this work with a script that looks something like this
```sql
INSERT INTO product_v2_history
SELECT
  product_history.id,
  product_history.name,
  price_history.amount,
  TSRANGE(
    GREATEST(LOWER(product_history.validity), LOWER(price_history.validity)),
    CASE
      WHEN UPPER(price_history.validity) = 'infinity' THEN UPPER(product_history.validity)
      WHEN UPPER(product_history.validity) = 'infinity' THEN UPPER(price_history.validity)
      ELSE LEAST(UPPER(product_history.validity), UPPER(price_history.validity))
    END
  )
  FROM product_history
  FULL JOIN price_history
    ON product_history.price_id = price_history.id AND product_history.validity && price_history.validity
  ORDER BY product_history.validity;
```

The Postgres operator that helps us a bunch here is `&&` which returns `true` if two ranges given intersect.

> If you want to keep the name of one of the older tables and merge the data of the other history table into it, the simplest way to achieve this is to create a new history table anyway, and afterwards delete the old table and rename the new one.

When I first thought about how to do this, I imagined the solution will be much more trouble, but as I played around with test data, it turned out to be quite a simple one to do.
If you want to play around with this, you can use [this repo](https://github.com/TopHatCroat/history-migration)
