# SSLPoke
Java tool for testing validity (certificates) of trust stores

Usage:
```
java -Djavax.net.ssl.trustStore=client-truststore.jks SSLPoke mydomain.com 443
```

- If the server certificate is trusted by the client-truststore it will print,
```
Successfully connected
```

- Othwerwise it will print print an exception like this.
```
javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
```

To import the public certificate of the server into the client-truststore, we can follow the steps below.

```
openssl s_client -showcerts -connect mydomain.com:443 < /dev/null | openssl x509 -outform PEM > mydomain.com.pem

keytool -import -trustcacerts -alias mydomain.com -file mydomain.com.pem -keystore client-truststore.jks
```

Additionally, note that if the server certificate is a CA-signed certificate, we can just add the relevant root certificate instead of the end-cert or intermediate certs. ([ref](https://security.stackexchange.com/questions/119460/do-i-put-my-subordinate-intermediate-or-root-ca-certificate-in-my-truststore))

The original code came from https://confluence.atlassian.com/download/attachments/117455/SSLPoke.java
