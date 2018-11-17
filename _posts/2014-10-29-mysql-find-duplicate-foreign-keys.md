---
title: MySQL - Find duplicate foreign keys
layout: post
comments: true
published: true
description: 
keywords: mysql
---

A foreign key is a field (or collection of fields) in one table that uniquely 
identifies a row of another table. As a database designer you have to ensure 
that all foreign keys are unique. In MySQL you can find all foreign keys in 
the information_schema.key_column_usage table.

Find all duplicate foreign keys:

```sql
SELECT 
    constraint_name AS 'constraint_name',
    CONCAT(table_name, '.', column_name) AS 'foreign_key',
    CONCAT(
        referenced_table_name,
        '.',
        referenced_column_name
    ) AS 'reference'
    #, COUNT(*) AS c
FROM
    information_schema.key_column_usage 
WHERE 
    referenced_table_name IS NOT NULL 
    AND table_schema = database()
GROUP BY 'column_name' 
# HAVING c > 1 ;
HAVING COUNT(*) > 1;
```

Find all foreign keys:

```sql
SELECT 
  constraint_name AS 'name',
  CONCAT(table_name, '.', column_name) AS 'foreign_key',
  CONCAT(
    referenced_table_name,
    '.',
    referenced_column_name
  ) AS 'reference' 
FROM
  information_schema.key_column_usage 
WHERE referenced_table_name IS NOT NULL 
  AND table_schema = DATABASE() 
ORDER BY NAME;

```

### Source

* http://stackoverflow.com/questions/688549/finding-duplicate-values-in-mysql
* http://lists.mysql.com/mysql/204859
* http://stackoverflow.com/questions/854128/find-duplicate-records-in-mysql