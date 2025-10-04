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

To address this, the **RFM (Recency, Frequency, Monetary)** analysis framework provides a data-driven approach to classify customers into actionable segments, allowing businesses to identify their most valuable customers, tailor marketing strategies, and improve customer retention and lifetime value.

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
  - Segmentation explaining:

| Segment               | Business Meaning |
|-----------------------|------------------|
| **Champions**         | High-value customers who purchase frequently and recently. Generously paying and will make a purchase anytime soon|
| **Can’t Lose Them**   | High-value customers with consistent buying in the past, who haven’t purchased in a while.  |
| **At Risk**           | Customers showing signs of churn, bought a lot and many times in the past |

- For full segmentation explanation, please check the Appendix at the bottom of this page.

🏹 **Who is this project for**
- The marketing department who wanna take significant action on their campaign.
- Sales managers who need to identify high-value users to prioritise efforts.
- Business Analyst and Data Analyst who want to understand customer behaviour through segmentation.

## 📂 2. Dataset Description & Data Structure
**🗃️ Raw Dataset**
- This is a dataset of an imaginary e-commerce company named *Adventure Works*, they sells bicycles and things related to sport.
- The dataset is in the Google cloud service, to get the data, use *Google BigQuery*. Check for it:
  - [BigQuery Plaform](https://cloud.google.com/bigquery/docs/sandbox)
- This is a very massive dataset with several tables contain information about sales, customers, employees, manufacutures, etc. connect to each other using ***keys***.
- Here is an example for the sophistication of the dataset:
 
  <img width="912" height="863" alt="image" src="https://github.com/user-attachments/assets/9cd7ad67-d1f4-4746-8dd5-d8d0aea1011c" />

- For this project we only need tables contain information of **sales, customers, products and countries**.

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

## 📋3. Main process in Power BI
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
### a. Main Dashboard

<img width="2558" height="1446" alt="Main Page" src="https://github.com/user-attachments/assets/2269b687-fad7-44fb-a63d-61e9c859c0e8" />

📝 **Overview**
- Customers: 19.12K | Transactions: 121K | Revenue: 109.85M

📊 **Highlights**

- Champions (8.8%)
  - Hold the highest revenue above all, at about 73.28M (~67%)
  - Supreme purchase frequency with more than 60K times.
- Can't lose them (9%)
  - Made high revenue at about 16.52M and purchased very frequently
  - However, they have the highest days since last purchased, 475 days on average.
- At risk (12.5%)
  - In the top 3 Revenue and Frequency
  - Long time no purchase (276 days since last purchase)
- Loyal Customer (7.4%) & Promising (13.8%)
  - Both are in the top 5 Revenue and top 6 Frequency
  - Loyal Customers just take 7.4% of total customers but made high revenue at about 4.92M.

🚀 **Business Reccomendations**

- Retention Focus
  - Launch targeted win-back campaigns for “Can’t Lose Them” and “At Risk” customers (e.g., loyalty offers, personalized discounts).
  - Prevent high-value churn to protect millions in revenue.
- Growth Opportunities
  - Nurture Promising and Potential Loyalists with engagement programs to convert them into Champions.
  - Encourage cross-sell/upsell opportunities among mid-tier segments.
- Resource Allocation
  - Prioritize marketing spend on Champions & Loyal Customers for long-term retention.
  - Reduce investment in Lost/Hibernating segments where ROI is low.

 ### 📈 b. Segment Analyze
 
- The discussion will focus on the top 3 most profitable and most frequently purchase segmentations: **Champions, Can't lose them, and At risk**. 

 👑 **Champions** (8.8% Customers | 66.7% Revenue)
 
<img width="2562" height="1448" alt="Champions" src="https://github.com/user-attachments/assets/a272e74b-c464-48ac-9e22-fb48deafe0f9" />

- Highlights:
  - Generate the highest revenue share despite small base.
  - Purchase concentration in Mountain Bikes (esp. Mountain-200 series).
  - Price and quality are dominant purchase drivers.
  - Customer type mainly B2B (91%), with strong presence in Australia & Southwest US.

- Recommendations:
  - Implement VIP/loyalty programs to retain this high-value group.
  - Bundle bikes with accessories/components to maximize revenue per purchase.
  - Focus marketing and partnerships on key regions and B2B customers.


🛑 **Can’t Lose Them** (9% Customers | 15% Revenue)

<img width="2560" height="1440" alt="Cant lose them" src="https://github.com/user-attachments/assets/e17146bf-0e5c-4822-be19-f16a7dbd2e98" />

- Highlights:
  - Contribute 15% total revenue (~16.5M), very frequent buyers.
  - Dominated by Mountain Bikes & Road Bikes (95% revenue).
  - Customers are mainly motivated by Price (47%) and Promotion (23%).
  - Strong base in the Southwest, Northwest, and Australia.
  - However, they have the longest recency (475 days since last purchase).

- Recommendations:
  - Launch targeted win-back campaigns (discounts, personalized offers).
  - Bundle promotions for bike accessories/components to diversify spend.
  - Apply loyalty perks (extended warranty, exclusive early launches) to re-engage.

⚠️ **At Risk** (12.5% Customers | 7.1% Revenue)

<img width="2562" height="1448" alt="At Risk" src="https://github.com/user-attachments/assets/f065d6c8-972b-4ade-8f3d-3339054d20b5" />

- Highlights:
  - Account for 7.1% revenue (~7.6M), mostly from Road Bikes (59%).
  - Heavily price-sensitive (72%), low response to quality/brand.
  - Customer base grew to ~2,200, but engagement dropped sharply (276 days since last purchase).
  - Highly concentrated in Australia.

- Recommendations:
  - Run time-limited promotions focused on road bikes to trigger urgency.
  - Offer installment/financing plans to reduce the price barrier.
  - Use customer surveys to explore why churn risk is rising.

### 🛬 3. Summary

Customer behavior is highly polarized: a small share of customers (Champions) contributes the majority of revenue, while other groups like Can’t Lose Them and At Risk show strong historical value but declining activity.
- Revenue is concentrated in a few loyal segments, highlighting their critical role in business performance.
- Retention risk is significant: many high-value customers have not purchased for a long time.
- Opportunities exist in re-engaging lapsed buyers and nurturing mid-tier customers to move upward.

Recommendations:
- Double down on loyalty programs and premium experiences for Champions.
- Design win-back campaigns for lapsed high-value customers.
- Use targeted promotions and personalized offers to shift promising customers into higher-value groups.

## 📑4. Appendix 
### 👥 Customer Info
- For more information about each customer, check the customer info dashboard
- The dashboard contains deep information about each customer
- Selecting the customer name to reach out to their personal information

<img width="2544" height="1440" alt="image" src="https://github.com/user-attachments/assets/d3010d4a-041c-4186-b172-364583eb76ac" />


### 📄 RFM Segment station full table
- RFM Segmentation full explanation

| Customer Segment           | RFM Score Range                                                                 | Description                                                                                   |
|----------------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| Champions                  | 555, 554, 545, 554, 455, 445                                                     | Customers who buy frequently, spend a lot, and are very recent, making them the most valuable. |
| Loyal Customers            | 543, 444, 435, 355, 354, 345, 344, 335                                           | Customers who are committed and buy regularly over time.                                       |
| Potential Loyalist         | 553, 551, 552, 541, 542, 533, 532, 531, 452, 451, 442, 441, 431, 453, 433, 432, 423, 353, 352, 351, 342, 341, 333, 323 | Customers who have made multiple purchases recently and could become loyal with encouragement. |
| Recent Customers           | 512, 511, 422, 421, 412, 411, 311                                               | Customers who have recently purchased, indicating potential for further purchases.             |
| Promising                  | 525, 524, 523, 522, 521, 515, 514, 513, 425, 424, 413, 414, 415, 315, 314, 313   | Customers with moderate frequency and recency, showing promise for growth.                     |
| Customers Needing Attention| 535, 534, 443, 434, 343, 334, 325, 324                                          | Customers who need attention to prevent them from becoming inactive.                           |
| About To Sleep             | 331, 321, 312, 221, 213                                                         | Customers who are at risk of becoming inactive due to reduced activity.                        |
| At Risk                    | 255, 254, 245, 244, 235, 252, 243, 242, 235, 234, 225, 224, 153, 152, 145, 143, 142, 135, 134, 133, 125, 124 | Customers who were once active but are now at risk of leaving due to inactivity.              |
| Can't Lose Them            | 155, 154, 144, 214, 215, 115, 114, 113                                          | Highly valuable customers who must be retained at all costs.                                   |
| Hibernating (ngủ đông)     | 332, 322, 231, 241, 251, 233, 232, 223, 222, 132, 123, 122, 212, 211             | Inactive customers who may return with the right incentives.                                   |
| Lost                       | 111, 112, 121, 131, 141, 151                                                    | Customers who are unlikely to return due to long inactivity.                                   |


  
