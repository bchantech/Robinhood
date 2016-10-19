# Order

Allows you to order various securities.

All endpoints on this page requires authentication.

- [Place an Order](#place-an-order)
- [Gather Order Information](#gather-order-information)
- [Gather Recent Orders](#get-recent-orders)
- [Cancel an Order](#cancel-an-order)
- [Todo](#todo)

## Place an Order

`POST /orders`

Buy and sell shares of securities!

### Request Parameters


| Parameter     | Type   | Description                                         		          |Required|
|---------------|--------|--------------------------------------------------------------------|--------|
| account       | URL    | Account to make this order with      				              | yes    |
| instrument    | URL    | Instrument URL of the security you're attempting to buy or sell    | yes    |
| symbol        | String | The ticker symbol of the security you're attmepting to buy or sell | yes    |
| type 		    | String | Order type: `market` or `limit`                                    | yes    |
| time_in_force | String | `gfd`, `gtc`                                                       | yes    |
| trigger	    | String | `immediate`, `stop`, or `on_close`                                 | yes    |
| price		    | Float  | The price you're willing to accept in a sell or pay in a buy       | yes    |
| stop_price    | Float  | The price at which an order with a `stop` trigger converts         | Only when `trigger` equals `stop` |
| quantity      | Int    | Number of shares you would like to buy or sell                     | yes    |
| extended_hours  | Bool   | Place an extended-hours order | no |
| side          | String | `buy` or `sell`                                                    | yes    |
| client_id     | String | Only available for OAuth applications                              | no     |

#### Request Sample

```
curl -v https://api.robinhood.com/orders/ \
   -H "Accept: application/json" \
   -H "Authorization: Token a9a7007f890c790a30a0e0f0a7a07a0242354114" \
   -d account=https://api.robinhood.com/accounts/8UD09348/ \
   -d instrument=https://api.robinhood.com/instruments/50810c35-d215-4866-9758-0ada4ac79ffa/ \
   -d symbol=MSFT \
   -d type=market \
   -d time_in_force=fok \
   -d trigger=immediate \
   -d quantity=1 \
   -d side=sell
```

### Response Details

Upon a successful submitted order, the following information is returned:

| Key          		| Type     | Description |
|-------------------|----------|-------------|
| updated_at        | ISO 8601 | Date the order was last updated |
| ref_id            | Unknown  | |
| time_in_force     | Unknown  | `gfd` or `gtc` |
| fees              | Float    | Total fees for this trade. |
| cancel            | URL      | If this is not `null`, you can `POST` to this URL to cancel the order |
| id                | String   | Internal id of this order |
| cumulative_quantity | Float  | Number of shares which have executed so far |
| stop_price        | Float    | Price which a stop order activates. `null` if not applicable |
| reject_reason     | String   ||
| instrument        | URL      | URL of the instrument that is traded in this order
| state             | String   |  `queued`, `unconfirmed`, `confirmed`, `partially_filled`, `filled`, `rejected`, `canceled`, or `failed` |
| trigger           | String   | `immediate`, `stop`, or `on_close` |
| type              | String   | The order type |
| last_transaction_at | ISO 8601 | Last time a transaction took place (includes created, cancelled, or filled order) |
| price             | String   | The price specified for that order      
| executions        | Array    | This is a list of Execution objects, details listed below |
| extended_hours    | Bool     | Is this order active during extended hours? |
| account           | URL      | Endpoint for the account executing the order |
| url               | URL      | Endpoint for this specific order |
| created_at        | ISO 8601 | Time the order was placed |
| side              | String   | `buy` or `sell` |
| position          | URL      | Link to positions for this account with this instrument |
| average_price     | Float    | Average price of all shares executed so far; `null` for cancelled or rejected orders |
| quantity          | Float    | The quantity of shares bought/sold |

The exeuction array contains objects with the following information:

| Key          		| Type     | Description |
|-------------------|----------|-------------|
| timestamp         | ISO 8601 | The time this execution was made
| price             | Float    | The price this execution was made
| settlement_date   | ISO 8601 | Date this execution settles
| quantity          | Float    | The quantity of shares in this execution
| id                | String   | Internal id of this execution |

If there was a problem with submitting the order, specific details of this error will be in `detail`.

#### Response Sample

```
{
    "updated_at": "2016-04-01T21:24:13.698563Z",
    "executions": [],
    "time_in_force": "fok",
    "fees": "0.00",
    "cancel": "https://api.robinhood.com/orders/15390ade-face-caca-0987-9fdac5824701/cancel/",
    "id": "15390ade-face-caca-0987-9fdac5824701",
    "cumulative_quantity": "0.00000",
    "stop_price": null,
    "reject_reason": null,
    "instrument": "https://api.robinhood.com/instruments/50810c35-d215-4866-9758-0ada4ac79ffa/",
    "state": "queued",
    "trigger": "immediate",
    "type": "market",
    "last_transaction_at": "2016-04-01T23:34:54.237390Z",
    "price": null,
    "client_id": null,
    "account": "https://api.robinhood.com/accounts/8UD09348/",
    "url": "https://api.robinhood.com/orders/15390ade-face-caca-0987-9fdac5824701/",
    "created_at": "2016-04-01T22:12:14.890283Z",
    "side": "sell",
    "position": "https://api.robinhood.com/positions/8UD09348/50810c35-d215-4866-9758-0ada4ac79ffa/",
    "average_price": null,
    "quantity": "1.00000"
}
```

### Notes 
- When an order is placed and accepted by the RH backend, state will initially be `unconfirmed` until it is updated to another state.
- At this time, you will not be able to sell (or place sell orders on) more shares than you own. 
- `canceled` orders also refer to GTD orders which expired and possibly orders canceled after a split. 
- `ioc` and `fok` are valid choices but are not valid `time_in_force` parameters.
- `price` and `stop_price` must have no more than two decimal places of precision if the price is higher than 1, and
four otherwise. If you are using a market sell order, price can be `null`.
- The `updated_at` value also updates when order execution emails are sent as well; in some cases there
can be a substantial time difference between when the order was actually sent and when it tells you otherwise.

## Gather Order Information

`GET /orders/{order_id}`

### Gather Recent Orders

`GET /orders/`

This returns the most recent orders.

### Request Parameters

| Parameter       | Type     | Description                         		          |Required|
|-----------------|----------|----------------------------------------------------|--------|
| updated_at[gte] | ISO 8601 | Timestamp of earliest order we want information on | No     |
| instrument      | URL      | Returns orders that contain this instrument		  | No     |
| cursor          | String   | Orders are returned as a paginated list            | No     |

### Notes

A workaround for implementing updated_at is to specify the date in the cursor parameter.

Example: If we want orders after Jul 1st, we encode `p=2016-07-01&r=1` to base64 and pass the resulting value `cD0yMDE2LTA3LTAxJnI9MQ%3D%3D` for cursor

#### Request sample

```
curl -v https://api.robinhood.com/orders/ \
   -H "Accept: application/json" \
   -H "Authorization: Token a9a7007f890c790a30a0e0f0a7a07a0242354114"
```

### Response Details


## Cancel an Order

`POST /orders/{order_id}/cancel/`

Allows you to cancel an order.


**Request sample**

```
curl -v https://api.robinhood.com/orders/15390ade-face-caca-0987-9fdac5824701/cancel/ \
   -H "Accept: application/json" \
   -H "Authorization: Token a9a7007f890c790a30a0e0f0a7a07a0242354114"
   -d ""
```

**Response**

See the response to [placing an order](#place-an-order).

**Response sample**

See the response sample to [placing an order](#place-an-order).

