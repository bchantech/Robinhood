# Dividends 

Retrieves a list of announced and paid dividends into your acccount.

This endpoint requires authentication.

## Get Dividend Information

`GET /dividends/`

Get dividend information for all your accounts. Returns an array of dividend objects (see below)

`GET /dividends/{id}`

Get information for a particular dividend based on id.

### Response Details

| Key           | Type      | Description |
|---------------|-----------|-------------|
| account       | URL       | URL of the account with the dividend |
| url           | URL       | Endpoint containing this particular dividend |
| payable_date  | ISO 8601  | Date of payment for the dividend |
| instrument    | URL       | URL of the instrument with the dividend |
| rate          | Float     | Divident payment per share |
| record_date   | ISO 8601  | Date of record for the dividend |
| position      | Float     | Amount of shares owned at record_date |
| withholding   | Float     | |
| id            | Hash      | Dividend ID |
| paid_at       | ISO 8601  | Time the dividend was credited to account. `null` if not paid yet |
