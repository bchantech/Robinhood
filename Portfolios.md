# Portfolios

This allows you to get information on your portfolio.

## Gather List of Portfolios
`GET api.robinhood.com/portfolios/{account}/`

Allows you to get portfolio data for all your accounts. If you specify an account number it will return that one only.

**Request sample**

```
curl -v https://api.robinhood.com/portfolios/ \
   -H "Accept: application/json" \
   -H "Authorization: Token a9a7007f890c790a30a0e0f0a7a07a0242354114"
```

**Response**

A [paginated](#pagination) list of accounts is returned. Accounts contain the following keys...

| Key                           | Type     | Description |
|-------------------------------|----------|-------------|
| account                        | URL      | URL for this account |
| url                            | URL      | Same as `account` |
| excess_maintenance             | Float    | Excess maintainence in your accout |
| excess_maintenance_with_uncleared_deposits     | Float    | Excess maintainence in your accout, counting uncleared deposits |
| market_value                   | Float    | Value of all stocks in portfolio |
| withdrawable_amount            | Float    | How much that can be withdrawn at this time |
| last_core_market_value         | Float    | Market value at previous close |
| unwithdrawable_deposits        | Float    | Amount of money you cannot withdraw at this time |
| extended_hours_equity          | Float    | Equity in your account factoring in extended hours. `null` if currently during normal trading hours |
| excess_margin                  | Float    | Excess margin in your account. |
| excess_margin_with_uncleared_deposits     | Excess margin in your account, counting uncleared deposits |
| equity                          | Float    | Equity in your account at this time. |
| last_core_equity                | Float    | |
| equity_previous_close           | Float    | Equity in your account at previous close. |
| start_date                      | Date     | Date this account was opened |
| extended_hours_market_value     | Float    | Market value of the account factoring in extended hours. `null` if currently during normal trading hours  |

## Gather Historical Data
`GET api.robinhood.com/portfolios/historicals/{account}/`

Allows you to get historical data for both the market value and equity in your account at a certain point in time. Inputs are the same as those of `quotes/historicals/`, with the exception that `minute` is not allowed for interval, and you can't use 5 minute data for week. Some information here such as market values can differ slightly between this and the above endpoint.

This endpoint is known to be throttled.

**Response**

Returns an object consisting of `interval`, `span`, and `total_return`, and a `equity_historicals` array of objects containing the following data:

| Key                           | Type    |
|-------------------------------|---------|
| adjusted_close_equity | Float |
| begins_at |  Date |
| open_market_value |  Float |
| session |  String |
| adjusted_open_equity |  Float |
| close_market_value |  Float |
| net_return |  Float |
| open_equity |  Float |
| close_equity |  Float |
