# Ecommerce-Customer-RFM-Analysis-PBI-

📊 This project resolves the customer segmentation problem by using RFM clustering with the support of the quintile algorithm. Visualize the main output with the Power BI toolset and get the data from Google Cloud Console via BigQuery

![customer-segmentation-ezgif com-webp-to-jpg-converter](https://github.com/user-attachments/assets/6bfcf138-464a-4d0d-9c5f-58916662f7c3)

- Author: Huy Huynh
- Date: August 2025
- Tools: Power BI, Google BigQuery

## 📝 1. Background & Overview 

### 📌 a. Problem Statement

In today's competitive market, customer retention has become as crucial as customer acquisition. While attracting new customers often requires a significant investment in advertising campaigns, **retaining existing ones** is more cost-effective. However, businesses frequently don't have a structured way to determine which set of customers is most profitable or which have the intention to leave.  
The challenge lies in segmenting customers based on their using behavior to answer key business questions:
- Who are our most loyal and profitable that we must prioritise?
- Which customers are at risk of churning and needing immediate attention?
- How can we turn new or occasional users into long-term loyal ones?
- What action could be done to achieve all those statements?

To address this, the **RFM (Recency, Frequency, Monetary)** analysis framework provides a data-driven approach to classify customers into actionable segments.

### ❓ b. About RFM Analysis
- RFM (Recency – Frequency – Monetary) analysis is a customer segmentation technique widely used in marketing and customer relationship management. It evaluates customer value based on three key dimensions:
  - **Recency (R)**: How recently a customer made a purchase.
  - **Frequency (F)**: How often a customer makes a purchase.
  - **Monetary (M)**: How much money a customer spends.
- In this project, I applied the quantile method to score each dimension. Specifically, all customers were divided into five equal groups based on their Recency, Frequency, and Monetary values:
  - Score 5 → Top 20% (best customers for that metric)
  - Score 1 → Bottom 20% (lowest-performing customers)
- After getting each of R-F-M score, fuse them into 1 score in the format of 442, 433, 541, 343, 321 ,etc. calling RFM and assign it with a suitable mark. For example:
  - Customers with very high scores such as 555, 554, 544 are called Champion
  - Low RFM score, such as 111, 112, 121 are Lost customer 
- Segmentation
  - From the RFM analysis, I focused on 6 key customer segments that are the most relevant for business strategy:

| Segment               | Business Meaning |
|-----------------------|------------------|
| **Champions**         | High-value customers who purchase frequently and recently. Generously paying and will make a purchase anytime soon|
| **Loyal Customers**   | Regular customers with consistent purchases and spending pretty high|
| **Potential Loyalist**| Customers who have been buying recently, middle level of spending, and purchased many times  |
| **Promising**         | Recently buying or maybe new customers with high spending but not shopping quite often. |
| **Can’t Lose Them**   | High-value customers with consistent buying in the past, who haven’t purchased in a while.  |
| **At Risk**           | Customers showing signs of churn, bought a lot and many times in the past |



🏹 **Who is this project for**
- The marketing department who wanna take significant action on their campaign.
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
- After the query there's only **3 tables**, a quick view of these tables:
  - **Fact_sales** table (main table):

| Row | CustomerID | PersonID | PersonType | FirstName | MiddleName | LastName | SalesOrderID | OrderDate               | LineTotal | OrderQty | UnitPrice | ProductID |
|-----|------------|----------|------------|-----------|------------|----------|--------------|-------------------------|-----------|----------|-----------|-----------|
| 1   | 11012      | 11663    | IN         | Lauren    | M          | Walker   | 68413        | 2014-03-17 00:00:00 UTC | 2.29      | 1        | 2.29      | 873       |
| 2   | 11012      | 11663    | IN         | Lauren    | M          | Walker   | 68413        | 2014-03-17 00:00:00 UTC | 3.99      | 1        | 3.99      | 922       |
| 3   | 11012      | 11663    | IN         | Lauren    | M          | Walker   | 54508        | 2013-08-16 00:00:00 UTC | 4.99      | 1        | 4.99      | 921       |
| 4   | 11012      | 11663    | IN         | Lauren    | M          | Walker   | 54508        | 2013-08-16 00:00:00 UTC | 35.00     | 1        | 35.00     | 930       |
| 5   | 11012      | 11663    | IN         | Lauren    | M          | Walker   | 54508        | 2013-08-16 00:00:00 UTC | 34.99     | 1        | 34.99     | 708       |

   - **Products** Table:

| Row | ProductID | Name              | Category    | Subcategory |
|-----|-----------|-------------------|-------------|-------------|
| 1   | 907       | Rear Brakes       | Components  | Brakes      |
| 2   | 948       | Front Brakes      | Components  | Brakes      |
| 3   | 952       | Chain             | Components  | Chains      |
| 4   | 935       | LL Mountain Pedal | Components  | Pedals      |
| 5   | 936       | ML Mountain Pedal | Components  | Pedals      |

   - **Demographic** table:

| Row | CustomerID | Country | State             | Title |
|-----|------------|---------|-------------------|-------|
| 1   | 29603      | Canada  | Alberta           | Sra.  |
| 2   | 29603      | Canada  | British Columbia  | Sra.  |
| 3   | 29603      | Canada  | Labrador          | Sra.  |
| 4   | 29603      | Canada  | Manitoba          | Sra.  |
| 5   | 29603      | Canada  | Brunswick         | Sra.  |

- The main table contain more than 110K rows and the other 2 contain specific information of products and customers 

## 📋 Main process in Power BI
### 1. ⚒️ Preprocessing Data
💻 **a. RFM Calculate**
**Recency calculation**
- For the recency, just substract the most recent day in the data set with the last day that user made a purchase (lastPurchasedDay)
```dax
Recency = DATEDIFF([lastPurchasedDay],DATE(2014,7,1), Day)
```
- Assign Recency score to each person using PERCENTILE.INC function 
```dax
Recency score = SWITCH(
                    TRUE,
                    'RFM Table'[Recency Value] <= PERCENTILE.INC('RFM Table'[Recency Value],0.20),"5",
                    'RFM Table'[Recency Value] <= PERCENTILE.INC('RFM Table'[Recency Value],0.40),"4",
                    'RFM Table'[Recency Value] <= PERCENTILE.INC('RFM Table'[Recency Value],0.60),"3",
                    'RFM Table'[Recency Value] <= PERCENTILE.INC('RFM Table'[Recency Value],0.80),"2",
                      "1"
                     )
```
**Frequency calculation**
- For the frequency, count the number of sales order each person made
```dax
Frequency = COUNT(Fact_Sales[SalesOrderID])
```
- Assign to Frequency score
```dax
Frequency Score = 
            SWITCH(TRUE(),
                'RFM Table'[Frequency Value] <= PERCENTILE.INC('RFM Table'[Frequency Value],0.20),"1",
                'RFM Table'[Frequency Value] <= PERCENTILE.INC('RFM Table'[Frequency Value],0.40),"2",
                'RFM Table'[Frequency Value] <= PERCENTILE.INC('RFM Table'[Frequency Value],0.60),"3",
                'RFM Table'[Frequency Value] <= PERCENTILE.INC('RFM Table'[Frequency Value],0.80),"4",
                "5")
```
**Monetary calculation**
- For the monetary, get the total amount of money each customer spent
```dax
Monetary = SUM(Fact_Sales[LineTotal])
```
- Assign to Monetary score
```dax
Monetary Score = SWITCH(
                    TRUE(),
                    'RFM Table'[Monetary Value] <= PERCENTILE.INC('RFM Table'[Monetary Value],0.2),"1",
                    'RFM Table'[Monetary Value] <= PERCENTILE.INC('RFM Table'[Monetary Value],0.4),"2",
                    'RFM Table'[Monetary Value] <= PERCENTILE.INC('RFM Table'[Monetary Value],0.6),"3",
                    'RFM Table'[Monetary Value] <= PERCENTILE.INC('RFM Table'[Monetary Value],0.8),"4",
                    "5")
```
✈️ **b. Connect all these 3 values into the RFM score**
```dax
RFM Score = 'RFM Table'[Recency score]&'RFM Table'[Frequency Score]&'RFM Table'[Monetary Score]
```
- The output looks like this:

| RFM Score | CustomerID |
|-----------|------------|
| 511       | 11418      |
| 111       | 12741      |
| 211       | 12799      |
| 311       | 12800      |
| 511       | 12802      |

- Finally, insert the table of RFM segment for the PBI to assign by using RFM Score as key value. The table looks like this:

| RFM Score | Segment        |
|-----------|----------------|
| 455       | Champions      |
| 445       | Champions      |
| 543       | Loyal Customers|
| 444       | Loyal Customers|
| 435       | Loyal Customers|

🚀 **c. Connection of tables**

- The Fact-Sales is the main table that contains all the information about transactions.
- 4 dim-tables are: SalesReason (reason the bought), Product, Demographic, and RFM Table
- RFM Score Segment, which indicates the assigning segment is connected to RFM Table

<img width="1872" height="1420" alt="Tables connection" src="https://github.com/user-attachments/assets/5820107c-4b64-4265-bdb7-43724082628a" />

### 2. 📈 Dashboard (Power BI)
**Main Dashboard**

<img width="2556" height="1436" alt="Main Page" src="https://github.com/user-attachments/assets/da013d48-ecd5-47cb-bf95-0cfeb737694d" />

