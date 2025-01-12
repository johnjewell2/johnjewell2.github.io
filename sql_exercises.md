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

### 2. Having

### 2. Having

### 2. Having

