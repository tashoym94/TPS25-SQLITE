# TPS25-SQL

Instructor: Dylan Comerford (dylancomerford1@gmail.com)

# Background: What is a relational database and what is the point of using one? 

Scenario: You're working for a company that tracks global weather data. Every day, the system you created collects readings from thousands of weather stations around the world — temperature, humidity, wind speed, and more. The data collected needs to be stored somewhere. 

Option (bad): Storing it all in one massive spreadsheet:

| Station Name | City   | Country | Latitude | Longitude | Date       | Temp | Wind  | Humidity |
|--------------|--------|---------|----------|-----------|------------|------|-------|----------|
| Station A    | NYC    | USA     | 40.71    | -74.01    | 2025-05-19 | 68°F | 12mph | 70%      |
| Station A    | NYC    | USA     | 40.71    | -74.01    | 2025-05-20 | 70°F | 8mph  | 65%      |
| Station B    | Tokyo  | Japan   | 35.68    | 139.69    | 2025-05-19 | 75°F | 10mph | 60%      |


Problems:
- Station info is repeated for every row, 
- Easy to introduce inconsistent data (e.g., "NYC" vs "New York"), 
- One change (e.g., GPS correction) must be made in every row 

Soultion: Use a relational database 
- Instead of repeating information, we split it into related tables:

table `stations`
| station_id | name      | city   | country | latitude | longitude |
|------------|-----------|--------|---------|----------|-----------|
| 1          | Station A | NYC    | USA     | 40.71    | -74.01    |
| 2          | Station B | Tokyo  | Japan   | 35.68    | 139.69    |

table `weather readings`
| reading_id | station_id | date       | temperature | wind_speed | humidity |
|------------|------------|------------|-------------|------------|----------|
| 1          | 1          | 2025-05-19 | 68°F        | 12mph      | 70%      |
| 2          | 1          | 2025-05-20 | 70°F        | 8mph       | 65%      |
| 3          | 2          | 2025-05-19 | 75°F        | 10mph      | 60%      |

Now: 
- Station info is stored once
- We link readings using station_id
- If a station moves or is renamed, we update one row, not hundreds

We can use a SQL Query to edit and analyze this data easily. 

Soon you'll understand how this query works, but the power of this query is allowing us to know the follwoing city-level insights from potentially thousands of readings with 4 simple lines of code:

| city  | AVG(temperature) |
|-------|------------------|
| NYC   | 69.0             |
| Tokyo | 75.0             |


```sql
SELECT city, AVG(temperature)
FROM weather_readings wr
JOIN stations s ON wr.station_id = s.station_id
GROUP BY city;
```

**Qualities of a relational database**

Notice how the first column of the stations table and weather reading table are unique numeric values. All tables in a relational database have this and it's called the **primary key**. 

Primary keys 
- Guarantee uniqueness for each row 
- Enable relationships between tables 
- Improves query process (fast and precise to reference)

example: 
`people`
| personid | name         | age |
|----------|--------------|-----|
| 1        | Alex Smith   | 30  |
| 2        | Jamie Patel  | 45  |
| 3        | Alex Smith   | 17  | 

Notice that 2 people have the same name here

You may wonder, why do we need a primary key to diferentate these 2 people when there's other qualities like age or maybe DOB that could do that too, but a primary key is still better because of 

- Performance: faster to reference numeric values 
- Stability: other fields can change
- Referencing: easier for you to query information a row with a short value
- Privacy: can keep track of sensitive information with a code 

`legal`
| legal_event_id | personid | charge        |
|----------------|----------|---------------|
| 101            | 1        | Trespassing   |
| 102            | 2        | Petty Theft   |
| 103            | 1        | Vandalism     |

Notice how the lega; events table contains the personids, attributing each event to a person


---
Course structure:

1. Exploring an existing database (class 1)
2. Learning how to create a database (class 2/3)
3. Analytical project using the `JTCsql.db` database, where you'll use what you've learned to create an analysis of new diversion pilot programs. (class 3)

# SQL Lesson: Exploring a Dataset

This lesson walks students through how to explore a simplified database using basic to intermediate SQL techniques, including `SELECT`, `DISTINCT`, `ORDER BY`, `WHERE`, `JOIN`, and aggregation functions like `COUNT`, `GROUP BY`, `AS` and more.


---

## Tables Overview

| Table Name       | Description                                      |
|------------------|--------------------------------------------------|
| `demographics`   | One row per person in the dataset                |
| `legalall`       | One row per legal event — a person may have many|
| `programming`    | One row per person referred to programming       |
| `programmingLU`  | One row per program, with details                |

---
## What to Type in your terminal to get started 

Hit enter after each one. 

