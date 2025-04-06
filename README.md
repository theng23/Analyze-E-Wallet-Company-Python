# Data_Wrangling

## I. Introduction
### Business question:
1. **Exploratory Data Analyst**
- Do EDA task:
  - Df payment_enriched (Merge payment_report.csv with product.csv)
  - Df transactions

2. **Data Wrangling**
- Using payment_report.csv & product.csv
  - Top 3 product_ids with the highest volume.
  - Given that 1 product_id is only owed by 1 team, are there any abnormal products against this rule?
  - Find the team has had the lowest performance (lowest volume) since Q2.2023. Find the category that contributes the least to that team.
  - Find the contribution of source_ids of refund transactions (payment_group = ‘refund’), what is the source_id with the highest contribution?
- Using transactions.csv
  - Define type of transactions (‘transaction_type’) for each row, given:
    - transType = 2 & merchant_id = 1205: Bank Transfer Transaction
    - transType = 2 & merchant_id = 2260: Withdraw Money Transaction
    - transType = 2 & merchant_id = 2270: Top Up Money Transaction
    - transType = 2 & others merchant_id: Payment Transaction
    - transType = 8, merchant_id = 2250: Transfer Money Transaction
    - transType = 8 & others merchant_id: Split Bill Transaction
    - Remained cases are invalid transactions
  - Of each transaction type (excluding invalid transactions): find the number of transactions, volume, senders and receivers.
### Dataset

- Transaction: <br>
    | Field Name | Data Type |
    |-------|-------|
    |report_month|DATETIME|
    |payment_group|STRING|
    |product_id|INT|
    |source_id|INT|
    |volume|INT|

- Product: <br>
    | Field Name | Data Type |
    |-------|-------|
    |product_id|INT|
    |category|STRING|
    |teamown|STRING|
  
- Transaction: <br>
    | Field Name | Data Type |
    |-------|-------|
    |transaction_id|INT|
    |merchant_id|INT|
    |volume|INT|
    |transType|INT|
    |transStatus|INT|
    |sender_id|INT|
    |receiver_id|INT|
    |extra_info|STRING|
    |timeStamp|INT|

## EDA and Wrangling
### EDA
1. 
