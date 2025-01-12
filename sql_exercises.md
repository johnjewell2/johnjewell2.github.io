## SQL Querying Exercises

**Project description:** This project is a collection of SQL queries demonstrating the fundamentals of SQL that have been mastered.

### 1. Joins

Show category namesand product names of all products. Use the WHERE clause syntax for the JOIN. Order by categoryname.

```
SELECT CategoryName as 'Category',
        ProductName as 'Product Name'
FROM Categories,
        Products
WHERE Categories.CategoryID = Products.CategoryID
ORDER BY CategoryName DESC
```

Do #1 again, but use the JOIN syntax

```
SELECT CategoryName as 'Category',
        ProductName as 'Product Name'
FROM Categories
    JOIN Products 
        ON Categories.CategoryID = Products.CategoryID
    ORDER BY CategoryName DESC
```

Show company names and product names of all products. Use the WHERE clause syntax for the JOIN. Order by company name.

```
SELECT CompanyName as 'Company',
        ProductName as 'Product'  
FROM Suppliers,
        Products
WHERE Suppliers.SupplierID = Products.SupplierID
ORDER BY CompanyName DESC
```

Do #3 again, but use the JOIN syntax

```
SELECT CompanyName as 'Company',
        ProductName as 'Product'
FROM Suppliers
        JOIN Products 
            ON Suppliers.SupplierID = Products.SupplierID
ORDER BY CompanyName DESC
```

Show category names, company names, and product names. Order by product name.

```
SELECT CategoryName as 'Category',
        CompanyName as 'Company',
        ProductName as 'Product'
```
        
```
FROM Suppliers
    JOIN Products 
        ON Suppliers.SupplierID = Products.SupplierID
    JOIN Categories 
        ON Categories.CategoryID = Products.CategoryID
ORDER BY ProductName DESC
```

### 2. Having

-- 1. Show a list of categories and maximum units on order for each category.  Only include categories withmaximum units on orderofabove 50.

```
SELECT CategoryID,
        MAX(UnitsOnOrder) AS 'Maximum Units On Order'
FROM Products
GROUP BY CategoryID
HAVING MAX(UnitsOnOrder) > 50
```

-- 2.Show a list of categories andaverage unit priceof products in each category. 
-- Only include products with more than 0 units in stock.Onlyinclude categories withaverage unit price ofabove $25.

```
SELECT CategoryID,
        AVG(UnitPrice) AS 'Average Unit Price'
FROM Products
WHERE UnitsInStock > 0
GROUP BY CategoryID
HAVING AVG(UnitPrice) > 25
```

-- 3.Show a list of countries and number of customers in each country. Only include countries withat least fivecustomers—order by the number of customers descending.

```
SELECT Country,
        COUNT(CustomerID) AS 'Number of Customers'
FROM Customers
GROUP BY Country
HAVING COUNT(CustomerID) > 5
ORDER BY COUNT(CustomerID) DESC
```

-- 4.Show a list of countries, the number of customersin each country, and customers’contact titles. 
-- Only include Countries where a manager is the contact person (the contact title includes the word ‘manager’). 
-- Only includecountries with at least one customer.Order bythename of the country descending.

```
SELECT Country,
        COUNT(CustomerID) AS 'Number of Customers',
        ContactTitle
FROM Customers
WHERE ContactTitle LIKE '%manager%'
GROUP BY Country, ContactTitle
HAVING COUNT(CustomerID) > 0
ORDER BY Country DESC
```

-- 5.Show a list of year of order (YEAR(OrderDate)), the destination country, and average freight charges for each year. 
-- Only include orders during and after the year 1996 that shipped to Canada, Mexico, Brazil, France, and the USA. 
-- Only include years with at least ten orders. Order by year and then average freight charges descending.

```
SELECT (Year(OrderDate)) AS 'Year of Order',
        ShipCountry,
        AVG(Freight) as 'Average Freight'
FROM Orders
WHERE Year(OrderDate) >= 1996 AND ShipCountry IN ('Canada','Mexico','Brazil','France','USA')
GROUP BY ShipCountry, YEAR(OrderDate)
HAVING COUNT(OrderID) > 10
ORDER BY [Year of Order], [Average Freight] DESC
```

-- 6. Show a list of customers and average freight charges by customers in dollars and euros. 
-- Only include customers with average fright charges more than $100. The freight charges shown in the table are in dollar (1 Dollar=0.86Euro).

```
SELECT CustomerID,
        FORMAT(AVG(Freight)*0.86, 'C', 'fr-FR') AS 'AVG in Euros',
        FORMAT(AVG(Freight), 'C4') AS 'AVG in Dollars'
FROM Orders
GROUP BY CustomerID
HAVING AVG(Freight) > 100
```

