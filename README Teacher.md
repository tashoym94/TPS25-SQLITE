# TPS25-SQLITE

1. Exploring an existing database 
2. Learning how to create a database 
3. Analytical project using the database

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

Hit enter after each one. 1-6 are for more readable outputs as we go. 

1. `sqlite3` 
2. `.open JTCsql.db` 
3. `.tables` (enter) `
         when you type this command, you should see the 4 tables appear 
4. `.headers on` 
5. `.mode column` 
6. `.mode box` 

.mode list is default, but returns an unformatted output 

Helpful tip: If you get stuck in a shell: CONTROL + C

## Database Tables

```sql
SELECT * FROM demographics;     -- 1 row for every person in the dataset
SELECT * FROM legalall;         -- 1 row for every legal event (a person may have many)
SELECT * FROM programming;      -- 1 row per person referred to programming
SELECT * FROM programmingLU;    -- 1 row per program
```

---

## Viewing Tables

```sql
-- Format for viewing any table:
SELECT * FROM tablename;
```
Some basic notes on writing a query: 

1. `*` means ALL
2. Each query must be followed with a ;
3. Commands are generally not case sensitive, but it's good to get into this habit because it makes long code much more readable. 


## Exploring the `demographics` Table

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

## Sorting with ORDER BY

**Ordering data can help us interpret it more clearly**

SYNTAX:
```sql
SELECT desiredcolumn(s) FROM tablename and ORDER BY desiredcolumn
```
Let's say we want to view the different combinations of langauge and zipcodes in the demographcis table. We would write this query:

QUERY 1: 
```sql
SELECT DISTINCT language, zipcode FROM demographics;
```

From this default ordered output, it would be easy to make a list of each langage and the zip codes in which it's spoken:

English: 62701, 62702, 62703, 62704
Hindi: 62703
....

But what if I want to make a list of every langauge spoken in a zipcode instead, like:
62701: English, Hindi
62702: Spanish, ....

We need to order the output differently to make it more easy to read. 

QUERY 2: 
```sql
SELECT DISTINCT language, zipcode FROM demographics
ORDER BY zipcode;
```

Notice how ordering the data by the zip code makes it easier to read for this purpose. 

Let's explore more of our database.

```sql
SELECT * FROM demographics;                  -- 1 row per person
SELECT * FROM legalall;                      -- 1 row per case
```
The demographcis table contains information about each person in the database. 

The legalall table contains information about each legal event in the database, so there could be more than 1 row per person. 

The organization of this table happens to be by person, but oftentimes the rows of tables are not organized in any particular way. 

Let's day you are reviewing legal events by charge and you want to view this table more clearly since all of the charges are currently mixed together. 

STUDENTS: looking at the legalall table, which column should we order by?

SELECT * FROM legalall;   

✅
topcharge

```sql
SELECT * FROM legalall ORDER BY topcharge;
```

STUDENTS: Now, we not only want to see all of the events with a certain charge grouped together, but we also want to see the most recent ones first. What other column do we need to order by? Try to write that query.

Hint: 
the format for ordering by more than 1 column is:
```sql 
SELECT * FROM tablename ORDER BY column1, column2
```

✅
```sql
SELECT * FROM legalall ORDER BY topcharge, courtdate;
```

Let's go back to the demographics table. 

```sql 
SELECT * FROM demographics;
```

Assume you are a researcher who has come across this table and who is interested in how many people in different age groups have encounters with the cirminal justice system. What column might you ORDER BY to read this table more clearly?

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

# SQL Lesson 1: CRUD OPERATIONS 

Begin by making a LOCAL copy of a new database with your name: by typing: `sqlite3 dylan.db` into the terminal 

** Important note: you will not be pushing any of the following changes

`rm student1.db` allows you to remove a local database (command this outside of the sqlite3 shell) 

## Section 1: `CREATE`

```sql 
CREATE TABLE food(
	foodid INTEGER PRIMARY KEY AUTOINCREMENT,  
	foodname VARCHAR(50),      
   category VARCHAR(30),
   rating INT
);
```

```sql
INSERT INTO food (foodname, category, rating)
VALUES 
  ('Sushi', 'Japanese', 9),
  ('Tacos', 'Mexican', 8),
  ('Pad Thai', 'Thai', 6),
  ('Deep Dish Pizza', 'Italian', 10),
  ('Hot Dog', 'American', 7);
