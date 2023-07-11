# xray-configs
My xray configuration files.

# Server configuration
This guide assumes you are using Ubuntu 22.10.<br/>
Install `acme.sh`, `nginx`, and `tailscale`. Relevant guides can be found online.

Create folders `certs` and `logs` under `/home/xray`.
```zsh
mkdir certs logs
```

Under `/var/www/html` prepare an `index.html` to use as decoy. It can be any static page of your choice.
```zsh
echo "Testing webpage" > /var/www/html/index.html
```

Add an `xray.conf` under `/etc/nginx/conf.d`.
```
server {
        listen 80;
        listen [::]:80;

        server_name ws.example.com vl.example.com tsc.example.com;

	return 301 https://$http_host$request_uri;
}

server {
	listen 9005;
	listen [::]:9005;

        server_name ws.example.com vl.example.com tsc.example.com;

	root /var/www/html;
	index index.html;
	add_header Strict-Transport-Security "max-age=63072000" always;
}
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

Put `server-config.json` as `config.json` under `/usr/local/etc/xray`. Modify fields accordingly.

Switch to user `xray`.
```zsh
su xray
```

Edit crontab.
```zsh
crontab -e
```

Then paste in the following line which will refresh your SSL certificates every month:
```
0 1 1 * *   bash /home/xray/certs/xray-cert-renew.sh
```
