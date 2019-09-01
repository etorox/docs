
# eToroX API

eToroX API allows customers to trade on the eToroX exchange via a REST API. The solution enables algo traders and programmers to send/cancel orders to buy/sell crypto assets and to retrieve account info, electronically. 

eToroX manage all orders per market (price of 1 asset in second asset units). The system save the customers orders in several persistence storages as well as in the matching engine. The matching engine compare buy/sell orders and if match found (price of buy equal or higher than the price of sell order) the  orders are matched/executed and the exchange balances between the buyer and the seller happen. 
The matching is being done using the price time provided.

## API Features
* Get balance
* Manage orders
* Withdrawal funds
* Deposit funds
* Stream full orderbook
* Stream current user trading feed

## Matching enginge


### Order life cycle
Orders accepted by the system immediately after several validations. The validations include customer token verification and balance check. The acceptance of the new order generate a unique Id ant the requested get it as a response. The order state will be `pending`. 
The new order is being saved in persistent storage as well. And its status will be changed to `open`. 

The Order is transferred immediately to the matching engine and in case of match, executed without being placed in the engine storage. The requested will get a confirmation message upon successful completion of this operation and the order state will be changed to `executed`. 
In case of match event, the matching engine update the database and will create a trade/exchange of funds between the buyer and the seller.
The relevant customers will get a confirmation message upon execution.
In case of partial execution, the order state will continue to be `open` but available volume (amount) will be decreased.  The partial executed amount will be saved in a dedicated storage and will be responded on the relevant REST API call. 

Cancel order requests will be processed immediately after the validation of order existence and will be deleted from the  matching engine. The request will get a confirmation message after process completion. The order state will change to  `cancelled` in such case.

## Data centers
eToroX trading system front servers are located on GCP in region west europe. 

## Authentication
eToroX values security, all the authenticated API end-points are protect with a specific signature process that insures maximum security 

Read more about the authentication process:
[eToroX authentication](authentication)






