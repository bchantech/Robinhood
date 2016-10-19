# Position

## Retrieve All Positions

Displays all of your positions on RH.

`GET /positions/`

### Request Parameters

| Parameter     | Type   | Description                                         		          |Required?|
|---------------|--------|--------------------------------------------------------------------|---------|
| nonzero       | Bool   | If `true`, displays all positions that are not closed (have zero shares in)  | no     |

### Response Details

Returns an array of the following values:

| Key                  | Type      | Description |
|----------------------|-----------|-------------|
| account              | String    | |
| intraday_quantity    | String    | |
| share_held_for_sells | Float     | |
| url                  | URL       | |
| created_at           | ISO 8601  | |
| updated_at           | ISO 8601  | |
| shares_held_for_buys | Bool      | |
| average_buy_price    | Float     | |
| instrument           | URL       | |
| quantity             | Float     | |

## Retrieve Positions on a Specific Account

`GET /accounts/{account}/positions/`

Displays all of your positions for a specific account.

This endpoint requires authentication.

### Request Parameters

| Parameter     | Type   | Description                                         		          |Required?|
|---------------|--------|--------------------------------------------------------------------|---------|
| nonzero       | Bool   | If `true`, displays all positions that are not closed              | no      |

### Response Details

See "Retrieve All Positions" -> Response Details.

## Retrieve position On A Specific Security.

`GET /accounts/{account}/positions/{instrument_id}`

Displays details on your position for a specific security (use instrument ID).

### Response Details

See "Retrieve All Positions" -> Response Details.

If you have never bought (or placeed an order for) that security in that account, returns this instead:

| Key                  | Type      | Description |
|----------------------|-----------|-------------|
| detail               | String    | `Not found.`|


### Notes

`updated_at` reflects the last time when shares are bought and/or sold and does not update when your position is updated due to a stock split (or reverse split). It is unknown whether this value updates when shares are added or removed via ACAT and/or corporate actions.
