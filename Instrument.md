# Instruments

The `/instruments` relative path is a directory that contains information about each instrument on Robinhood.

# Search for an Instrument

`api.robinhood.com/instruments/{instrument}/`

**Request Details**

| Key         | Type   | Required? | Description |
|-------------|--------|-----------|-------------|
| instrument  | Hash   | no        | Hash of the instrument |
| symbol      | String | no        | Search by ticker symbol. Case-sensitive |
| query       | String | no        | Search instruments based on a query. Max 32 characters |

** Request Example **
```
curl -v https://api.robinhood.com/instruments/66ec1551-e033-4f9a-a46f-2b73aa529977/ \
   -H "Accept: application/json" \
```

**Response**

If `instrument` is provided, will return a single object. If either `symbol` or `query` is provided, multiple instruments 
will be returned in an array format, to a maximum of ten.

| Key                  | Type       | Description |
|----------------------|------------|-------------|
| min_tick_size        | Float      | Minimum price increment for the stock. `null` if there isn't one |
| splits               | URL        | Endpoint containing split information on this instrument |
| margin_initial_ratio | Float      | |
| url                  | URL        | Endpoint for this instrument |
| quote                | URL        | Endpoint containing quote information of this instrument |
| symbol               | String     | Ticker symbol this instrument is trading as |
| bloomberg_unique     | String     | Bloomberg Unique ID |
| list_date            | ISO 8601   | Date the security was listed |
| fundamentals         | URL        | Endpoint containing fundamentals information of this instrument |
| state                | String     | `active` or `unlisted` |
| day_trade_ratio      | Float      | |
| tradeable            | bool       | Whether the stock is tradable on Robinhood at this time |
| maintenence_ratio    | Float      | |
| id                   | Hash       | Hash pointing to this instrument |
| market               | URL        | Endpoint containing market information this instrument is trading on |
| name                 | String     | Name of this instrument |

**Response sample**

```
{
	"min_tick_size": null,
	"splits": "https:\/\/api.robinhood.com\/instruments\/66ec1551-e033-4f9a-a46f-2b73aa529977\/splits\/",
	"margin_initial_ratio": "0.8000",
	"url": "https:\/\/api.robinhood.com\/instruments\/66ec1551-e033-4f9a-a46f-2b73aa529977\/",
	"quote": "https:\/\/api.robinhood.com\/quotes\/JNUG\/",
	"symbol": "JNUG",
	"bloomberg_unique": "EQ0000000031965598",
	"list_date": "2013-10-03",
	"fundamentals": "https:\/\/api.robinhood.com\/fundamentals\/JNUG\/",
	"state": "active",
	"day_trade_ratio": "0.7500",
	"tradeable": true,
	"maintenance_ratio": "0.7500",
	"id": "66ec1551-e033-4f9a-a46f-2b73aa529977",
	"market": "https:\/\/api.robinhood.com\/markets\/ARCX\/",
	"name": "Direxion Daily Junior Gold Miners Index Bull 3x Shares"
}
```

**Notes**

If none of the above fields are present, then it will return all instruments that are available in Robinhood, 
sorted by most recently modified. This list can include unlisted stocks. Up to 100 instruments can be returned in this manner.

When searching for a particular symbol, use `symbol` instead of `query`; the stock with that symbol is not guaranteed
to appear when using the latter. 

Unlisted stocks will not be displayed when searching by symbol or query.

The instrument name and/or name can update at any time, particularly if the stock is delisted or a corporate action occurs, so this data should be updated periodically.

`min_tick_size` affects what price increment that orders are quoted and accepted due to the stock's particpation in the Tick Size Pilot Program (TSPP). The stock may not always trade at that price increment, however, due to exemptions and/or being in the first test group.


# Gather Stock Split Data on an Instrument

| URI  | HTTP Method | Authentication |
|------|-------------|----------------|
| api.robinhood.com/instruments/{instrument}/splits/ | GET | No |

**Response**

Returns an array of objects representing stock split data. Stock splits before 2014 are not listed.

| Key                  | Type       | Description |
|----------------------|------------|-------------|
| url                  | URL        | Endpoint containing this particular stock split |
| instrument           | Hash       | The instrument this particular split occurred |
| execution_date       | ISO-8601   | Date the split occurred |
| divisor              | Float      | |
| multiplier           | Float      | |

** Request Example **
```
curl -v https://api.robinhood.com/instruments/66ec1551-e033-4f9a-a46f-2b73aa529977/splits/ \
   -H "Accept: application/json" \
```

**Response sample**

```
{
		"url": "https:\/\/api.robinhood.com\/instruments\/66ec1551-e033-4f9a-a46f-2b73aa529977\/splits\/c55aa028-cc1e-4f42-9b5d-5b91ebf5a247\/",
		"instrument": "https:\/\/api.robinhood.com\/instruments\/66ec1551-e033-4f9a-a46f-2b73aa529977\/",
		"execution_date": "2015-10-01",
		"divisor": "5.00000000",
		"multiplier": "1.00000000"
	},
{
		"url": "https:\/\/api.robinhood.com\/instruments\/66ec1551-e033-4f9a-a46f-2b73aa529977\/splits\/f0bea088-c7ad-4338-b5b4-99d810d73a23\/",
		"instrument": "https:\/\/api.robinhood.com\/instruments\/66ec1551-e033-4f9a-a46f-2b73aa529977\/",
		"execution_date": "2014-12-23",
		"divisor": "10.00000000",
		"multiplier": "1.00000000"
}
```