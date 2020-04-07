
# eToroX API

eToroX API allows customers to trade on the eToroX exchange using a REST API solution, which allows algo traders and programmers to electronically send or cancel orders to buy/sell cryptoassets, and retrieve account information.

eToroX manages all orders per market. The system saves  customer orders in several persistence (permanent) storage, as well as in the matching engine. The matching engine compares buy/sell orders and if a match is found (a buy order price that is equal to or higher than the sell order price) the orders are matched and executed within the stated price timeframe.

## Support
For any issues with API integration, please contact us at customerservice@etorox.com    

## API Features
* Get balance
* Manage orders
* Withdrawal funds
* Deposit funds
* Stream full orders book and trades
* Stream current user trading feed

## Matching Engine


### Order life cycle
Orders are accepted by the system after several validations, which include customer token verification and a balance check.

A new order being accepted generates a unique ID, the requester of the order receives the order’s unique ID. The order’s state is now `pending`.

The new order is then saved  in persistent storage where its status changes to `open`.
The order is transferred immediately to the matching engine and if it matches another order, is executed without delay. The requester receives a confirmation message upon successful completion of this operation and the order state changes to `executed`. 

If the match happens, the matching engine updates the database and creates a trade/exchange of funds between the buyer and the seller. The relevant customers will receive a confirmation message upon execution. 

If this is only a partial execution, the order state will continue to be `open` but the available volume (base currency amount) will decrease. The partially executed amount is saved in dedicated storage area.
Cancel order requests are processed immediately after the validation that an order exists and is deleted from the matching engine. The requester will receive a confirmation message after process completion. The order state then changes to `cancelled`.

## Data centers
eToroX trading system front servers are located on the GCP in western Europe.

## Authentication
eToroX values security very highly. All authenticated API end-points are protected with a specific signature process that ensures maximum security
Read more about the authentication process in the section [eToroX authentication](authentication).








