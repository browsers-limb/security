# SSL

# Become a Certificate Authority

### Generate private key
```bash
openssl genrsa -des3 -out myCA.key 2048
```
### Generate root certificate
```bash
openssl req -x509 -new -nodes -key myCA.key -sha256 -days 825 -out myCA.pem
```

# Create CA-signed certs

```bash
NAME=localhost # Use your own domain name
```bash
# Generate a private key
```bash
openssl genrsa -out $NAME.key 2048
```
### Create a certificate-signing request
```bash
openssl req -new -key $NAME.key -out $NAME.csr
```
### Create a config file for the extensions
```bash
>$NAME.ext cat <<-EOF
```
```bash
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names
[alt_names]
DNS.1 = $NAME # Be sure to include the domain name here because Common Name is not so commonly honoured by itself
DNS.2 = bar.$NAME # Optionally, add additional domains (I've added a subdomain here)
IP.1 = 192.168.0.13 # Optionally, add an IP address (if the connection which you have planned requires it)
EOF
```
### Create the signed certificate
```bash
openssl x509 -req -in $NAME.csr -CA myCA.pem -CAkey myCA.key -CAcreateserial \
-out $NAME.crt -days 825 -sha256 -extfile $NAME.ext
```

### Add the authority
```bash
sudo cp myCA.pem /usr/share/pki/ca-trust-source/anchors/
```

### Refresh the local trusted authorities
```bash
sudo update-ca-trust
```