1. `sqlite3 JTCsql.db` 
2. `.tables` 
3. `.headers on`  
4. `.mode column` 
5. `.mode box` 

Helpful tip: If you get stuck in a shell: hit `CONTROL + C` 2 times and then reset with sqlite3 JTCsql.db 

***sqlite3 is the program we are using and JTCsql.db is the database we are querying***

## Database Tables

```sql
SELECT * FROM demographics;     -- 1 row for every person in the dataset
SELECT * FROM legalall;         -- 1 row for every legal event (a person may have many)
SELECT * FROM programming;      -- 1 row per person referred to programming
SELECT * FROM programmingLU;    -- 1 row per program
```

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

SQL defualts order by least to greatest, but we can also use DESC and ASC to specify if we want to see things in ascending or descending order. 

```sql
SELECT * FROM demographics ORDER BY age DESC;
SELECT * FROM demographics ORDER BY age ASC; -- default 
```

STUDENTS: how can we sort this table by the person's name alphabetically? 

✅
```sql
SELECT * FROM demographics ORDER BY name ASC;
```

---

## WHERE, IN, and Wildcard Searches + a game! 
Sometimes, we do not need to see all of the data in a table. We can refine it using `WHERE`. 

Examples: 

I am looking into the people in our database that are Asian. While I could use `ORDER BY` race and find these people, `WHERE` allows me to only view the people I am interested in. 
```sql
SELECT * FROM demographics WHERE race = 'Asian';
```

STUDENTS: let's say I am interested in viewing this same list of Asian people, but I want it to be in the order of oldest to youngest

HINT: ORDER BY comes after WHERE 

✅
```sql
SELECT * FROM demographics WHERE race = 'Asian' ORDER BY age desc;
```

Another example: I want to view anyone in the dataset who is 40 years old. 
```sql
SELECT * FROM demographics WHERE age = 40;
```

WHERE statements can also be used to identify a range for numbers and dates: 

```sql
SELECT * FROM demographics WHERE age < 40 AND age > 20; --between 20 and 40
SELECT * FROM legalall WHERE courtdate <= '2024-03-13'; -- on or before this date
```

We can also refine to groups of items using IN or multiple = statements
```sql
SELECT * FROM demographics WHERE zipcode IN (62703, 62701);
SELECT * FROM demographics WHERE zipcode = 62703 OR zipcode = 62701;
```
Notice how these 2 statements do the same thing. 

`WHERE` statements can also contain more complex logic:

Situation: A program has been created to provide resources to any individual that lives in the 62703 area OR any individual who lives in the 62701 area that is under 30 years old. They would like the names of those individuals: 

```sql
SELECT name, zipcode, age FROM demographics
WHERE zipcode = 62703 OR (zipcode = 62701 AND age < 30);
```

STUDENTS: Another program has been created and the following people are eligible: 
- Everyone in 62703 if they speak Spanish 
- Everyone in 62701 if they speak English 

Please provide the names, zipcodes, and languages of these individuals: 

Hint: Use () like this: (criteria one) OR (criteria two)

✅
```sql
--
SELECT * FROM demographics
WHERE (language = 'Spanish' AND zipcode = 62703)
   OR (language = 'English' AND zipcode = 62701);
```

## WILDCARD SEARCHING 

Wildcard searches using `LIKE` allow you to refine based on a partial match:

```sql
SELECT * FROM demographics WHERE name LIKE 'Ma%';
SELECT * FROM demographics WHERE name = 'Ma%';         -- This won’t work
SELECT * FROM demographics WHERE name LIKE '%Ma%';
SELECT * FROM demographics WHERE name LIKE '%Simpson';
```

STUDENTS: I'm looking for someone in the databse whose last name is Smith, but I can't remember their first name. How can I retrieve that?

✅
```sql
SELECT * FROM demographics WHERE name LIKE '%Smith%';
SELECT * FROM demographics WHERE name LIKE '%Smith';
```

## COUNT + GROUP BY 

Simple counts: 

```sql
SELECT COUNT(DISTINCT personid) FROM demographics;
SELECT COUNT(DISTINCT personid) FROM legalall;
```

STUDENTS: write a query using `COUNT` to figure out how many programs are in the programmingLU table:

For reference: 
```sql
SELECT * FROM programmingLU;
```

✅
```sql
SELECT COUNT (DISTINCT name) FROM programmingLU;
```

`COUNT` can be paired with GROUP BY to aggregate 

See below how we would count the number of people per race in the demographics table:
```sql
SELECT race, COUNT(*) AS num_people --num_people is an alias, which allows us to name the column in the output
FROM demographics
GROUP BY race;
```

STUDENTS: using the format of the query above, write a query that counts the number of people per zip code in the demographics table

