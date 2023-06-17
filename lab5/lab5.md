# Lab 5: SQL Continued

## Find the name of the patron who has checked out the most books

```sql
SELECT Name
FROM Patrons
NATURAL JOIN (
    SELECT CardNum 
    FROM CheckedOut
    GROUP BY CardNum
    HAVING COUNT(*) = (
        SELECT COUNT(*) AS MaxCheckedOut
        FROM CheckedOut
        GROUP BY CardNum
        ORDER BY MaxCheckedOut DESC
        LIMIT 1
    )
) AS CardNumToCheckOut;
```

## Find the Titles of all books that were written by an author whose name starts with 'K'

```sql
SELECT Title
FROM Titles
WHERE Author LIKE 'K%';
```

## Find the Authors who have written more than one book. Assume that two Authors with the same name are the same Author for this query

```sql
SELECT Author
FROM Titles
GROUP BY Author
HAVING COUNT(*) > 1;
```

## Find the Authors for which the library has more than one book in inventory (this includes multiple copies of the same book). Assume that two Authors with the same name are the same Author for this query.

```sql
SELECT Author
FROM Titles
NATURAL JOIN (
    SELECT ISBN
    FROM Inventory
    GROUP BY ISBN
    HAVING COUNT(*) > 1
) AS DuplicateBooks;
```

## The library wants to implement a customer loyalty program based on how many books each patron has checked out. Provide an SQL query that returns the names, number of books they have checked out, and loyalty level of each Patron. The loyalty level should be the string "Platinum" if they have checked out > 2 books, "Gold" if they have 2 books, "Silver" if they have 1 book, and "Bronze" if they have no books. Hint: remember that NULL represents an unknown in SQL (it does not represent 0).

```sql
SELECT Name, CheckedOutNum,
    CASE 
        WHEN CheckedOutNum > 2 THEN 'Platinum'
        WHEN CheckedOutNum = 2 THEN 'Gold'
        WHEN CheckedOutNum = 1 THEN 'Silver'
        WHEN CheckedOutNum = 0 THEN 'Bronze'
    END AS LoyaltyLevel
FROM 
    (SELECT Patrons.Name, COUNT(Serial) as CheckedOutNum 
    FROM Patrons 
    LEFT JOIN CheckedOut ON CheckedOut.CardNum = Patrons.CardNum
    GROUP BY Patrons.Name, Patrons.CardNum) AS CheckedOutStats;
```

