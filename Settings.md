# Settings

Push notifications are amazing. ...I just haven't figured them out yet. This determines what notifications you will receive, as well as which mobile devices will receive push notifications.

## Get Notification Settings

`GET /settings/notifications/`

Aloows you to retrieve a list of settings in your account.

## Put Notification Settings

`PUT /settings/notifications/`
`PATCH /settings/notifications/`

Allows you to change one or more setting in your account (such as `trading_email=true`). If you are doing a PUT action,
any setting you don't specify is false by default.

Valid parameters:
- market_push
- market_email
- trading_push
- trading_email
- compliance_push
- compliance_email
- margin_push
- market_email
- transfers_push
_ transfers_email

		
## Add Device

`POST /notifications/devices/`

Allows you to add a device to subscribe to push notifications. Generally this endpoint is called when you login using a new device.

### Request Parameters

| Key         | Type      | Description |
|-------------|-----------|-------------|
| token     | String     | The device token that will receive push notifications. |
| identifier	  | Hash | The device id that will receive these notifications. |
| type		  | Hash | `android` or `ios` |

					
## Delete device

`DELETE /notifications/devices/{deviceId}/`

Allows you to remove a device from push notifications. Generally this endpoint is called when you log out of your account from a device.
					
## Get Devices

`GET /notifications/devices/`

Allows you to see which devices are receiving push notifications. 