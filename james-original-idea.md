WPA2 PSK is not very safe because many evil apps upload your PSK and share them with others (it is even possible on iOS). WPA2 Enterprise is relatively safer but you can't create a username/password for each guest. So I thought of a way to deploy WPA2 Enterprise without complex user database at home with a relatively low cost.

The main program will be a RADIUS server providing auth for the AP. An admin creates a username/key pair (e.g., 'guest' and a random key), and all users will do auth to the AP using that username ('guest') and the 6-digit TOTP code derived from that random key. Once auth is finished, the auth information (device NAS number aka MAC address, username, TOTP code at that time) will be kept in database. When next time that device connects, it would automatically login using cached credential, eliminating the need to input password again.

There are multiple pros for running this at home or small office:


* WPA2 Enterprise provides significantly better security than PSK or plain portal
* WiFi key share app typically do not support WPA2 Enterprise
* Low cost, you only need a RasPi and many home APs (even cheap TP-Link ones) that support RADIUS
* No manual user creation process for guests
* No shared accounts AND still able to log or invalidate any device
* You can use different usernames to separate devices into different VLANs if supported by AP
* Credential changes over time automatically, no need to rotate password manually, and connected devices will still work
* You can use a screen/pad to display guest credentials to guests, or even use a TOTP authenticator app on the phone


Still I think its security model needs careful evaluation since there are always bad boys scanning your WiFi trying to get free internet or even capture some data from it. 

James Swineson
2018-11-29
(from an email to a friend)