### 3. Inner join, Left join

-- 1. Show the company name and city of all suppliers that supply us products (INNER JOIN, 
-- DISTINCT).

```
SELECT DISTINCT CompanyName,
        City
FROM Products
    INNER JOIN Suppliers
        ON Suppliers.SupplierID = Products.SupplierID
```

-- 2. Show the supplier company name and a count of how many products they supply us. 
-- Only include suppliers that supply us products (INNER JOIN, GROUP BY). In other 
-- words, there should be no counts of zero. 

```
SELECT s.CompanyName,
        COUNT(ProductID) as 'Number of Products'
FROM Products p
    INNER JOIN Suppliers s
        ON s.SupplierID = p.SupplierID
GROUP BY s.CompanyName
HAVING COUNT(ProductID) > 0
ORDER BY COUNT(ProductID) ASC
```

-- 3. Show the supplier company name and a count of how many products they supply us. 
-- Include suppliers that don't supply us products (LEFTJOIN, GROUP BY). In other
-- words, there may be counts of zero. 

```
SELECT s.CompanyName,
        COUNT(p.ProductID) as 'Number of Products'
FROM Suppliers s
    LEFT JOIN Products p 
        ON p.SupplierID = s.SupplierID
GROUP BY s.CompanyName
ORDER BY COUNT(p.ProductID) ASC
```

-- 4. Show the company name and city of all suppliers that DO NOT supply us products. 
-- (LEFT JOIN, WHERE IS NULL).

```
SELECT s.CompanyName,
        s.City,
        p.ProductID
FROM Suppliers s
    LEFT JOIN Products p
        ON p.SupplierID = s.SupplierID
WHERE p.ProductID IS NULL
```

-- From the tables Products and Categories: 

-- 5. Show the category ID and name of categories that have products in them (INNER 
-- JOIN, DISTINCT).

```
SELECT DISTINCT c.CategoryID,
        c.CategoryName
FROM Categories c 
    INNER JOIN Products p 
        ON p.CategoryID = c.CategoryID
```

-- 6. Show the category name and a count of how many products in the category. Only 
-- include categories that have products (INNER JOIN, GROUP BY). In other words, 
-- there should be no counts of zero.

```
SELECT c.CategoryName,
        COUNT(p.ProductID) as 'Number of Products'
FROM Products p
    INNER JOIN Categories c
        ON c.CategoryID = p.CategoryID
GROUP BY c.CategoryName
HAVING COUNT(p.ProductID) > 0
```

-- 7. Show the category name and a count of how many products in the category. Include 
-- categories that don't have products (LEFTJOIN, GROUP BY). In other words, there 
-- may be counts of zero. 

```
SELECT c.CategoryName,
        COUNT(p.ProductID) as 'Number of Products'
FROM Categories c
    LEFT JOIN Products p
        ON c.CategoryID = p.CategoryID
GROUP BY c.CategoryName
```

-- 8. Show the categoryID and name of all categories that do not have products in them. 
-- (LEFT JOIN, WHERE IS NULL).

```
SELECT c.CategoryID,
        c.CategoryName,
        COUNT(p.ProductID) as 'Number of Products'
FROM Categories c
    LEFT JOIN Products p
        ON p.CategoryID = c.CategoryID
WHERE p.ProductID IS NULL
GROUP BY c.CategoryID, c.CategoryName
```

### 4. Where, Where Exists, Where Not Exists

-- 1. Write a query to list order id, order date, and ship country for the orders with below 
-- average freight charges. (Use the Orders table table) 

```
SELECT OrderID,
        OrderDate,
        ShipCountry
FROM Orders
WHERE Freight < (SELECT AVG(Freight)
                        FROM Orders)
```

-- 2. Write a query to list order id, order date, and ship country for the orders with the least 
-- freight charges. (Use the Orders table table) 

```
SELECT OrderID,
        OrderDate,
        ShipCountry
FROM Orders
WHERE Freight = (SELECT MIN(Freight)
                        FROM Orders)
```


-- 3. Write a query to list all details of orders for customers in Germany 
-- ([NOT] IN (subquery)).   

```
SELECT *
FROM Orders
WHERE CustomerID IN
            (SELECT CustomerID
            FROM Customers
            WHERE Country = 'Germany')
```

--4. Write a query to list all details of orders for customers with the word ‘manager’ in 
-- their contact titles ([NOT] IN (subquery)).  

```
SELECT *
FROM Orders
WHERE CustomerID IN
            (SELECT CustomerID
            FROM Customers
            WHERE ContactTitle LIKE '%manager%')
```

-- 5. Write a query to list all details of customers who placed orders with more than $500 
-- freight charges ([NOT] IN (subquery)).

