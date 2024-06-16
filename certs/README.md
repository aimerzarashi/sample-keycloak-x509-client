## Create a Self Signed root certificate authority (CA):

### let's proceed with the creation of the CA certificate

```
openssl req -x509 -sha256 -days 3650 -newkey rsa:4096 -keyout rootCA.key -out rootCA.crt -subj "/C=JP/ST=Tokyo/O=rootCA/CN=rootCA"
1234
```

## Host Certificate:

### creating what is known as a certificate signing request (CSR)

```
openssl req -new -newkey rsa:4096 -keyout localhost.key -out localhost.csr -nodes -subj "/C=JP/ST=Tokyo/O=Keycloak/CN=localhost"
```

### sign the request with our rootCA.crt certificate and its private key

```
openssl x509 -req -CA rootCA.crt -CAkey rootCA.key -in localhost.csr -out localhost.crt -days 365 -CAcreateserial -extfile localhost.ext

openssl x509 -in localhost.crt -text
```

### Create pkcs12 file for server

```
openssl pkcs12 -export -out keystore.p12 -in localhost.crt -inkey localhost.key -name "server certificate" -chain -CAfile rootCA.crt -caname "self signed ca certificate"
2345
openssl pkcs12 -info -in keystore.p12
```

## Client (User) certificate:

```
openssl req -new -newkey rsa:4096 -nodes -keyout user1.key -out user1.csr -subj "/C=JP/ST=Tokyo/O=Client/CN=localhost/emailAddress=user1@example.com"

openssl x509 -req -CA rootCA.crt -CAkey rootCA.key -in user1.csr -out user1.crt -days 365 -CAcreateserial

openssl pkcs12 -export -out user1.p12 -name "user1" -inkey user1.key -in user1.crt**
```

## Client (User) certificate:
```
```

### Create a truststore using keytool
```
openssl pkcs12 -export -in rootCA.crt -inkey rootCA.key -out truststore.p12 -name "server certificate" -chain -CAfile rootCA.crt -caname "self signed ca certificate"
3456
openssl pkcs12 -info -in truststore.p12
```