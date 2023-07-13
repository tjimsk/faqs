# TLS Web Server Self Signed Certificate Guide

1. Generate a 2048-bit RSA private key
---

with passphrase:
```
openssl genrsa -des3 -out private.pem 2048
```

without passphrase:
```
openssl genrsa -out private.pem 2048
```

2. Generate public key
---

```
openssl rsa -in private.pem -outform PEM -pubout -out public.pem
```

3. Generate CA root certificate
---

```
openssl req -x509 -new -nodes -key private.pem -sha256 -days 1825 -out myca.pem
```

4. Generate CSR for web service
---

```
openssl req -new -key private.pem -out request.csr
```

5. Create extension config file
---

```
cat > request.ext <<'endmsg'
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = ds920
DNS.2 = ds920.local
DNS.3 = jbookpro.local
DNS.4 = jbookpro
DNS.5 = jimmy-lunar-vm.local
DNS.6 = jimmy-lunar-vm
DNS.7 = localhost
endmsg
```

6. Create certificate 
---

```
openssl x509 -req -in request.csr -CA myca.pem -CAkey private.pem -CAcreateserial -out cert.pem -days 825 -sha256 -extfile request.ext
```