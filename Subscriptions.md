# Subscriptions

Allows you to view your Robinhood Gold status, or if you are eligible, to upgrade to one.  

## View your margin upgrades

`GET /margin/upgrades/`

Allows you to see what dates and times you got particular margin upgrades.

## Upgrade An Account to Robinhood Instant

`POST /margin/upgrades/`

Should you not be able to do so in the app for some reason, you can upgrade your account to Instant here. Doing this assumes you read the margin agreement.

### Request Parameters

| Key         | Type      | Description |
|-------------|-----------|-------------|
| account     | URL     | URL of the account to apply the changes to |

## Get Robinhood Gold Eligible Tiers

`GET /subscription/plans/`

Retrieves your current instant deposit limit, as well as various Robinhood Gold plans you are eligible for. 

### Response Data

| Key         | Type      | Description |
|-------------|-----------|-------------|
| symbols     | Float     | Your current instant deposit limit |
| plans		  | Array     | URL of the instrument |

Plans Array
| Key         | Type      | Description |
|-------------|-----------|-------------|
| is_default | Bool | What tier is visible to you when you choose a plan |
| first_fee | Float | How much money you pay when signing up. `null` for none
| first_fee_date | Date | What day is your first payment due? `null` for N/A 
| margin_limit_withheld_due_to_volatility | Float | How much margin is withheld from Gold
| new_buying_power | Float | What is your new buying power 
| subscription_margin_limit | Float | What is your margin limit raised to?
| instant_deposit_limit | Float | When you deposit, how much is immediately made available to you?
| margin_interest | Float | How much percent you owe in interest when borrowing money. `null` for zero.
| monthly_cost | Float | How much it costs monthly to maintain this status
| free_trial_days | Integer | How many days you get free. This is only for display, however.
| id | Hash | Hash of the selected plan

#### Notes
- `margin_limit_withheld_due_to_volatility` is acutally margin limit withheld for any reason; it doesn't 
necessarily have to be due to volatility. 

## View your subscriptions

`GET /subscription/subscriptions/[?active=true]`

Allows you to view your all your subscription(s) in the history of your account.


### Response Data

| Key         | Type      | Description |
|-------------|-----------|-------------|
| account | URL
| ended_at | Date
| url | URL
| created at / Date
| credit / double / Credit obtained from a previous subscription
| unsubscribe
| plan / Endpoint / contains margin interest, monthly cost, margin limit, id, instant deposit limit
| renewal_date
| id



## Remove a subscription

`POST /subscription/subscriptions/{subscription_id}/unsubscribe`

Allows you to cancel a particular subscription.

## View your subscription feees

`GET /subscription/subscription_fees/`

### Response Data

| Key         | Type      | Description |
|-------------|-----------|-------------|
| id		  | id     | id of the specific fee |
| amount      | double | amount due for the subscription |
| credit      | double | amount credited from a previous subscription or some other source |
| date		  | DateTime | Date the subscription fees are due |

Allows you to view your current subscription fees. 

## Change your subscription level

`POST /subscription/subscriptions/`

Immediately send a request to subscribe to this tier. If the subscription request is successful, any
remaining days in your existing plan (if applicable) will be credited towards your new subscription. 

By using this command, you automatically consent to the terms of the Robinhood Gold Agreement, and your
account may undergo manual review for eligibility before the change is made.

Subscription ID's are hardcoded and shared amongst all users; it is possible to subscribe to one you're not
eligible for, however you won't get additional benefit for subscribing above your tier.

### Request Parameters

| Key         | Type      | Description |
|-------------|-----------|-------------|
| account     | URL     | URL of the account to apply the charges to |
| plan_id		  | Hash | Hash containing the plan id. |
