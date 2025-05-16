# TPS25-SQLITE

# SQL Lesson: Exploring a Dataset

This lesson walks students through how to explore a simplified database using basic to intermediate SQL techniques, including `SELECT`, `DISTINCT`, `ORDER BY`, `WHERE`, `JOIN`, and aggregation functions like `COUNT`, `STRING_AGG`, and `CONCAT`.

---

## Lesson Goals
- Query and filter rows using `SELECT`, `WHERE`, and `IN`
- Sort results with `ORDER BY`
- Perform string and pattern matching with `LIKE`
- Use aggregation functions like `COUNT`, `CONCAT`, and `STRING_AGG`
- Perform `JOIN`s across multiple tables

---

## Tables Overview

| Table Name       | Description                                      |
|------------------|--------------------------------------------------|
| `demographics`   | One row per person in the dataset                |
| `legalall`       | One row per legal event — a person may have many|
| `programming`    | One row per person referred to programming       |
| `programmingLU`  | One row per program, with details                |

---
## What to Type in your terminal

1. sqlite3 (enter)
2. .open JTCsql.db (enter)
3. .tables (enter) 
         when you type this command, you should see the 4 tables appear 
4. .headers on (enter) 
5. .mode column (enter)
6. .mode box (enter)

.mode list is default, but returns an unformatted output 

Helpful tip: If you get stuck in a shell: CONTROL + C

## 📜 Database Tables

```sql
SELECT * FROM demographics;     -- 1 row for every person in the dataset
SELECT * FROM legalall;         -- 1 row for every legal event (a person may have many)
SELECT * FROM programming;      -- 1 row per person referred to programming
SELECT * FROM programmingLU;    -- 1 row per program
```

---

## 📖 Viewing Tables

```sql
-- Format for viewing any table:
SELECT * FROM <table name>;
-- * = all columns
```

---

## 🔎 Exploring the `demographics` Table

```sql
SELECT * FROM demographics;
```

### SELECT vs SELECT DISTINCT

**In what situations may we want to see everything in a colum vs. every unique thing in a column?**

```sql
SELECT zipcode FROM demographics;            -- lists every zipcode 
SELECT DISTINCT zipcode FROM demographics;   -- lists every unique zipcode
```
STUDENTS: if someone is asking what zipcodes are people in this database from, would we want to use `SELECT` or `SELECT DISTINCT` to get that information?

✅
`SELECT DISTINCT`

STUDENTS: let's say your boss asks you "what languages do the people that come to our office speak?". How would you query that information from the demographics table?

✅
```sql
SELECT DISTINCT language FROM demographics;
```
STUDENTS: a researcher wants a list of everyone in our demographics table, would we use `SELECT` or `SELECT DISTINCT`?

✅
```sql
SELECT name FROM demographics;
```
In some situations, we want potential repeats. For example, imagine if 2 people have the same name. These individuals should both still be accounted for. 


**How can we select more than one thing from a table?**
```sql
SELECT DISTINCT language, zipcode FROM demographics;
```

STUDENTS: try selecting the unique combination of race and gender from the demographics table

✅
```sql
SELECT DISTINCT race, gender FROM demographics;
```

---

## 🔽 Sorting with ORDER BY

**Ordering data can help us interpret it more clearly**

SYNTAX:
```sql
select <item> from <source> and ORDER BY <column(s)>
```
Question: What are the combinations of zipcode and language in this table?

QUERY 1: 
```sql
SELECT DISTINCT language, zipcode FROM demographics;
```

Let's say we want to be able to read this output as all of the languages spoken in each zipcode. 

QUERY 2: 
```sql
SELECT DISTINCT language, zipcode FROM demographics
ORDER BY zipcode;
-- Notice how ORDER BY improves readability
```

Let's explore more of our database 

```sql
SELECT * FROM demographics;                  -- 1 row per person
SELECT * FROM legalall;                      -- 1 row per case
```

```sql
--
SELECT * FROM legalall ORDER BY topcharge;
```

```sql
SELECT * FROM legalall ORDER BY topcharge, courtdate;
```

```sql
--
SELECT * FROM demographics ORDER BY age;
```

```sql
SELECT * FROM demographics ORDER BY age DESC;
SELECT * FROM demographics ORDER BY age ASC;
```

```sql
--
SELECT * FROM demographics ORDER BY name ASC;
```

---

## 🌟 WHERE, IN, and Wildcard Searches

