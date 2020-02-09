# ```captive.d```
```captive.d``` is a collection of scripts to log in to captive portals automatically.

Feel free to PR additional scripts.

## Dependencies

Some scripts are implemented using `bash` and `curl`. Others require more complex oprtations and are implememted in `python3` using `requests` and `BeautifulSoup 4` (`bs4`).

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
		if [[ "$SSID" =~ [a-zA-Z0-9\!-]* ]] && [[ -f "/etc/NetworkManager/dispatcher.d/captive.d/$SSID" ]]; then
			/etc/NetworkManager/dispatcher.d/captive.d/$SSID
            # Optional: connect to VPN
            # nmcli connection up vpn
		fi
	fi
fi
```
