# Ecommerce Data Analysis

### Project Overview

This data analysis project aims to provide insights into the sales performance of an eCommerce company over the past year. The goal is to utilize customer segmentation, identify trends, and make data driven recommendations for key marketing and optimize sales.

### Data Source

https://www.kaggle.com/datasets/salahuddinahmedshuvo/ecommerce-consumer-behavior-analysis-data

### Tools

- Excel
- SQL Server
- Tableau

 ### Data Cleaning/Preperation

- Created a duplicate of the raw dataset to manipulate
- Checked for duplicates
- Checked for NULL or missing values

### Goals fo Data Analysis 

The goal for this project is to answer key questions such as:

- What's the best selling item category?
- Which demographic would be best to target for marketing?
- When is the best time for marketing a particulier item and to who?

### Data Analysis

``` sql
SELECT
	* INTO Ecommerce_dup
FROM
	Ecommerce_Consumer_Behavior_Ana$;

--Changing the datetime to date format
ALTER TABLE 
	ecommerce_dup
ALTER COLUMN
	Time_of_Purchase date

--checking for duplicates
SELECT 
	Customer_ID, COUNT(*)
FROM 
	Ecommerce_dup
GROUP BY 
	Customer_ID
HAVING 
	COUNT(*) > 1;

--checking for NULL values
SELECT 
    SUM(CASE WHEN Customer_ID IS NULL OR LTRIM(RTRIM(Customer_ID)) = '' THEN 1 ELSE 0 END) AS customer_id_Nulls,
    SUM(CASE WHEN Age IS NULL OR LTRIM(RTRIM(age)) = '' THEN 1 ELSE 0 END) AS age_Nulls,
    SUM(CASE WHEN Income_Level IS NULL OR LTRIM(RTRIM(Income_Level)) = '' THEN 1 ELSE 0 END) AS Income_level_Nulls,
	SUM(CASE WHEN Marital_Status IS NULL OR LTRIM(RTRIM(Marital_Status)) = '' THEN 1 ELSE 0 END) AS Marital_status_Nulls,
	SUM(CASE WHEN Education_Level IS NULL OR LTRIM(RTRIM(Education_Level)) = '' THEN 1 ELSE 0 END) AS Education_Level_Nulls,
	SUM(CASE WHEN Occupation IS NULL OR LTRIM(RTRIM(Occupation)) = '' THEN 1 ELSE 0 END) AS age_Nulls,
	SUM(CASE WHEN Purchase_Category IS NULL OR LTRIM(RTRIM(Purchase_Category)) = '' THEN 1 ELSE 0 END) AS Purchase_Category_Nulls,
	SUM(CASE WHEN Frequency_of_Purchase IS NULL OR LTRIM(RTRIM(Frequency_of_Purchase)) = '' THEN 1 ELSE 0 END) AS Frequency_of_Purchase_Nulls,
	SUM(CASE WHEN Purchase_Amount IS NULL OR LTRIM(RTRIM(Purchase_Amount)) = '' THEN 1 ELSE 0 END) AS Purchase_Amount_Nulls,
	SUM(CASE WHEN Purchase_Channel IS NULL OR LTRIM(RTRIM(Purchase_Channel)) = '' THEN 1 ELSE 0 END) AS Purchase_Channel_Nulls,
	SUM(CASE WHEN Brand_Loyalty IS NULL OR LTRIM(RTRIM(Brand_Loyalty)) = '' THEN 1 ELSE 0 END) AS Brand_Loyalty,
	SUM(CASE WHEN Product_Rating IS NULL OR LTRIM(RTRIM(Product_Rating)) = '' THEN 1 ELSE 0 END) AS Product_Rating_Nulls,
	SUM(CASE WHEN [Time_Spent_on_Product_Research(hours)] IS NULL OR LTRIM(RTRIM([Time_Spent_on_Product_Research(hours)])) = '' THEN 1 ELSE 0 END) AS Time_Spent_on_Product_Research_Nulls,
	SUM(CASE WHEN Social_Media_Influence IS NULL OR LTRIM(RTRIM(Social_Media_Influence)) = '' THEN 1 ELSE 0 END) AS Social_Media_Influence_Nulls,
	SUM(CASE WHEN Discount_Sensitivity IS NULL OR LTRIM(RTRIM(Discount_Sensitivity)) = '' THEN 1 ELSE 0 END) AS Discount_Sensitivity_Nulls,
	SUM(CASE WHEN Return_Rate IS NULL OR LTRIM(RTRIM(Return_Rate)) = '' THEN 1 ELSE 0 END) AS Return_rate_Nulls,
	SUM(CASE WHEN Customer_Satisfaction IS NULL OR LTRIM(RTRIM(Customer_Satisfaction)) = '' THEN 1 ELSE 0 END) AS Customer_Satisfaction_Nulls,
	SUM(CASE WHEN Engagement_with_Ads IS NULL OR LTRIM(RTRIM(Engagement_with_Ads)) = '' THEN 1 ELSE 0 END) AS Engagement_with_Ads_Nulls,
	SUM(CASE WHEN Device_Used_for_Shopping IS NULL OR LTRIM(RTRIM(Device_Used_for_Shopping)) = '' THEN 1 ELSE 0 END) AS Device_Used_for_Shopping_Nulls,
	SUM(CASE WHEN Payment_Method IS NULL OR LTRIM(RTRIM(Payment_Method)) = '' THEN 1 ELSE 0 END) AS Payment_Method_Nulls,
	SUM(CASE WHEN Time_of_Purchase IS NULL OR LTRIM(RTRIM(Time_of_Purchase)) = '' THEN 1 ELSE 0 END) AS Time_of_Purchase_Nulls,
	SUM(CASE WHEN Discount_Used IS NULL OR LTRIM(RTRIM(Discount_Used)) = '' THEN 1 ELSE 0 END) AS Discount_Used_Nulls,
	SUM(CASE WHEN Customer_Loyalty_Program_Member IS NULL OR LTRIM(RTRIM(Customer_Loyalty_Program_Member)) = '' THEN 1 ELSE 0 END) AS Customer_Loyalty_Program_Member_Nulls,
	SUM(CASE WHEN Purchase_Intent IS NULL OR LTRIM(RTRIM(Purchase_Intent)) = '' THEN 1 ELSE 0 END) AS Purchase_Intent_Nulls,
	SUM(CASE WHEN Shipping_Preference IS NULL OR LTRIM(RTRIM(Shipping_Preference)) = '' THEN 1 ELSE 0 END) AS Shipping_Preference_Nulls,
	SUM(CASE WHEN Time_to_Decision IS NULL OR LTRIM(RTRIM(Time_to_Decision)) = '' THEN 1 ELSE 0 END) AS Time_to_Decision_Nulls
FROM Ecommerce_dup;

--Checking for outliers and missing data. 89% are male/female. 11% are LGBTQIA+
SELECT 
	gender, COUNT(*) AS quantity
FROM 
	Ecommerce_dup
GROUP BY 
	Gender
ORDER BY
	quantity desc

--Total amount made per gender
SELECT
	gender, sum(Purchase_Amount) as Total_Spent
FROM
	Ecommerce_dup
GROUP BY 
	Gender
ORDER BY
	Total_Spent desc

--Most frequently purchased category per gender
WITH CategoryPurchaseCounts AS (
    SELECT 
        Gender,
        Purchase_Category,
        SUM(Frequency_of_Purchase) AS PurchaseCount
    FROM 
        Ecommerce_dup
    GROUP BY 
        Gender, Purchase_Category
),
RankedCategories AS (
    SELECT 
        Gender,
        Purchase_Category,
        PurchaseCount,
        RANK() OVER (PARTITION BY Gender ORDER BY PurchaseCount DESC) AS Rank
    FROM 
        CategoryPurchaseCounts
)
SELECT 
    Gender,
    Purchase_Category AS MostPurchasedCategory,
    PurchaseCount
FROM 
    RankedCategories
WHERE 
    Rank = 1;

-- Where are each gender spending the most money
WITH 
RankedCategories AS (
    SELECT 
        Gender,
      Purchase_Category,
        ROW_NUMBER() OVER (PARTITION BY gender ORDER BY gender) AS Rank
    FROM Ecommerce_dup
)
SELECT 
    Gender,
    Purchase_Category
FROM RankedCategories
WHERE Rank = 1
ORDER BY gender

--Most frequently purchased category per age
WITH CategoryPurchaseCounts AS (
    SELECT 
        age,
		Gender,
        Purchase_Category,
        SUM(Frequency_of_Purchase) AS PurchaseCount
    FROM 
        Ecommerce_dup
    GROUP BY 
        age, gender, Purchase_Category
),
RankedCategories AS (
    SELECT 
        age,
		gender,
        Purchase_Category,
        PurchaseCount,
        RANK() OVER (PARTITION BY age ORDER BY PurchaseCount DESC) AS Rank
    FROM 
        CategoryPurchaseCounts
)
SELECT 
    age,
	gender,
    Purchase_Category AS MostPurchasedCategory,
    PurchaseCount
FROM 
    RankedCategories
WHERE 
    Rank = 1
ORDER BY PurchaseCount desc;

--Which age group average the most money per purchase
WITH AveragePurchasesByAge AS (
    SELECT
        Age,
        AVG(Purchase_Amount) AS AveragePurchaseAmount
    FROM
        Ecommerce_dup
    GROUP BY
        Age
)
SELECT
    Age,
    AveragePurchaseAmount
FROM
    AveragePurchasesByAge
ORDER BY
    AveragePurchaseAmount DESC;

-- Which age group spent the most money
SELECT 
	Age, SUM(Purchase_Amount) AS TotalSpent
FROM 
	Ecommerce_dup
GROUP BY 
	Age
ORDER BY 
	TotalSpent DESC

--Which category totals the most money
SELECT 
	Purchase_Category, SUM(Purchase_Amount) AS TotalSpent
FROM 
	Ecommerce_dup
GROUP BY 
	Purchase_Category
ORDER BY 
	TotalSpent DESC

--What days and months are people shoppping the most
SELECT 
     FORMAT(Time_of_Purchase, 'MMMM') AS Month,
    COUNT(*) AS OrderCount
FROM Ecommerce_dup
GROUP BY  FORMAT(Time_of_Purchase, 'MMMM')
ORDER BY OrderCount desc

Select 
	Time_of_Purchase, count(Time_of_Purchase) as Date_Average
From 
	Ecommerce_dup
GROUP BY 
	Time_of_Purchase
ORDER BY  
	Date_Average desc

--How much does each category total per month
SELECT 
    Purchase_Category,
     FORMAT(Time_of_Purchase, 'MMMM') AS Month,
    sum(Purchase_Amount) AS TotalAmount
FROM 
    Ecommerce_dup
GROUP BY 
    Purchase_Category,
     FORMAT(Time_of_Purchase, 'MMMM')
ORDER BY 
Purchase_Category;

--Each categories best month in sales
WITH CategoryMonthlyAverages AS (
    SELECT
        Purchase_Category,
        FORMAT(Time_of_Purchase, 'MMMM') AS Month,
        SUM(Purchase_Amount) AS TotalAmount
    FROM 
        Ecommerce_dup
    GROUP BY 
        Purchase_Category,
        FORMAT(Time_of_Purchase, 'MMMM')
)
SELECT
    Purchase_Category,
    Month,
    TotalAmount
FROM (
    SELECT 
        Purchase_Category,
        Month,
        TotalAmount,
        ROW_NUMBER() OVER (PARTITION BY Purchase_Category ORDER BY TotalAmount DESC) AS rn
    FROM 
        CategoryMonthlyAverages
) AS ranked
WHERE rn = 1
ORDER BY 
    TotalAmount desc;

--Which month totals the most money
WITH MonthlySales AS (
    SELECT
         FORMAT(Time_of_Purchase, 'MMMM') AS Month,
        SUM(Purchase_Amount) AS TotalSales
    FROM 
        Ecommerce_dup
    GROUP BY 
         FORMAT(Time_of_Purchase, 'MMMM')
)
SELECT
    Month,
    TotalSales
FROM (
    SELECT 
        Month,
        TotalSales,
        ROW_NUMBER() OVER (ORDER BY TotalSales DESC) AS rn
    FROM 
        MonthlySales
) AS ranked
ORDER BY  TotalSales desc;

--Each month's highest selling category
WITH MonthlyCategorySales AS (
    SELECT
         FORMAT(Time_of_Purchase, 'MMMM') AS Month,
        Purchase_Category,
        SUM(Purchase_Amount) AS TotalSales
    FROM 
        Ecommerce_dup
    GROUP BY 
         FORMAT(Time_of_Purchase, 'MMMM'),
        Purchase_Category
)
SELECT
    Month,
    Purchase_Category,
    TotalSales
FROM (
    SELECT 
        Month,
        Purchase_Category,
        TotalSales,
        ROW_NUMBER() OVER (PARTITION BY Month ORDER BY TotalSales DESC) AS rn
    FROM 
        MonthlyCategorySales
) AS ranked
WHERE rn = 1
ORDER BY 
    Month;

--Top five categories per month for sales
WITH MonthlyCategoryTotals AS (
    SELECT
         FORMAT(Time_of_Purchase, 'MMMM') AS Month,
        Purchase_Category,
        SUM(Purchase_Amount) AS TotalAmount
    FROM 
        Ecommerce_dup
    GROUP BY 
         FORMAT(Time_of_Purchase, 'MMMM'),
        Purchase_Category
),
RankedCategories AS (
    SELECT
        Month,
        Purchase_Category,
        TotalAmount,
        RANK() OVER (PARTITION BY Month ORDER BY TotalAmount DESC) AS RankInMonth
    FROM 
        MonthlyCategoryTotals
)
SELECT
    Month,
    Purchase_Category,
    TotalAmount
FROM 
    RankedCategories
WHERE 
    RankInMonth <= 5
ORDER BY 
    Month,
    RankInMonth;

-- Location With Frequent Buyers
SELECT
	location, SUM(Frequency_of_Purchase) as PurchaseCount
FROM
	Ecommerce_dup
group by 
	Location
HAVING 
	SUM(Frequency_of_Purchase) > 12
ORDER BY  
	Purchasecount desc
--
WITH CategoryTotals AS (
    SELECT
        Location,
        Purchase_Category,
        SUM(Frequency_of_Purchase) AS total_quantity
    FROM Ecommerce_dup
    GROUP BY location, Purchase_Category
),
RankedCategories AS (
    SELECT *,
        RANK() OVER (PARTITION BY location ORDER BY total_quantity DESC) AS rnk
    FROM CategoryTotals
)
SELECT Location, Purchase_Category, total_quantity
FROM RankedCategories
WHERE rnk = 1
ORDER BY total_quantity DESC;
```
### Results/findings

- 'Jewelery & Accessories' has the highest number in sales.
- The month of April has the highest total sales.
- In August, 'Toys and Games' had a better selling month than any other category.
- 29 year old men spent the most money while 24 year old women average the most per purchase. 
- 21 year old men had the most total purchases. The category most purchased was 'Sports & Outdoors'.
- 'Oslo' is the location with the most total purchases.

### Recommendations

- Invest in marketing and promotions when sales are at their best. August (Toys & Games), April (Food & Beverages), and March (Sports & Outdoors).
- Focus on promoting and expanding 'Jewelery & Accessories', the highest earning category, especially to women 42 years of age.
- Consider promoting to 21 year old men. They had the most amount of purchases. Most of their purchases were in 'Sports & Outdoors', which is a high selling category, especially in March.
- 24 year old women had the best average per purchase and the second most amount of purchases. Consider promoting their favorite category, 'Luxury Goods' to them in December when it sells best.
- Focus on marketing in 'Oslo'. That location has the most frequent buyers.