✅
```sql
SELECT zipcode, COUNT(*) AS num_people
FROM demographics
GROUP BY zipcode;
```

What if we want to count the distinct things in a group?
```sql 
SELECT zipcode, COUNT(DISTINCT language) AS num_languages
FROM demographics
GROUP BY zipcode;
```

## 🎮 Student Practice Game 

STUDENTS: Please complete the question and send me the name of the person in a private chat 

1. Please create a list of all males over the age of 18 in the demographics table in reverse alphabetical order (Z --> A). What is the 4th name in the list? 

✅
```sql
SELECT * FROM demographics
WHERE gender = 'Male' AND age > 18
ORDER BY name DESC;
```

2. Find the name of the person who has an 's' in their name, speaks English, is over 60, and lives in the 62704 zip code...

✅
```sql
SELECT * FROM demographics
WHERE name LIKE '%s%' AND language = 'English' AND age > 60 AND zipcode = 62704;
```

3. Please find the NAME of the person who had a Fraud legal event on Feb. 19, 2023. 

Hint: you will need to query the legalall table and then using information from that, you'll query the demographcis table...

✅
```sql
SELECT * FROM legalall where courtdate = '2023-02-19' and topcharge = 'Fraud'; -- find the personid

SELECT * FROM demographics where personid = '37094218'; --find the name
```


## 🔗 JOINs

Notice how in that last problem, we are referencing 2 tables. We can do that more efficiently using a `JOIN`. 

```sql 
SELECT * FROM legalall l
JOIN demographics d ON l.personid = d.personid 
WHERE courtdate = '2023-02-19' and topcharge = 'Fraud';
```
This statement combines the information from these 2 tables when we link them based on their column in common. 

Let's break down this proces with a more simple example: 

Our programming table contains a record for every referral to a program. 
```sql
SELECT * FROM programming;
```

Our programming look-up table contains a record for each possible program one can be referred to.
```sql
SELECT * FROM programmingLU;
```

The table I want to create has the following columns, which include information from both of these tables: 

personid, program name, chargeseligible, referral date

JOIN FORMULA 
```sql 
SELECT * (or columns)
FROM A 
JOIN B ON A.id = B.id;
```

STUDENTS: what column do both of these tables have in common? 

✅ programluid 

```sql 
SELECT personid, name, chargeseligible, referraldate 
FROM programming p
JOIN programmingLU lu on p.programluid = lu.programluid;

--notice how with a normal JOIN, it doesn't matter which table is the base table 

SELECT personid, name, chargeseligible, referraldate 
FROM programmingLU p
JOIN programming lu on p.programluid = lu.programluid;
```

Now, instead of the personid, we want the person's name. 

STUDENTS: What table table will we need to join to that has this new information?

✅ demographics 

STUDENTS: What is a column that this table shares with either the programmingLU table or the programming table?

✅ personid


STUDENTS: add another JOIN to this statement

```sql 
SELECT personid, name, chargeseligible, referraldate 
FROM programming p
JOIN programmingLU lu on p.programluid = lu.programluid;
```

✅
```sql 
SELECT personid, name, chargeseligible, referraldate 
FROM programming p
JOIN programmingLU lu on p.programluid = lu.programluid
JOIN demographics d on d.personid = p.personid;
```

Now we want to replace personid with the name from the demographics table. You'll notice that there is a name column in both the demographics table and the programming table. When that happens, we need to specify which tables we are geeting those columns from using the table alias:

```sql 
SELECT d.name, lu.name, chargeseligible, referraldate 
FROM programming p
JOIN programmingLU lu on p.programluid = lu.programluid
JOIN demographics d on d.personid = p.personid;
```
To further clarify this output, we can also rename the coulmns using `AS` 

```sql 
SELECT d.name as [person name], lu.name as [program name], chargeseligible, referraldate 
FROM programming p
JOIN programmingLU lu on p.programluid = lu.programluid
JOIN demographics d on d.personid = p.personid;
```

STUDENTS: please create a table that looks like this using JOINS 

person name, program name, completion date

✅
```sql 
SELECT d.name as [person name], lu.name as [program name], completiondate 
FROM programming p
JOIN programmingLU lu on p.programluid = lu.programluid
JOIN demographics d on d.personid = p.personid;
```
You'll notice that some of the program completion dates are NULL, as not everyone has completed their project. What if we only want the output for those with a completion date? 

We can add a where statement: 

```sql 
SELECT d.name AS [person name], lu.name AS [program name], completiondate 
FROM programming p
JOIN programmingLU lu ON p.programluid = lu.programluid
JOIN demographics d ON d.personid = p.personid
WHERE completiondate IS NOT NULL;
```

STUDENTS: add to this where statement to further



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