```sql
SELECT * FROM demographics WHERE race = 'Asian';
SELECT * FROM demographics WHERE age = 40;
```

```sql
-- Ranges for numbers and dates
SELECT * FROM demographics WHERE age < 40 AND age > 20;
```

```sql
-- Groups of items
SELECT * FROM demographics WHERE zipcode IN (62703, 62701);
SELECT * FROM demographics WHERE zipcode = 62703 OR zipcode = 62701;
```

```sql
-- More complex logic
SELECT * FROM demographics
WHERE zipcode = 62703 OR (zipcode = 62701 AND age < 30);
```

```sql
--
SELECT * FROM demographics
WHERE (language = 'Spanish' AND zipcode = 62703)
   OR (language = 'English' AND zipcode = 62701);
```

---

## 🧪 LIKE and Pattern Matching

```sql
SELECT * FROM demographics WHERE name LIKE 'Ma%';
SELECT * FROM demographics WHERE name = 'Ma%';         -- This won’t work
SELECT * FROM demographics WHERE name LIKE '%Ma%';
SELECT * FROM demographics WHERE name LIKE '%Simpson';
```

---

## 🎮 Student Practice Challenges

```sql
-- 1: Find the name of the person who has an 's' in their name,
-- speaks English, is over 60, and lives in 62704
SELECT * FROM demographics
WHERE name LIKE '%s%' AND language = 'English' AND age > 60 AND zipcode = 62704;
```

```sql
-- 2: How many people speak Hindi?
SELECT * FROM demographics WHERE language = 'Hindi';
```

```sql
-- 3: View all males over 18, sorted Z → A by name
SELECT * FROM demographics
WHERE gender = 'Male' AND age > 18
ORDER BY name DESC;
```

---

## 💲 COUNT, CONCAT, and Aliases

```sql
SELECT COUNT(DISTINCT personid) FROM demographics;
SELECT COUNT(DISTINCT personid) FROM legalall;
```

```sql
-- AS renames columns
SELECT name AS "Full Name", age AS "Age in Years" FROM demographics;

-- CONCAT joins values in a row
SELECT name, age, CONCAT(name, age) AS nameandage FROM demographics;
SELECT name, age, CONCAT(name, ' ', age) AS nameandage FROM demographics;
```

```sql
--
SELECT CONCAT(name, ',', zipcode) AS nameandzip FROM demographics;
```

---

## 🥝 STRING_AGG Across Rows

```sql
SELECT * FROM legalall;

-- Topcharges grouped by person
SELECT personid, STRING_AGG(topcharge, ', ') AS personidandcharge
FROM legalall
GROUP BY personid;
```

```sql
--
SELECT personid, STRING_AGG(DISTINCT topcharge, ', ') AS personidandcharge
FROM legalall
GROUP BY personid;
```

--returning to the examole in order by.
SELECT zipcode, GROUP_CONCAT(DISTINCT language) AS languages
FROM demographics
GROUP BY zipcode;

---

## 🔗 JOINs

```sql
SELECT * FROM programming;
SELECT * FROM programmingLU;
```

```sql
--
SELECT * FROM programming p
JOIN programmingLU l ON p.programluid = l.programluid;
```

```sql
SELECT * FROM legalall;
--
SELECT * FROM demographics;
```

```sql
--
SELECT * FROM legalall l
JOIN demographics d ON l.personid = d.personid;
```

```sql
-- We want all of legalall and just name/age from demographics
SELECT l.*, d.name, d.age
FROM legalall l
JOIN demographics d ON l.personid = d.personid;
```

```sql
--
SELECT * FROM demographics d
JOIN programming p ON d.personid = p.personid;
```

```sql
-- LEFT JOIN to keep all people, even those not in programming
SELECT * FROM demographics d
LEFT JOIN programming p ON d.personid = p.personid;
```

---

## Summary

You've just walked through a realistic dataset structure using SQL: exploring tables, filtering, joining, and aggregating. The next step is learning how to create your own tables.

# SQL Lesson 2: Creating your own tables/CRUD OPERATIONS 

Begin by making a LOCAL copy of a new database with your name: by typing: `sqlite3 dylan.db` into the terminal 

** Important note: you will not be pushing any changes to the 

`rm student1.db` allows you to remove a local database (command this outside of the sqlite3 shell) 

git add "README Teacher.md"
git commit -m "Update README Teacher"
git push origin main


