## sampleconfig

提供了一些样例证书文件和配置文件。

pem （Privacy Enhanced Mail，属于 X.509 证书标准格式）格式，内容是 BASE64 编码，可以通过 openssl 来查看内容。

如

```sh
$ openssl x509 -in msp/sampleconfig/admincerts/admincert.pem -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            07:70:93:0c:e5:38:ee:c5:02:e4:ae:24:9f:f0:9a:aa:78:75:d7:86
        Signature Algorithm: ecdsa-with-SHA256
        Issuer: C=US, ST=California, L=San Francisco, O=Internet Widgets, Inc., OU=WWW, CN=example.com
        Validity
            Not Before: Oct 12 19:31:00 2016 GMT
            Not After : Oct 11 19:31:00 2021 GMT
        Subject: C=US, ST=California, L=San Francisco, O=Internet Widgets, Inc., OU=WWW, CN=example.com
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
            EC Public Key:
                pub:
                    04:a2:07:e5:bd:89:69:29:aa:89:05:c7:ca:a0:be:
                    72:69:27:20:27:05:8b:90:1d:75:58:ec:42:2c:c3:
                    57:ab:99:e2:fe:ac:f8:f5:a2:27:2c:df:03:fa:d3:
                    b4:cb:d8:00:fa:bf:c9:e1:07:a4:15:01:d6:3d:39:
                    de:6c:67:a4:cb
                ASN1 OID: prime256v1
        X509v3 extensions:
            X509v3 Key Usage: critical
                Certificate Sign, CRL Sign
            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Subject Key Identifier:
                17:67:42:3D:AA:9E:82:3F:C4:C5:1D:9F:5B:C3:99:D1:B5:9C:48:10
            X509v3 Authority Key Identifier:
                keyid:17:67:42:3D:AA:9E:82:3F:C4:C5:1D:9F:5B:C3:99:D1:B5:9C:48:10

    Signature Algorithm: ecdsa-with-SHA256
        30:44:02:20:07:a7:94:5b:a7:d1:26:d4:6f:d4:98:a9:fc:5a:
        c0:9b:a8:37:d6:79:c5:5b:64:f4:23:dd:12:b8:a0:6e:64:41:
        02:20:40:07:b8:38:e2:98:84:97:61:dd:fe:d4:45:a2:9f:19:
        37:f8:f7:6f:e7:99:19:ad:2b:ec:92:2a:3a:47:4a:b5
```



