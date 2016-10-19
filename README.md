# Unofficial Documentation of Robinhood Trading API

Table of Contents:

- [Introduction](#introduction)
- [Table of Contents](#table-of-contents)
- [API Security](#api-security)
- [API Error Reporting](#api-error-reporting)
- [Pagination](#pagination)
	- [Semi-Pagination](#semi-pagination)

## See also

- [Authentication.md](Authentication.md) for methods related to user authentication, password recovery, etc.
- [Banking.md](Banking.md) for bank accounts & ACH transfers methods
- [Order.md](Order.md) for order-related functions (placing, cancelling, listing previous orders, etc.)
- [Quote.md](Quote.md) for stock quotes
- [Watchlist.md](Watchlist.md) for watchlists management
- [Account.md](Account.md) talks about gathering and modifying user and account information
- [Settings.md](Settings.md) covers notifications and other settings
- [Markets.md](Markets.md) describes the API for getting basic info about specific exchanges themselves
- [Referrals.md](Referrals.md) is all about account referrals

Things I have yet to organize are in [Unsorted.md](Unsorted.md)

# Introduction

[Robinhood](http://robinhood.com/) is a free, online securities brokerage offering trading with zero commissions. As you would expect, being an online service means everything is handled through a request that is made to a specific URL.

This is considered to be unofficial documentation; although attempts will be made to provide accurate information, there is no guarantee that any of the endpoints listed in this document will work under the current version of the API. 

I do not work with or for Robinhood Financial, LLC.

# Authentication

Authentication is required to access most endpoints on the API. It can be done through two methods:

- **Token**: Authenticate through a token generated with a call to [log in](Authentication.md#log-in).
- **Oauth2**: Authenticate through an app (an app can automatically read and execute actions on your behalf).

Authorized calls require an `Authorization` HTTP Header with the authentication type set as `Token` (Example: `Authorization: Token 40charauthozationtokenherexxxxxxxxxxxxxx`).

Generally, you should use your personal API token to access your account and/or for developing personal apps, as it has unrestricted access to all information in your account. You'll want to use Oauth2 if you're building a consumer app, or if you are using another integration, as the user can securely login to the app using Robinhood, and can limit what actions can be done within the app.

SSL Pinning in used in the official Android and iOS apps to prevent MITM attacks.

# Versioning

The API version that is being used to return the data is indicated by the `X-Robinhood-API-Version` header (with the exception of the `quotes` and `fundamentals` endpoints).

There is currently no known interface to retrieve a particular version of the API, and endpoints may change at any time.

# API Error Reporting

The API reports incorrect data or improper use with HTTP staus codes and JSON objects returned as body content. More than one error can be thrown in a response. Some endpoints do not return errors.

Currently, no machine-readable error codes are returned. Generally, if an error exists in a field it will be under the parameter, or under `non_field_errors` if the error isn't in a specific parameter. If there is an error processing the request, specific details will be under `detail`. 

Examples of errors include:

| HTTP Status | Key                | Value | What I Did Wrong |
|-------------|--------------------|-------|------------------|
| 400         | `non_field_errors` | `["Unable to log in with provided credentials."]` | Attempted to [log in](#logging-in) with incorrect username/password |
| 400         | `password`         | `["This field may not be blank."]`                | Attempted to [log in](#logging-in) without a password |
| 401         | `detail`           | `["Invalid token."]`                              | Attempted to use cached token after [logging out](#logging-out) |
| 400         | `password`          | `["This password is too short. It must contain at least 10 characters.", "This password is too common."]`                                    | Attempted to [change my password](#password-reset) to `password` |
| 400         | `{value}`           | `[" \\\'value'\\\ is not a valid choice."]` | TBA
| 400         | `{value}`           | `["A valid number is required."]` | TBA
| 400         | `{value}`           | `["This field is required."]` | Not including a required field on a GET or POST request
| 400         | `{value}`           | `["Invalid hyperlink - No URL match."]` | entering a invalid site for a value that expects a URL
| 404         | `detail`            | `["Not found."]` | Resource does not exist.
| 429         | `detail`            | `"Request was throttled."` | Empty POST to `/watchlists/`
| 500         | N/A                 | Server Error | Passing in a malformed cursor that is parsable
| 501         | N/A                 | Server Error | Nothing

# Rate Limits

In the event you are calling a particular endpoint too many times, or you are accessing a locked-down endpoint (such as POST to `/watchlists/` to create a new watchlist) the call will return a 429 error. Rate limits are not specified per endpoint.

# Pagination

Some data is returned from the Robinhood API as paginated data with `next` and `previous` cursors already in URL form.

If your call returns paginated data, it will look like this call to `https://api.robinhood.com/instruments/`:

```
{
    "previous": null,
    "results": [{
        "splits" : "https://api.robinhood.com/instruments/42e07e3a-ca7a-4abc-8c23-de49cb657c62/splits/",
        "margin_initial_ratio" : "1.0000",
        "url" : "https://api.robinhood.com/instruments/42e07e3a-ca7a-4abc-8c23-de49cb657c62/",
        "quote" : "https://api.robinhood.com/quotes/SBPH/",
        "symbol" : "SBPH",
        "bloomberg_unique" : "EQ0000000028928752",
        "list_date" : null,
        "fundamentals" : "https://api.robinhood.com/fundamentals/SBPH/",
        "state" : "active",
        "tradeable" : true,
        "maintenance_ratio" : "1.0000",
        "id" : "42e07e3a-ca7a-4abc-8c23-de49cb657c62",
        "market" : "https://api.robinhood.com/markets/XNAS/",
        "name" : "Spring Bank Pharmaceuticals, Inc. Common Stock"
    },
        ...
    ],
    "next": "https://api.robinhood.com/instruments/?cursor=cD04NjUz"
}
```

To get the next page of results, just use the `next` URL.

## Semi-Pagination

Some data is returned as a list of `results` as if they were paginated but `previous` and `next` keys will be null since they cannot be traversed using cursor-based pagination.
