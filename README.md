# xray-configs
My xray configuration files.

# Server configuration
```zsh
acme.sh --issue -d '*.example.com' --keylength ec-256 --force --dns dns_cf --ocsp-must-staple
/home/xray/.acme.sh/acme.sh --install-cert -d *.example.com --ecc --cert-file /home/xray/certs/xray.crt --fullchain-file /home/xray/certs/fullchain.crt --key-file /home/xray/certs/xray.key
```
