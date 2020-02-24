# ```captive.d```
```captive.d``` is a collection of scripts to log in to captive portals automatically.

Feel free to PR additional scripts.

## Integration in dispatcher.d (example)
```
> cat /etc/NetworkManager/dispatcher.d/02-captive
#!/bin/bash
INTERFACE=$1
STATE=$2
if [[ $INTERFACE = 'wlp3s0' &&  $STATE = 'up' ]]; then
	PORTALCHECK=`curl http://portalcheck.yellowant.de`
	if [[ "$PORTALCHECK" != "success" ]]; then
		echo "Captive Portal detected!"
		SSID=`nmcli -t -f active,ssid dev wifi | egrep '^yes' | cut -d: -f2`
		echo "SSID: $SSID"
		if [[ -f "/etc/NetworkManager/dispatcher.d/captive.d/${SSID}" ]]; then
			if [[ "$SSID" =~ [a-zA-Z0-9\!-:space:]* ]]; then
				echo "Running command \"/etc/NetworkManager/dispatcher.d/captive.d/$SSID\""
				. "/etc/NetworkManager/dispatcher.d/captive.d/$SSID"
              			  # Optional: connect to VPN
              			  # nmcli connection up vpn
			else
				echo "Invalid SSID!"
			fi
		else
			echo "No profile found."
		fi
	fi
fi
```
