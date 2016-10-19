# Bank Accounts

Allows you to view your transfers and fund your account from a variety of sources. Official APIs only support reading transfer history and the last four digits of the back account number associated with it.

All endpoints on this page require authentication.

## View Transfers

`GET /ach/transfers/`
`GET /wire/transfers/`

Allows you to view all your transfers to and from your accounts.

## Transfer Money (ACH)

`POST /ach/transfers/`

Allows you to add or withdraw cash from Robinhood.

### Request Parameters

| Parameter        | Type   | Description                                 |
|------------------|--------|---------------------------------------------|
| ach_relationship | URL    | URL containing the ACH account to transfer  |
| direction		   | String | `deposit` or `withdraw`                     |
| amount		   | Float  | Amount to deposit or withdraw               |

### Response Details

If successful, returns the following values:

| Key                   | Type      | Description |
|-----------------------|-----------|-------------|
| scheduled				| Bool		| Was the deposit a scheduled one?
| direction				| String 	| `deposit` or `withdraw`
| url					| URL		| Endpoint containing this specific transfer
| created_at			| ISO 8601  | Date the transfer was created
| state					| String	| Status of the transfer. Valid values: `new`, `pending`, `canceled`, `failed`, or `completed`
| updated_at			| ISO 8601  | Date the transfer was updated
| amount				| Float     | Amount of the transfer
| early_access_amount	| Float     | Amount of money you can access early
| fees					| Float     | Amount of fees paid for this transfer
| cancel				| URL       | URL to cancel this account. `null` if it cannot be cancelled
| ach_relationship		| URL       | Endpoint containing the ACH Relationship for
| expected_landing_date	| ISO 8601  | Date the transfer is expected to complete.
| status_description	| String    | Additional information
| id					| Hash      | Unique ID for this transfer
 
### Notes
- Upon submission, state will be `new`, early_access_amount will be `0.00`, and cancel will be `null`. They will update after the initial submission.

## (Wire) Transfer Money

`POST /wire/transfers/`

Allows you to withdraw cash from Robinhood via a Wire Transfer. Keep in mind wire transfers are not free and you will be charged when initating one. 

Wire Transfers into the account needs to be done from outside the Robinhood App.

### Request Parameters

| Parameter        | Type   | Description                                 |
|------------------|--------|---------------------------------------------|
| wire_relationship | URL    | URL containing the Wire account to transfer  |
| direction		   | String | `withdraw`                     |
| amount		   | Float  | Amount to deposit or withdraw               |

### Response Details

Untested

## View Transfer Details

`GET /ach/transfers/{transfer_id}/`

Allows you to view details on a single transfer.

## Cancel Transfer

`POST /ach/transfers/{transfer_id}/cancel/`

Allows you to cancel a pending transfer.

## View ACH Relationships

`GET /ach/relationships/`

Allows you to view details on all your ACH Relationships (linked bank accounts).

## View ACH Relationship

`GET /ach/relationships/{relationship_id}/`

Allows you to view details on a single ACH Relationship.

### Response Details

| Key                  | Type      | Description |
|----------------------|-----------|-------------|
| account					| URL	    | The account on Robinhood the bank account is linked to
| verification method		| string    | The method used to verify the account. Can be `bank_auth` (Instant verification) or micro deposit.
| verify_micro_deposits		| bool	    | Whether micro deposits were verified. `null` if another verification method was used 
| url						| URL	    | Endpoint for this ACH relationship
| bank_account_nickname		| string    | How it appears in Robinhood (or not) when choosing an account to withdraw and deposit
| created_at				| ISO 8601  | When was this connection created
| bank_account_holder_name 	| string    | Who owns this bank account?
| bank_account_number	 	| integer   | The last four digits of your account number
| bank_account_type		 	| string    | `checking` or `savings`
| unlinked_at				| ISO 8601  | Date when this bank account was unlinked, `null` if it wasn't
| initial_desposit			| Unknown   | Could be float or date, need further reserach | 
| verified					| Bool	    | Was this bank account verified?
| unlink					| URL	    | URL to unlink this acccount
| bank_routing_number		| String	| Routing number for this account
| id						| Hash		| Unique id for this ACH Relationship

## Unlink ACH Relationship

`POST /ach/relationships/{relationship_id}/unlink`

# TODO

- Create Sweep Account			PUT  /user/additional_info/ 		{sweep_consent}
- Get Auto Deposit				GET /ach/deposit_schedules/$automaticDepositId/
- Get Auto Deposits				GET /ach/deposit_schedules/
- Create Auto Deposits				GET /ach/deposit_schedules/			{ach_relationship, amount, frequency (weekly, biweekly, monthly, quarterly)}
- Verify Microdeposits			POST /ach/relationships/$id/micro_deposits/veryify/	{first_amount_cents, second_amount_cents}
- Get Queued ACH Deposit		GET /ach/iav/queued_deposit/
- IAV AuthMfaAnswer				POST /ach/iav/auth/mfa/					{bank_institution, access_token, mfa}
- IAV Auth Request				POST /ach/iav/auth/						{bank_institution, username, password, pin}
- Post Queued ACH Deposit		POST /ach/iav/queued_deposit/
- Delete Auto Deposit			DELETE /ach/deposit_schedules/$autoDepositId/
- Add ACH Bank account			POST /ach/relationships/			{account ($account_id)
																	 bank_routing_number
																	 bank_account_number
																	 bank_account_type ('checking' or 'savings')
																	 bank_account_holder_name
																	 bank_account_nickname
																	}
- Add ACH with Instant Acct Verification	POST /ach/iav/create/				{access_token
																	 iav_account_id
																	 account ($account_id)
																	 bank_account_type ('checking' or 'savings')
																	 bank_account_holder_name
																	}
- Add Wire Account   			POST /ach/relationships/			{account ($account_id)
																	 beneficiary_bank
																	 beneficiary_account_number
																	 nickname
																	}