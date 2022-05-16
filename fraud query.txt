-- Create CardBase table
CREATE TABLE CARDBASE (card_number VARCHAR(40),
                      card_family VARCHAR(30),
                      credit_limit INT,
                      customer_id VARCHAR(40));
COPY cardbase from 'C:\Users\HR\Documents\data set\Fraud\CardBase.csv'
delimiter ',' csv header;
SELECT * FROM CARDBASE

-- Create CustomerBase Table
CREATE TABLE CUSTOMERBASE (Cust_id varchar(40),
						  Age int,
						  Customer_Segment varchar,
						  Customer_Vintage_Group varchar)
COPY CUSTOMERBASE FROM 'C:\Users\HR\Documents\data set\Fraud\CustomerBase.csv'
DELIMITER ',' CSV HEADER;
SELECT * FROM CUSTOMERBASE;

-- Create FraudBase Table
CREATE TABLE FRAUDBASE (Transaction_id varchar,
					   Fraud_flag int);
COPY FRAUDBASE FROM 'C:\Users\HR\Documents\data set\Fraud\FraudBase.csv'
DELIMITER ',' CSV HEADER;
SELECT * FROM FRAUDBASE;

-- Create TransactionBase Table
CREATE TABLE TRANSACTIONBASE (Transaction_id varchar,
							 Transaction_date date,
							 Credit_card_id varchar,
							 Transaction_value int,
							 Transaction_segment varchar);
COPY TRANSACTIONBASE FROM 'C:\Users\HR\Documents\data set\Fraud\TransactionBase.csv'
DELIMITER ',' CSV HEADER;
SELECT * FROM TRANSACTIONBASE;

/*  Get the list of transactions flagged as fraud together with the transaction value
in November.       */
SELECT FB.Transaction_id, TB.Transaction_value, TB.Transaction_Date
FROM FraudBase FB
INNER JOIN TransactionBase TB
ON FB.Transaction_id = TB.Transaction_id
WHERE FB.Fraud_flag = 1 AND TB.Transaction_Date BETWEEN '2016-11-01'  AND '2016-11-30' 
ORDER BY TB.Transaction_value DESC;

-- Get the list of all Credit Card id associated with fraud
SELECT DISTINCT TB.Credit_card_id 
FROM TransactionBase TB
INNER JOIN FraudBase FB
ON TB.Transaction_id = FB.Transaction_id
WHERE FB.Fraud_flag = 1;

-- What type of Card Family is associated with the most fraud
SELECT CB.Card_family, COUNT(CB.Card_family)
FROM CardBase CB
INNER JOIN TransactionBase TB
ON CB.Card_number = TB.Credit_card_id
INNER JOIN FraudBase FB
ON TB.Transaction_id = FB.Transaction_id
WHERE FB.Fraud_flag = 1
GROUP BY CB.Card_family;

-- What Top 5 Ages are associated with the most fraud
SELECT CSB.Age, COUNT(CSB.Age)
FROM CustomerBase CSB
INNER JOIN CardBase CB
ON CSB.Cust_id = CB.customer_id
INNER JOIN TransactionBase TB
ON CB.Card_number = TB.Credit_card_id
INNER JOIN FraudBase FB
ON TB.Transaction_id = FB.Transaction_id
WHERE FB.Fraud_flag = 1
GROUP BY CSB.age
LIMIT 5;


