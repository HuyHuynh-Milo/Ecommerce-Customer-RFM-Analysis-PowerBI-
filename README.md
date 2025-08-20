# Ecommerce-Customer-RFM-Analysis-PBI-

📊 This project resolves the customer segmentation problem by using RFM clustering with the support of the quintile algorithm. Visualize the main output with the Power BI toolset and get the data from Google Cloud Console via BigQuery

![customer-segmentation-ezgif com-webp-to-jpg-converter](https://github.com/user-attachments/assets/6bfcf138-464a-4d0d-9c5f-58916662f7c3)

- Author: Huy Huynh
- Date: August 2025
- Tools: Power BI, Google BigQuery

## 📝 Background & Overview 

📌 **Problem Statement**

In today's competitive market, customer retention has become as crucial as customer acquisition. While attracting new customers often requires a significant investment in advertising campaigns, **retaining existing ones** is both more cost-effective and more impactful in long-term profitability. However, businesses frequently don't have a structured way to determine which set of customers is most profitable or which have the intention to leave.  
The challenge lies in segmenting customers based on their using behavior to answer key business questions:
- Who are our most loyal and profitable that we must prioritise?
- Which customers are at risk of churning and needing immediate attention?
- How can we turn new or occasional users into long-term loyal ones?
- What action could be done to achieve all those statements?

To address this, the **RFM (Recency, Frequency, Monetary)** analysis framework provides a data-driven approach to classify customers into actionable segments.

🏹 **Who is this project for**
- Marketing department who wanna take significant action on their campaign.
- Sales managers who need to identify high-value users to prioritise efforts.
- Business Analyst and Data Analyst who want to understand customer behaviour through segmentation.

## 📂 Dataset Description & Data Structure
**🗃️ Raw Dataset**
- This is a dataset of an imaginary e-commerce company named *Adventure Works*, they sells bicycles and things related to sport.
- The dataset is in the Google cloud service, to get the data, use *Google BigQuery*. Check for it:
  - [BigQuery Plaform](https://cloud.google.com/bigquery/docs/sandbox)
- This is a very massive dataset with several tables contain information about sales, customers, employees, manufacutures, etc. connect to each other using ***keys***.
  
  <img width="912" height="863" alt="image" src="https://github.com/user-attachments/assets/9cd7ad67-d1f4-4746-8dd5-d8d0aea1011c" />

- For this project we only need tables contain information of sales, customers, products and countries.

**📩 Get Data**
- Using SQL to get data form cloud service:
```sql
 -- Query Main Table
 SELECT 
        CustomerID, PersonID,  PersonType, 
        FirstName, MiddleName, LastName,
        SalesOrderID, OrderDate, 
        LineTotal, OrderQty, UnitPrice,
        ProductID
FROM `adventureworks2019.Sales.Customer` c
LEFT JOIN `adventureworks2019.Person.Person` p
ON SAFE_CAST(c.PersonID AS INT64) = p.BusinessEntityID 
LEFT JOIN `adventureworks2019.Sales.SalesOrderHeader` h
USING(CustomerID)
LEFT JOIN  `adventureworks2019.Sales.SalesOrderDetail` d
USING(SalesOrderID)
WHERE PersonType IN ("IN","SC")
;

--- Query Product Table
SELECT ProductID, Name, 
        Category , Subcategory
FROM `adventureworks2019.Production.Product`
INNER JOIN `adventureworks2019.Sales.Product`
USING(ProductID)
;

-- Query Demographic Table
SELECT CustomerID, Country, State,
        Title
FROM `adventureworks2019.Person.Customerinfo`
INNER JOIN `adventureworks2019.Person.CustomerAW`
USING(CustomerID)
```
**📬 Using Dataset**
- After the query there's only 3 tables, a quick view of these tables:
  - *Fact_sales* table (main table):

| Row | CustomerID | PersonID | PersonType | FirstName | MiddleName | LastName | SalesOrderID | OrderDate               | LineTotal | OrderQty | UnitPrice | ProductID |
|-----|------------|----------|------------|-----------|------------|----------|--------------|-------------------------|-----------|----------|-----------|-----------|
| 1   | 11012      | 11663    | IN         | Lauren    | M          | Walker   | 68413        | 2014-03-17 00:00:00 UTC | 2.29      | 1        | 2.29      | 873       |
| 2   | 11012      | 11663    | IN         | Lauren    | M          | Walker   | 68413        | 2014-03-17 00:00:00 UTC | 3.99      | 1        | 3.99      | 922       |
| 3   | 11012      | 11663    | IN         | Lauren    | M          | Walker   | 54508        | 2013-08-16 00:00:00 UTC | 4.99      | 1        | 4.99      | 921       |
| 4   | 11012      | 11663    | IN         | Lauren    | M          | Walker   | 54508        | 2013-08-16 00:00:00 UTC | 35.00     | 1        | 35.00     | 930       |
| 5   | 11012      | 11663    | IN         | Lauren    | M          | Walker   | 54508        | 2013-08-16 00:00:00 UTC | 34.99     | 1        | 34.99     | 708       |

   - *Products* Table:

| Row | ProductID | Name              | Category    | Subcategory |
|-----|-----------|-------------------|-------------|-------------|
| 1   | 907       | Rear Brakes       | Components  | Brakes      |
| 2   | 948       | Front Brakes      | Components  | Brakes      |
| 3   | 952       | Chain             | Components  | Chains      |
| 4   | 935       | LL Mountain Pedal | Components  | Pedals      |
| 5   | 936       | ML Mountain Pedal | Components  | Pedals      |

   - *Demographic* table:

| Row | CustomerID | Country | State             | Title |
|-----|------------|---------|-------------------|-------|
| 1   | 29603      | Canada  | Alberta           | Sra.  |
| 2   | 29603      | Canada  | British Columbia  | Sra.  |
| 3   | 29603      | Canada  | Labrador          | Sra.  |
| 4   | 29603      | Canada  | Manitoba          | Sra.  |
| 5   | 29603      | Canada  | Brunswick         | Sra.  |



