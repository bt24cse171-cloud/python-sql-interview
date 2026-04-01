# **SQL Assessment: Banking & Financial Services**

Please review the data schema below and write SQL queries to solve the following business problems.

## **Data Schema Overview**

Assume the following standardized schema for all questions:

### **1\. transactions (CASA Data)**

Tracks all debits and credits for customer accounts.

| Column | Type | Description |
| :---- | :---- | :---- |
| txn\_id | VARCHAR | Unique transaction identifier |
| acc\_id | VARCHAR | Account number |
| txn\_date | TIMESTAMP | Date and time of transaction |
| amount | DECIMAL | Transaction value |
| txn\_type | VARCHAR | 'CR' (Credit) or 'DR' (Debit) |
| category | VARCHAR | e.g., 'Corporate', 'ATM', 'Merchant', 'Transfer' |

### **2\. lms\_schedule (Repayment Plan)**

The expected repayment timeline generated at loan disbursal.

| Column | Type | Description |
| :---- | :---- | :---- |
| loan\_id | VARCHAR | Unique loan identifier |
| inst\_num | INT | Installment number (1, 2, 3...) |
| due\_date | DATE | Date the payment is expected |
| inst\_amount | DECIMAL | Total amount due (Principal \+ Interest) |

### **3\. lms\_payments (Actual Payments)**

Actual payments received from the customer.

| Column | Type | Description |
| :---- | :---- | :---- |
| payment\_id | VARCHAR | Unique payment identifier |
| loan\_id | VARCHAR | Loan identifier |
| payment\_date | DATE | Date the payment was received |
| paid\_amount | DECIMAL | Amount actually paid |

### **4\. bureau\_reports (Credit Bureau Data)**

External data from providers like Experian or TransUnion.

| Column | Type | Description |
| :---- | :---- | :---- |
| cust\_id | VARCHAR | Customer identifier |
| bureau\_name | VARCHAR | 'Experian', 'TransUnion', or 'Equifax' |
| score | INT | Credit score (e.g., 300-900) |
| report\_date | DATE | Date the score was generated |

## **Part 1: Warm-up**

### **Q1: High Value Transactions**

Find all acc\_ids that have made at least one 'Debit' transaction greater than $5,000 in the last 30 days.

### **Q2: Top Spenders by Category**

For each account, find the category they spent the most money on (Debit) in the last calendar month.

**Example Input (transactions):**

| acc\_id | amount | txn\_type | category |
| :---- | :---- | :---- | :---- |
| A1 | 200 | DR | Shopping |
| A1 | 500 | DR | Shopping |
| A1 | 300 | DR | Dining |

*Result: A1 \-\> Shopping ($700 total)*

## **Part 2: Transactional Analysis**

### **Q3: Identifying "Salary" Customers**

A customer is considered a "Salary" customer if they receive at least one credit transaction greater than $2,000 from a 'Corporate' source for 3 consecutive months. Write a query to flag all customers who met this condition in the last 3 months.

### **Q4: Velocity Alerts for Fraud Detection**

Write a query to identify accounts that have had more than 5 debit transactions within any 1-hour rolling window, where the total value of those transactions exceeded $10,000. Return the account\_id and the start\_time of the first transaction in that window.

## **Part 3: Loan Management System (LMS)**

### **Q5: NPA (Non-Performing Asset) Classification**

Calculate the Days Past Due (DPD) based on the oldest unpaid installment. Assign the asset class for every active loan as of today using the following logic:

* **Standard:** 0 DPD  
* **SMA-0:** 1-30 DPD  
* **SMA-1:** 31-60 DPD  
* **SMA-2:** 61-90 DPD  
* **NPA:** \> 90 DPD

### **Q6: Month-on-Month (MoM) Roll-Back Analysis**

A "Roll-Back" occurs when a customer moves from a worse DPD bucket (e.g., SMA-1) in the previous month to a better bucket (e.g., SMA-0) in the current month. Write a query to calculate the percentage of customers in the SMA-1 bucket last month who successfully rolled back to SMA-0 this month.

## **Part 4: Credit Bureau & Analytics**

### **Q7: Multi-Bureau "Golden Score" Logic**

For each customer, determine their "Active Golden Score". This score should be taken from the bureau that was updated most recently. If multiple bureaus were updated on the exact same day, take the highest score among them.

**Example Input (bureau\_reports):**

| cust\_id | bureau\_name | score | report\_date |
| :---- | :---- | :---- | :---- |
| C01 | Experian | 720 | 2024-03-01 |
| C01 | Equifax | 735 | 2024-03-01 |
| C01 | TransUnion | 710 | 2024-02-15 |

*Result: C01 \-\> 735 (Latest date, highest score if tied).*

### **Q8: Debt-to-Income (DTI) Calculation**

Calculate the Debt-to-Income (DTI) ratio for all customers. The DTI calculation logic is: (Sum of Internal EMIs \+ Sum of Bureau Scheduled Payments) / Monthly Income. Write a query to calculate this by joining internal LMS data with external Bureau files. *(Assume an additional customer\_profiles table exists with cust\_id and monthly\_income columns).*