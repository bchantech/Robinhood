# Cards 

Retrieves a list of notification cards in your account (the one that appears on the front page).

This endpoint requires authentication.

## Get List of Cards

`GET /midlands/notifications/stack/`

`GET /midlands/notifications/stack/{id}`

### Response Details

| Key                 | Type      | Description |
|---------------------|-----------|-------------|
| type                | String    | Type of notification |
| title               | String    | Title of the card |
| message             | String    | Message on the card |
| call_to_action      | String    | If it contains a link, the text on it |
| action              | URL       | Link to action. Intralinks start with `robinhood://` |
| icon                | String    | The icon to display alongside the card |
| fixed               | Bool      | Will this be fixed on the screen? |
| time                | ISO 8601  | When the card was generated |
| relative_time       | String    | How long ago since the card was issued |
| show_if_unsupported | Bool      | |
| url                 | URL       | Endpoint for this specific card |

## Dismiss Card

`POST /midlands/notifications/stack/{id}/dismiss/`

Removes a card from your stack.