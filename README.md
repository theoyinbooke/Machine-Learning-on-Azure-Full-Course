# Machine-Learning-on-Azure-Full-Course
This is the ReadMe file to the Beginner friendly course on Machine Learning on Azure

# About the Course
Welcome to "Machine Learning on Azure - Beginners Series"! In this course, we'll introduce you to the exciting world of machine learning and cloud computing with Microsoft Azure.

Through a series of easy-to-follow tutorials, you'll learn the basics of working with the Azure Portal and Azure Machine Learning Service Workspace. We'll cover everything from spinning up resources and setting up the AdventureWorks database, to using SQL and creating a customer churn prediction use case.

You'll also learn how to deploy your machine learning model, test your solution, and manage your resources to ensure optimal performance.

Whether you're a data scientist or developer looking to upskill, or just someone interested in exploring the power of machine learning and cloud computing, this course is perfect for you.

Enroll now and start your journey towards mastering machine learning on Azure!

## Enrol Now



## SQL Query for the Extracted Data Including the XML Column (Demographics)
;WITH XMLNAMESPACES ('http://schemas.microsoft.com/sqlserver/2004/07/adventure-works/IndividualSurvey' AS ns)
SELECT c.CustomerID, Convert(date,o.max_order_date) max_order_date, o.num_orders, o.avg_order_value, 
  CASE 
    WHEN o.max_order_date < DATEADD(month,-6,(SELECT MAX(OrderDate) FROM Sales.SalesOrderHeader))
    THEN 'Churned'
    ELSE 'Active' 
  END AS ChurnStatus,
  Convert(date, p.BirthDate) BirthDate,
  p.CommuteDistance,
  p.DateFirstPurchase,
  p.Education,
  p.Gender,
  p.HomeOwnerFlag,
  p.MaritalStatus,
  p.NumberCarsOwned,
  p.NumberChildrenAtHome,
  p.Occupation,
  p.TotalChildren,
  p.YearlyIncome
FROM Sales.Customer AS c
JOIN (
  SELECT CustomerID, MAX(OrderDate) AS max_order_date, COUNT(DISTINCT o.SalesOrderID) AS num_orders, AVG(LineTotal) AS avg_order_value
  FROM Sales.SalesOrderHeader AS o
  JOIN Sales.SalesOrderDetail AS od ON o.SalesOrderID = od.SalesOrderID
  GROUP BY CustomerID
) AS o ON c.CustomerID = o.CustomerID
LEFT JOIN (
SELECT BusinessEntityID,
   FirstName, 
   MiddleName,
   LastName,
   C.value('ns:DateFirstPurchase[1]','date') AS DateFirstPurchase,
   C.value('ns:BirthDate[1]','date') AS BirthDate,
   C.value('ns:MaritalStatus[1]','varchar(50)') AS MaritalStatus,
   C.value('ns:YearlyIncome[1]','varchar(50)') AS YearlyIncome,
   C.value('ns:Gender[1]','varchar(50)') AS Gender,
   C.value('ns:TotalChildren[1]','int') AS TotalChildren,
   C.value('ns:NumberChildrenAtHome[1]','int') AS NumberChildrenAtHome,
   C.value('ns:Education[1]','varchar(50)') AS Education,
   C.value('ns:Occupation[1]','varchar(50)') AS Occupation,
   C.value('ns:HomeOwnerFlag[1]','bit') AS HomeOwnerFlag,
   C.value('ns:NumberCarsOwned[1]','int') AS NumberCarsOwned,
   C.value('ns:CommuteDistance[1]','varchar(50)') AS CommuteDistance
FROM Person.Person p
CROSS APPLY Demographics.nodes('/ns:IndividualSurvey') AS T(C)
WHERE C.value('ns:DateFirstPurchase[1]','date') is NOT NULL
) AS p ON PersonID = p.BusinessEntityID
GROUP BY c.CustomerID, max_order_date, o.num_orders, o.avg_order_value,
  BirthDate,
  p.CommuteDistance,
  p.DateFirstPurchase,
  p.Education,
  p.Gender,
  p.HomeOwnerFlag,
  p.MaritalStatus,
  p.NumberCarsOwned,
  p.NumberChildrenAtHome,
  p.Occupation,
  p.TotalChildren,
  p.YearlyIncome



## SQL Query to select the needed data and write them into a new table called "DSMLAzure"
;WITH XMLNAMESPACES ('http://schemas.microsoft.com/sqlserver/2004/07/adventure-works/IndividualSurvey' AS ns)
SELECT c.CustomerID, Convert(date,o.max_order_date) max_order_date, o.num_orders, o.avg_order_value, 
  CASE 
    WHEN o.max_order_date < DATEADD(month,-6,(SELECT MAX(OrderDate) FROM Sales.SalesOrderHeader))
    THEN 'Churned'
    ELSE 'Active' 
  END AS ChurnStatus,
  Convert(date, p.BirthDate) BirthDate,
  p.CommuteDistance,
  p.DateFirstPurchase,
  p.Education,
  p.Gender,
  p.HomeOwnerFlag,
  p.MaritalStatus,
  p.NumberCarsOwned,
  p.NumberChildrenAtHome,
  p.Occupation,
  p.TotalChildren,
  p.YearlyIncome
INTO DSAzureML
FROM Sales.Customer AS c
JOIN (
  SELECT CustomerID, MAX(OrderDate) AS max_order_date, COUNT(DISTINCT o.SalesOrderID) AS num_orders, AVG(LineTotal) AS avg_order_value
  FROM Sales.SalesOrderHeader AS o
  JOIN Sales.SalesOrderDetail AS od ON o.SalesOrderID = od.SalesOrderID
  GROUP BY CustomerID
) AS o ON c.CustomerID = o.CustomerID
LEFT JOIN (
SELECT BusinessEntityID,
   FirstName, 
   MiddleName,
   LastName,
   C.value('ns:DateFirstPurchase[1]','date') AS DateFirstPurchase,
   C.value('ns:BirthDate[1]','date') AS BirthDate,
   C.value('ns:MaritalStatus[1]','varchar(50)') AS MaritalStatus,
   C.value('ns:YearlyIncome[1]','varchar(50)') AS YearlyIncome,
   C.value('ns:Gender[1]','varchar(50)') AS Gender,
   C.value('ns:TotalChildren[1]','int') AS TotalChildren,
   C.value('ns:NumberChildrenAtHome[1]','int') AS NumberChildrenAtHome,
   C.value('ns:Education[1]','varchar(50)') AS Education,
   C.value('ns:Occupation[1]','varchar(50)') AS Occupation,
   C.value('ns:HomeOwnerFlag[1]','bit') AS HomeOwnerFlag,
   C.value('ns:NumberCarsOwned[1]','int') AS NumberCarsOwned,
   C.value('ns:CommuteDistance[1]','varchar(50)') AS CommuteDistance
FROM Person.Person p
CROSS APPLY Demographics.nodes('/ns:IndividualSurvey') AS T(C)
WHERE C.value('ns:DateFirstPurchase[1]','date') is NOT NULL
) AS p ON PersonID = p.BusinessEntityID
GROUP BY c.CustomerID, max_order_date, o.num_orders, o.avg_order_value,
  BirthDate,
  p.CommuteDistance,
  p.DateFirstPurchase,
  p.Education,
  p.Gender,
  p.HomeOwnerFlag,
  p.MaritalStatus,
  p.NumberCarsOwned,
  p.NumberChildrenAtHome,
  p.Occupation,
  p.TotalChildren,
  p.YearlyIncome


