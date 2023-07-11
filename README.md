# xray-configs
My xray configuration files.

# Server configuration
Install `acme.sh` and `nginx`. Relevant guides can be found online.

Create folders `certs` and `logs` under `/home/xray`.
```zsh
mkdir certs logs
```

Under `/var/www/html` prepare an `index.html` to use as decoy. It can be any static page of your choice.
```zsh
echo "Testing webpage" > /var/www/html/index.html
```

Set `CF_Token` environment variable.
```zsh
export CF_Token="my_cf_token"
```

Get certificates.
```zsh
acme.sh --issue -d '*.example.com' --keylength ec-256 --force --dns dns_cf --ocsp-must-staple
/home/xray/.acme.sh/acme.sh --install-cert -d *.example.com --ecc --cert-file /home/xray/certs/xray.crt --fullchain-file /home/xray/certs/fullchain.crt --key-file /home/xray/certs/xray.key
```

Put `xray-cert-renew.sh` in /home/xray/certs.
```bash
#!/bin/bash

/home/xray/.acme.sh/acme.sh --install-cert -d *.example.com --ecc --cert-file /home/xray/certs/xray.crt --fullchain-file /home/xray/certs/fullchain.crt --key-file /home/xray/certs/xray.key
echo "Xray Certificates Renewed"

chmod +r /home/xray/certs/xray.key
echo "Read Permission Granted for Private Key"

sudo systemctl restart xray
echo "Xray Restarted"
```

Switch to user `xray`.
```zsh
su xray
```

Edit crontab.
```zsh
crontab -e
```

Then paste in the following line:
```
0 1 1 * *   bash /home/xray/certs/xray-cert-renew.sh
```