```
SELECT *
FROM Customers
WHERE CustomerID IN
            (SELECT CustomerID
            FROM Orders
            WHERE Freight > 500)
```

-- 6. Write a query to list all details of customers for orders processed by EmployeeID 1, 3, 
-- and 5 ([NOT] IN (subquery)).  

```
SELECT *
FROM Customers
WHERE CustomerID IN
                (SELECT CustomerID
                FROM Orders
                WHERE EmployeeID IN (1,3,5))
```

--7. Write a query to list all details of customers that have placed orders ([NOT] EXISTS 
-- Subquery). 

```
SELECT *
FROM Customers
WHERE EXISTS
        (SELECT *
        FROM Orders
        WHERE Orders.CustomerID = Customers.CustomerID)
```

-- 8. Write a query to list all details of customers that DO NOT match with any orders 
-- ([NOT] EXISTS Subquery). 

```
SELECT *
FROM Customers
WHERE NOT EXISTS
        (SELECT *
        FROM Orders
        WHERE Orders.CustomerID = Customers.CustomerID)
```

### 5. Insert, Update

-- 1. Insert a new patient whose name is Tara Smith. Her MiddleName is Grace. Leave all 
-- the other fields empty. Use the “safe” form of the INSERT statement, where you 
-- specify every field that you will provide a value for. 

```
INSERT INTO Patients (FirstName, MiddleName, LastName)
VALUES      ('Tara', 'Grace', 'Smith')

SELECT *
FROM Patients
```

-- 2. Update Tara's cell phone number to 910-656-1574. Don't change any other field. 
-- Make sure you only change Tara's cell phone by having the right PatientID=? in the 
-- WHERE clause. 

```
UPDATE Patients
SET    PhoneCell = '910-656-1574'
WHERE  PatientID = 10000
```

-- 3. Insert a new Patient whose name is Alex Johnson with the nickname of Al and phone 
-- number 919-867-5309. His date of birth is June 9th, 1984. 

```
INSERT INTO Patients (FirstName, LastName, NickName, DOB, PhoneCell)
VALUES  ('Alex', 'Johnson', 'Al', '1984-06-09', '919-867-5309')
```

-- 4. Insert a new patient whose name is Ryan Jones. His nickname is Ry. Leave all the other 
-- fields empty. Use the safe form of the INSERT statement, where you specify every field 
-- that you will provide a value for. 

```
INSERT INTO Patients (FirstName, LastName, NickName)
VALUES ('Ryan', 'Jones', 'Ry')
```

-- 5. Update Ryan's nickname to Ray. Don't change any other field. Make sure you only 
-- change Ryan's nickname by having the right PatientID=? in the WHERE clause. 

```
UPDATE Patients
SET NickName = 'Ray'
WHERE PatientID = 10003
```

-- 6. Add Tara to the Admission table. You do this by inserting a record into the Admission 
-- table. Use Tara's patient ID.  Refer to Departments table to find the DepartmentID for 
-- ‘Emergency’. 

```
INSERT INTO Admission (PatientID, DepartmentID)
VALUES (10000, 12)

```
-- 7. Add another row for Tara as she is also admitted to the ‘Radiology’ department (yes, 
-- Tara can be admitted to two departments). Look up the right DepartmentID for the 
-- ‘Radiology’ department in the Departments table. 

```
INSERT INTO Admission (PatientID, DepartmentID)
VALUES (10000, 4)
```

-- 8. Delete Ryan from the database. Make sure you only delete Ryan's record by having the 
-- right PatientID=? in the WHERE clause. 

```
DELETE Patients
WHERE PatientID = 10003
```

-- 9. We need to put Tara's mother into the system. This will take two separate operations: 
-- an insert into Patient Table, and an insert into Admission Table. Write both 
-- statements. Tara's mom's name is Mary Smith. 

```
INSERT INTO Patients (FirstName, LastName)
VALUES ('Mary', 'Smith')

INSERT INTO Admission (PatientID, DepartmentID)
VALUES (10004, 12)
```

-- 10. Enter a new patient named Andre Kincaid with the nickname Dre. He is admitted to 
-- ‘Emergency’ and ‘Cardiology’ departments. He also needs to visit the ‘Pharmacy’.

```
INSERT INTO Patients (FirstName, LastName, NickName)
VALUES ('Andre', 'Kincaid', 'Dre')

INSERT INTO Admission (PatientID, DepartmentID)
VALUES (10005, 12)

INSERT INTO Admission (PatientID, DepartmentID)
VALUES (10005, 11)

INSERT INTO Admission (PatientID, DepartmentID)
VALUES (10005, 15)
```