```

## Section 2: READ

```sql 
SELECT * FROM food;
```

We will come back to more complex read statments in the next unit, but the format for viewing an entire table is:

```sql 
SELECT * FROM table; --* = all
``` 


```sql 
SELECT foodname, rating FROM food WHERE rating >= 9;
```

```sql 
SELECT foodname FROM food WHERE category = 'Thai';
```

## PART 3: UPDATE
modify data in your dataset 

I went back to the Mexican place and decided the tacos were actually so good that I want to rate them 10

``` sql
UPDATE food 
SET rating = 10 
WHERE foodname = 'Tacos';
```

check the update 

```sql 
SELECT * FROM food;
```

STUDENTS: Try to rename the Deep Dish Pizza to Thin Crust Pizza 

✅
```sql 
UPDATE food 
SET foodname = 'Thin Crust Pizza' 
WHERE foodname = 'Deep Dish Pizza' 
```

## Part 4: Delete
Remove data (carefully) from the dataset 

See below the format for deleting things from a dataset 
```sql 
DELETE FROM tablename 
WHERE X = ___
```

STUDENTS: Last night you ate sushi and got food poisoning so you want to delete your sushi entry from the database. 

✅
```sql 
DELETE FROM food 
WHERE foodname = 'Sushi';
```

STUDENTS: We only want top noth food in our data now. Please remove any food with a rating under 8. 

✅
```sql 
DELETE FROM food 
WHERE rating < 8;
```

Real-world databases typically have multiple related tables. Let's look at how tables can be related. 

```sql 
-- Create a restaurants table
CREATE TABLE restaurants (
    restaurant_id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    cuisine_type TEXT,
    location TEXT,
    rating INTEGER
);
```

```sql 
INSERT INTO restaurants (name, cuisine_type, location, rating)
VALUES
('Joe''s Pizza', 'Italian', 'New York, NY', 7),
('Sushi Go', 'Japanese', 'San Francisco, CA', 2),
('La Taqueria', 'Mexican', 'Chicago, IL', 9),
('Green Earth', 'Vegetarian', 'Austin, TX', 8);
```

In a relational database, we could create relationships between foods and restaurants:
-- Example: A menu_items table linking foods and restaurants

```sql
CREATE TABLE menu_items (
    item_id INTEGER PRIMARY KEY AUTOINCREMENT,
    food_id INTEGER REFERENCES foods(foodid),
    restaurant_id INTEGER REFERENCES restaurants(restaurant_id),
    price DECIMAL (6,2)
);
```



dylan add: make them be able to make a table 

##making a table using simple data 

##making a table using csv data 
Let's make your own copy of the demographics table in your new database. Please open this csv file and save a copy to your machine. 

```bash
https://drive.google.com/file/d/1q3HVU2pb_7mna9ZXz3uO2BkJhf5eYj3f/view?usp=sharing
```

## Project: Program Analysis 

ASK: An executive at your office would like you to make a presentation on 

Let's explore our other 2 tables: programming and programminglu 

```sql 
SELECT * FROM programming;
```
```sql 
SELECT * FROM programminglu;
```






##making a table using simple data 

##making a table using csv data 
Let's make your own copy of the demographics table in your new database. Please open this csv file and save a copy to your machine. 

```bash
https://drive.google.com/file/d/1q3HVU2pb_7mna9ZXz3uO2BkJhf5eYj3f/view?usp=sharing
```



git add "README Teacher.md"
git commit -m "Update README Teacher"
git push origin main

git restore --staged "README Teacher.md"
git add "README Teacher.md"
git commit -m "Force-resync README Teacher"
git push origin main


