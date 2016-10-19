# Quotes

- [Gather Quote Data](#gather-quote-data)
- [Gather Quote Fundamentals](#gather-quote-fundamentals)
- [Gather Historical Quote Data](#gather-historical-quote-data)

## Gather Quote Data

`GET /quotes/{symbol}/`

Retrieves current quote data for a single security; `symbol` can either be a ticker symbol or instrument. 

`GET /quotes/`

### Request Parameters

| Key         | Type      | Required? | Description |
|-------------|-----------|-----------|-------------|
| symbols     | String(s) | yes*      | Ticker symbol |
| instruments | URL(s)    | yes*      | URL of the instrument |

Retrieves information on one or more securities, separated by commas. Either symbols or instruments should be provided; If `instruments` is present, the information in that field will be displayed.

### Response Details

If a single instrument is entered using the first method, a `Quote` object is returned. No data is returned if the symbol is invalid.

If multiple symbols are entered and at least one symbol is valid, returns an array of objects; returns `null` for invalid entries. If all entries are invalid, this does not return any data.

| Key                             | Type       | Description |
|---------------------------------|------------|-------------|
| ask_price                       | Float      | |
| ask_size                        | Float      | |
| bid_price                       | Float      | |
| bid_size                        | Float      | |
| last_trade_price                | Float      | Price the security was last traded at |
| last_extended_hours_trade_price | Float      | |
| previous_close                  | Float      | |
| adjusted_previous_close         | Float      | |
| previous_close_date             | ISO 8601   | Date of the previous trading day for that security |
| symbol                          | String     | |
| trading_halted                  | bool       | Has trading been halted on that security? |
| last_trade_price_source         | string     | Generally `nls` (Nasdaq Last Sale) or `consolidated` |
| updated_at                      | ISO 8601   | Time the data was last updated |
| instrument                      | UUID       | Instrument UUID for this particular stock |

### Notes
- Ask/Bid prices are delayed by at least 15 minutes and should generally not be used to determine buy/sell ranges.
- Adjusted previous close does not display properly on dividend payout dates.

## Gather Quote Fundamentals

`GET /fundamantals/{symbol}/`

Retrieves fundamantals for a single security; `symbol` can either be a ticker symbol or instrument.

`GET /fundamantals/`

### Request Parameters

| Key         | Type      | Required? | Description |
|-------------|-----------|-----------|-------------|
| symbols     | String(s) | yes*      | Ticker symbol |
| instruments | URL(s)    | yes*      | URL of the instrument |

Retrieves fundamantals on one or more securities, separated by commas. Either symbols or instruments should be provided; if `instruments` is provided, the information in that parameter will be displayed.

### Response Details

Note that all fields can return a value of `null` if that data is not available.

| Key                  | Type       | Description |
|----------------------|------------|-------------|
| open                 | Float      | Opening price on the last trading day
| high                 | Float      | Highest price during the last trading day
| low                  | Float      | Lowest price during the last trading day
| volume               | Float      | Volume during the last trading day
| average_volume       | Float      | Average volume during the last 52 weeks
| high_52_weeks        | Float      | Highest price during the last 52 weeks
| dividend_yield       | Float      | Total amount paid in dividends during the last 52 weeks, relative to the price of the stock
| low_52_weeks         | Float      | Lowest price during the last 52 weeks
| market_cap           | Float      | Total market capitalization for that security
| pe_ratio             | Float      | Price/Earnings ratio
| description          | String     | Description of the security
| instrument                      | string     | Instrument for this particular stock |

### Notes
- Any of these keys can have a value of `null`; Your application should handle these values appropriately.
- Volume refers to the amount of trading activity picked up by Nasdaq Last Sale; it does not necessarily represent total trading activity on all exchanges.

## Gather Historical Quote Data

Allows you to retrieve historical quote data. It is generally used to display sparklines, though it has enough data to do much more than that.

`GET /quotes/historicals/{symbol}/`

Retrieves historicals quote data for a single security; `symbol` can either be a ticker symbol or instrument. `interval` and `span` are required; See below for details.

`GET /quotes/historicals/`

### Request Parameters

| Key         | Type      | Required? | Description |
|-------------|-----------|-----------|-------------|
| symbols     | String(s) | yes*      | Ticker symbol |
| instruments | URL(s)    | yes*      | URL of the instrument |
| interval    | String    | yes       | |
| span        | String    | no        | |
| extended    | bool      | no        | Displays extended hours |

Retrieves historical data on one or more securities, separated by commas. Either symbols or instruments should be provided; If both parameters are entered, only the information in the `instruments` parameter will be displayed.

If `extended` = true, this also retrieves data for extended hours (30 minutes before market open, 2 hours after close at the time this page was late updated); `span = day` is the only valid option in this case. This may not represent all after-hours trading activity.

The following `interval` / `span` combinations are permitted: 

| `interval`    | Valid `span` values   | Default     |
|---------------|-----------------------|-------------|
| `minute`      | `day`                 | `day`       |
| `5minute`     | `day`, `week`         | `day`       |
| `10minute`    | `day`, `week`         | `day`       |
| `day`         | `year`                | `year`      |
| `week`        | `5year`               | `5year`     |

Note that `day` will retrieve data starting from the opening of that trading day; It does not retrieve data from a 24 hour period. Data may be unavailable for delisted stocks.

`interval=minute` is no longer supported in API > 104.0.

### Response Details
If multiple securities are queried, they are contained within a results array.
		 
| Key                  | Type       | Description |
|----------------------|------------|-------------|
| quote                | URL        | URL containing the quote
| symbol               | String     | Ticker symbol
| interval             | String     | Interval used for the `historicals` array
| span                 | String     | What range to get data from
| bounds               | String     | `regular` or `trading`
| previous_close_price | Float      | Previous closing price; not present if unavailable
| open_price           | Float      | Value of the first `open_price` entry in `historicals` array
| open_time            | Float      | Value of the first `begins_at` entry in `historicals` array
| historicals          | Array      | |

The `historicals` array contains an array of objects with the following data:

| Key                  | Type       | Description |
|----------------------|------------|-------------|
| begins_at            | ISO 8601   | Date and time the interval begins at for the data in the array
| open_price           | Float      | Opening price during that interval
| close_price          | Float      | Closing price during that interval
| high_price           | Float      | Highest price during that interval
| low_price            | Float      | Lowest price during that interval
| volume               | Integer    | Amount of trading volume during that interval
| session              | String     | `reg`, `pre`, or `post`
| interpolated         | ISO 8601   | `true` if the interval has zero volume

### Notes

- Historical data prices are not adjusted for dividends. 

- This endpoint will always return `200 OK` if the server is up. If the historicals array is empty either the symbol is invalid or the historical data for this stock is temporarily unavailable. Your application should check if `symbol` is blank in the former case.

- Any stock value data before the instrument's list date is set to the value of the price when it first opened for trading.