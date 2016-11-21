#Sample Solr ReST Queries

The supplied Express-based Solr ReST interface provides a number of representative queries against the transaction table and roll-up/aggregate data tables.

Use the following URL's (substituting the external address of your server and the port number you used when you started the Epress Server) to run the queries shown. 

The results will be returned in JSON format suitable for consumption by an external system.

##CQL Queries On The Transactions Table

###1. List all the card transactions across all cards and vendors
List all the card transactions across all cards and vendors in the TRANSACTIONS table:
http://[server_IP:Express_port]/getalltransactions
```
SELECT * FROM rtfap.transactions;
```

##Solr Queries On The Transactions Table

###2. List all transactions over a specified amount
Retrieve data for all transactions in the TRANSACTIONS table where the amount is greater than a specified value
http://[server_IP:Express_port]/getalltransactionsbyamount/1000
```
SELECT * FROM rtfap.transactions WHERE solr_query = '{"q":"*:*",  "fq":"amount:[1000 TO *]"}';
```

###3. List all rejected transactions
Retrieve all transactions in the TRANSACTIONS table where status="Rejected"
http://[server_IP:Express_port]/getallrejectedtransactions
```
SELECT * FROM rtfap.transactions WHERE solr_query='{"q":"status: Rejected"}';
```

###4. List all transactions faceted by merchant
Retrieve all transactions in the TRANSACTIONS table, faceted by merchant
http://[server_IP:Express_port]/getfacetedtransactionsbymerchant
```
SELECT * FROM rtfap.transactions WHERE solr_query='{"q":"*:*", "facet":{"field":"merchant"}}';
```

###5. List all transaction success ratio (faceted by status) in the last period e.g. minute
Retrieve all transactions in the TRANSACTIONS table, faceted by status, over the last year/month/minute
http://[server_IP:Express_port]/getfacetedtransactionsbystatusinlastperiod/MINUTE
```
SELECT * FROM rtfap.transactions WHERE solr_query = '{"q":"*:*",  "fq":"txn_time:[NOW-1" + lastPeriod + " TO *]","facet":{"field":"status"}}';
```

###6. List all transaction success ratio (faceted by status) for a specified card in the last period e.g. year
Retrieve all transactions in the TRANSACTIONS table, faceted by status, for the specified card number and period
http://[server_IP:Express_port]/getfacetedtransactionsbyccnoandstatusinlastperiod/123412*/YEAR
```
SELECT * FROM rtfap.transactions WHERE solr_query = '{"q":"cc_no:123412*",  "fq":"txn_time:[NOW-1MINUTE TO *]","facet":{"field":"status"}}';
```

###7. List all transactions for a specific card
Retrieve all transactions in the TRANSACTIONS table for a specified card number (optional wild card)
http://[server_IP:Express_port]/getalltransactionsbyccno/123412*
```
SELECT * FROM rtfap.transactions WHERE solr_query='{"q":"cc_no:123412*"}';
```

###8. List all fraudulent transactions for a specific card
Retrieve all transactions in the TRANSACTIONS table tagged as "Fraudulent" for a specified card number
http://[server_IP:Express_port]/getallfraudulenttransactionsbyccno/123412*
```
SELECT * FROM rtfap.transactions WHERE solr_query='{"q":"cc_no:123412*", "fq":["tags:Fraudulent"]}';
```

###9. List all fraudulent transactions for a specified period-to-date
9.a Retrieve data for all transactions in the TRANSACTIONS table tagged as "Fraudulent" over the last year
http://[server_IP:Express_port]/getallfraudulenttransactionsinlastperiod/YEAR
```
SELECT * FROM rtfap.transactions WHERE solr_query = '{"q":"*:*", "fq":["txn_time:[NOW-1YEAR TO *]", "tags:Fraudulent"]}';
```

9.b Retrieve data for all transactions in the TRANSACTIONS table tagged as "Fraudulent" over the last month
http://[server_IP:Express_port]/getallfraudulenttransactionsinlastperiod/MONTH
```
SELECT * FROM rtfap.transactions WHERE solr_query = '{"q":"*:*", "fq":["txn_time:[NOW-1MONTH TO *]", "tags:Fraudulent"]}';
```

9.c Retrieve data for all transactions in the TRANSACTIONS table tagged as "Fraudulent" over the last day
http://[server_IP:Express_port]/getallfraudulenttransactionsinlastperiod/DAY
SELECT * FROM rtfap.transactions WHERE solr_query = '{"q":"*:*", "fq":["txn_time:[NOW-7DAY TO *]", "tags:Fraudulent"]}';

##CQL Queries On The Roll-Up Tables

###10. List all transactions for a merchant on a specified day
Retrieve data for all transactions for the specified day in the DAILYTXNS_BYMERCHANT rollup table where the merchant = "GAP"
http://[server_IP:Express_port]/getdailytransactionsbymerchant/GAP/20160309
SELECT * FROM rtfap.dailytxns_bymerchant WHERE merchant='GAP' AND day=20160309;

###11. List aggregated purchase history for a specific card and year
Retrieve data for all transactions for the specified year in the YEARLYAGGREGATES_BYCC rollup table where the card number = "1234123412341235"
http://[server_IP:Express_port]/getyearlytransactionsbyccno/1234123412341235/2016
```
SELECT * FROM rtfap.yearlyaggregates_bycc WHERE cc_no='1234123412341235' AND year=2016;
```

###12. Rolling transaction success ratio and count, by minute and hour
Retrieve a rolling ratio of successful transactions and transaction count over the previous minute and hour
http://[server_IP:port]/getTransactionsApprovalByDate/201603270521
```
SELECT approved_rate_hr, approved_txn_hr, approved_rate_min, approved_txn_min FROM rtfap.txn_count_min WHERE year=2016 AND month=3 AND day=27 AND hour=5 AND minute=22;
```
