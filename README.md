# xray-configs
My xray configuration files.

# Server configuration
Set `CF_Token` environment variable
```zsh
export CF_Token="my_cf_token"
```

Get certificates
```zsh
acme.sh --issue -d '*.example.com' --keylength ec-256 --force --dns dns_cf --ocsp-must-staple
/home/xray/.acme.sh/acme.sh --install-cert -d *.example.com --ecc --cert-file /home/xray/certs/xray.crt --fullchain-file /home/xray/certs/fullchain.crt --key-file /home/xray/certs/xray.key
```
