# Data_Wrangling - Ewallet company | Python, GoogleColab
Please see the coding file attached or reach this link: [Data_Wrangling](https://colab.research.google.com/drive/18jfqAQUxfjQRU-8-3guUfHlovR9QKcV7#scrollTo=Ypzc_zE2QR0G)

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
This is a file database in this topic: [**Dataset**](https://drive.google.com/drive/u/0/folders/1tj4H9FB1_t4GmTdPOSr4YOrU2RRPHurI)
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

## II. EDA and Wrangling
### EDA
1. Payment_Enriched

```python
payment_enriched = final_payment_report.merge(product, on='product_id', how = 'left')
payment_enriched.info()
```
![45c3286f-0111-4833-872a-758c2d3b7162](https://github.com/user-attachments/assets/36751094-f285-4597-8ecd-ef84a5a79b8c)

- Checking Dtype -> Next step: No action
- Category and Team_own: Missing Vales -> Next step: Check missing values

```python
payment_enriched.isnull().sum()
```
![7231ebb1-1d43-4238-ba49-c5317a51ce8c](https://github.com/user-attachments/assets/ae30b0d7-6302-4bd0-b326-d766379aabfd)

- Handle missing value: Replace **<NA>** to 'Unknown'

```python
cleaned_payment_enriched = payment_enriched.replace(np.nan, 'Unknown')
cleaned_payment_enriched
```
![85cd0f70-0257-4783-b5a8-41887ce3fb18](https://github.com/user-attachments/assets/aec6b508-1ac8-47aa-89c7-62493f757b27)

- Check duplicate value -> No duplicate value
```python
duplicates_payment_enriched = cleaned_payment_enriched[cleaned_payment_enriched.duplicated(subset=['report_month','payment_group','product_id','source_id'], keep=False)]
duplicates_payment_enriched.head()
```

![0121f53d-7605-4719-ae3d-01b33f9cc122](https://github.com/user-attachments/assets/f1765502-b0fc-4837-aba1-eb26a5470f2d)

2. Transactions

```python
transactions = pd.read_csv('transactions.csv',encoding = 'utf-8')
transactions.info()
```
![2b853513-f2f1-49ad-ac87-b67fb9dfb535](https://github.com/user-attachments/assets/180a2eab-149a-4986-acc5-a2333f20c513)

- Check Dtype
  - Incorrect data type 'Sender_id' -> Action: convert to int
  - Incorrect data type 'Receiver_id' -> Action: convert to int
  - Incorrect data type 'Extra_info' -> Action: convert to string
- Check missing values
  - Sender_id: missing values -> Action: replace NaN to 0
  - Receiver_id: missing values -> Action: replace NaN to 0
  - Extra_info: missing values -> Action: ignore
 
```python
transactions['sender_id'] = transactions['sender_id'].fillna(0).astype('int')
transactions['receiver_id'] = transactions['receiver_id'].fillna(0).astype('int')
transactions['extra_info'] = transactions['extra_info'].astype('string')
transactions.info()
```

![273b5396-c435-4375-939e-847e0489a44e](https://github.com/user-attachments/assets/96d7959d-263f-491a-944b-15cea3637728)

- Check duplicate value -> Duplicate value with Primary Key: 'transaction_id' -> Action: Drop

```python
no_dup_transactions = transactions.drop_duplicates(subset = 'transaction_id', ignore_index= False)
no_dup_transactions.head()
```

![ac48a300-accc-482a-8e03-b7acce151ca5](https://github.com/user-attachments/assets/91500a7d-6f9a-4628-a61b-08418415b6ab)

### Data Wrangling

```python
Data_wrangling = payment_report.merge(product, on='product_id', how = 'left')
Data_wrangling.head()
```
1. Top 3 product_id with the highest volumne
```python
Data_wrangling.groupby('product_id')['volume'].sum().sort_values(ascending=False).head(3)
```

![05d02963-acd5-4040-a676-f7cab41e7222](https://github.com/user-attachments/assets/18c121b9-b5f8-43be-aada-eec11ae8669f)

2. Given that 1 product_id is only owed by 1 team, are there any abnormal products against this rule?
```python
Data_wrangling['product_team_own'] = (
    Data_wrangling.groupby(['product_id','source_id'])['team_own'].transform('nunique')
)
Data_wrangling.head()
```
![4979c9a5-2b00-4721-8cbb-f7def3d06903](https://github.com/user-attachments/assets/f3d0bde3-82b5-4efa-8972-4f267a6cea6f)

```python
Non_Team = Data_wrangling[Data_wrangling['product_team_own'] != 1].reset_index()
Non_Team.groupby('product_id')['team_own'].nunique()
```
![efd32368-7e63-43a1-9a04-901055b7af09](https://github.com/user-attachments/assets/809a586f-dfa9-4f35-acac-5573617a1e52)


3. Find the team has had the lowest performance (lowest volume) since Q2.2023. Find the category that contributes the least to that team.
```python
Data_wrangling[Data_wrangling['report_month'] >= '2023-04-01'].groupby(['team_own','category'])['volume'].sum().sort_values(ascending=True).head(1)
```
![3a8bbd4c-fe2a-4621-9309-27f0f5a29a1f](https://github.com/user-attachments/assets/822bd310-39bd-40fb-bdb2-e85e3dd18d15)



4. Find the contribution of source_ids of refund transactions (payment_group = ‘refund’), what is the source_id with the highest contribution?

```python
Data_wrangling[Data_wrangling['payment_group'] == 'refund'].groupby('source_id')['volume'].sum().sort_values(ascending=False).head(1)
```

![b35de446-52d5-4f46-b2e5-7663ee3e33d1](https://github.com/user-attachments/assets/2a4b2ed8-bb0e-488b-b1d4-ebf8e36665fd)


5. Define type of transactions (‘transaction_type’) for each row, given:
  - transType = 2 & merchant_id = 1205: Bank Transfer Transaction
  - transType = 2 & merchant_id = 2260: Withdraw Money Transaction
  - transType = 2 & merchant_id = 2270: Top Up Money Transaction
  - transType = 2 & others merchant_id: Payment Transaction
  - transType = 8, merchant_id = 2250: Transfer Money Transaction
  - transType = 8 & others merchant_id: Split Bill Transaction
  - Remained cases are invalid transactions

```python
def transaction_type(row):
    if row['transType'] == 2 and row['merchant_id'] == 1205:
        return 'Bank Transfer Transaction'
    elif row['transType'] == 2 and row['merchant_id'] == 2260:
        return 'Withdraw Money Transaction'
    elif row['transType'] == 2 and row['merchant_id'] == 2270:
        return 'Top Up Money Transaction'
    elif row['transType'] == 2 and row['merchant_id'] not in [1205,2260,2270]:
        return 'Payment Transaction'
    elif row['transType'] == 8 and row['merchant_id'] == 2250:
        return 'Transfer Money Transaction'
    elif row['transType'] == 8:
        return 'Split Bill Transaction'
    else:
        return 'Invalid Transaction'

transactions['Type'] = transactions.apply(transaction_type, axis=1)
transactions.head()

```

![c7326ee8-92b0-40cf-b17f-0d985cab42ad](https://github.com/user-attachments/assets/0dc67985-a47b-4283-9504-551d5f8d3235)


6. Of each transaction type (excluding invalid transactions): find the number of transactions, volume, senders and receivers.
```python
transactions[transactions['Type'] != 'Invalid Transaction'].groupby('Type').agg({'transaction_id': 'count'
                                                                                , 'volume': 'sum'
                                                                                , 'sender_id': 'nunique'
                                                                                , 'receiver_id': 'nunique'})
```


![9a6f7ae7-5077-483e-9e1f-58afdfe6e0f6](https://github.com/user-attachments/assets/ea5e957d-77a8-4cbd-9660-62f5b642f859)
