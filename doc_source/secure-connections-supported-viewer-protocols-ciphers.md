# Supported protocols and ciphers between viewers and CloudFront<a name="secure-connections-supported-viewer-protocols-ciphers"></a>

When you [require HTTPS between viewers and your CloudFront distribution](distribution-web-values-specify.md#DownloadDistValuesViewerProtocolPolicy), you must choose a [security policy](distribution-web-values-specify.md#DownloadDistValues-security-policy), which determines the following settings:
+ The minimum SSL/TLS protocol that CloudFront uses to communicate with viewers\.
+ The ciphers that CloudFront can use to encrypt the communication with viewers\.

To choose a security policy, specify the applicable value for [Security policy](distribution-web-values-specify.md#DownloadDistValues-security-policy)\. The following table lists the protocols and ciphers that CloudFront can use for each security policy\.

A viewer must support at least one of the supported ciphers to establish an HTTPS connection with CloudFront\. CloudFront chooses a cipher in the listed order from among the ciphers that the viewer supports\. See also [OpenSSL, s2n, and RFC cipher names](#secure-connections-openssl-rfc-cipher-names)\.


|  | Security policy |  | SSLv3 | TLSv1 | TLSv1\_2016 | TLSv1\.1\_2016 | TLSv1\.2\_2018 | TLSv1\.2\_2019 | TLSv1\.2\_2021 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Supported SSL/TLS protocols | 
| TLSv1\.3 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | 
| TLSv1\.2 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | 
| TLSv1\.1 | ♦ | ♦ | ♦ | ♦ |  |  |  | 
| TLSv1 | ♦ | ♦ | ♦ |  |  |  |  | 
| SSLv3 | ♦ |  |  |  |  |  |  | 
| Supported TLSv1\.3 ciphers | 
| TLS\_AES\_128\_GCM\_SHA256 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | 
| TLS\_AES\_256\_GCM\_SHA384 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | 
| TLS\_CHACHA20\_POLY1305\_SHA256 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | 
| Supported ECDSA ciphers | 
| ECDHE\-ECDSA\-AES128\-GCM\-SHA256 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | 
| ECDHE\-ECDSA\-AES128\-SHA256 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ |  | 
| ECDHE\-ECDSA\-AES128\-SHA | ♦ | ♦ | ♦ | ♦ |  |  |  | 
| ECDHE\-ECDSA\-AES256\-GCM\-SHA384 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | 
| ECDHE\-ECDSA\-CHACHA20\-POLY1305 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | 
| ECDHE\-ECDSA\-AES256\-SHA384 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ |  | 
| ECDHE\-ECDSA\-AES256\-SHA | ♦ | ♦ | ♦ | ♦ |  |  |  | 
| Supported RSA ciphers | 
| ECDHE\-RSA\-AES128\-GCM\-SHA256 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | 
| ECDHE\-RSA\-AES128\-SHA256 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ |  | 
| ECDHE\-RSA\-AES128\-SHA | ♦ | ♦ | ♦ | ♦ |  |  |  | 
| ECDHE\-RSA\-AES256\-GCM\-SHA384 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | 
| ECDHE\-RSA\-CHACHA20\-POLY1305 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ | 
| ECDHE\-RSA\-AES256\-SHA384 | ♦ | ♦ | ♦ | ♦ | ♦ | ♦ |  | 
| ECDHE\-RSA\-AES256\-SHA | ♦ | ♦ | ♦ | ♦ |  |  |  | 
| AES128\-GCM\-SHA256 | ♦ | ♦ | ♦ | ♦ | ♦ |  |  | 
| AES256\-GCM\-SHA384 | ♦ | ♦ | ♦ | ♦ | ♦ |  |  | 
| AES128\-SHA256 | ♦ | ♦ | ♦ | ♦ | ♦ |  |  | 
| AES256\-SHA | ♦ | ♦ | ♦ | ♦ |  |  |  | 
| AES128\-SHA | ♦ | ♦ | ♦ | ♦ |  |  |  | 
| DES\-CBC3\-SHA | ♦ | ♦ |  |  |  |  |  | 
| RC4\-MD5 | ♦ |  |  |  |  |  |  | 

## OpenSSL, s2n, and RFC cipher names<a name="secure-connections-openssl-rfc-cipher-names"></a>

OpenSSL and [s2n](https://github.com/awslabs/s2n) use different names for ciphers than the TLS standards use \([RFC 2246](https://tools.ietf.org/html/rfc2246), [RFC 4346](https://tools.ietf.org/html/rfc4346), [RFC 5246](https://tools.ietf.org/html/rfc5246), and [RFC 8446](https://tools.ietf.org/html/rfc8446)\)\. The following table maps the OpenSSL and s2n names to the RFC name for each cipher\.

For ciphers with elliptic curve key exchange algorithms, CloudFront supports the following elliptic curvers:
+ prime256v1
+ secp384r1
+ X25519


| OpenSSL and s2n cipher name | RFC cipher name | 
| --- | --- | 
| Supported TLSv1\.3 ciphers | 
| TLS\_AES\_128\_GCM\_SHA256 | TLS\_AES\_128\_GCM\_SHA256 | 
| TLS\_AES\_256\_GCM\_SHA384 | TLS\_AES\_256\_GCM\_SHA384 | 
| TLS\_CHACHA20\_POLY1305\_SHA256 | TLS\_CHACHA20\_POLY1305\_SHA256 | 
| Supported ECDSA ciphers | 
| ECDHE\-ECDSA\-AES128\-GCM\-SHA256 | TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256 | 
| ECDHE\-ECDSA\-AES128\-SHA256 | TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_CBC\_SHA256 | 
| ECDHE\-ECDSA\-AES128\-SHA | TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_CBC\_SHA | 
| ECDHE\-ECDSA\-AES256\-GCM\-SHA384 | TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384 | 
| ECDHE\-ECDSA\-CHACHA20\-POLY1305 | TLS\_ECDHE\_ECDSA\_WITH\_CHACHA20\_POLY1305\_SHA256 | 
| ECDHE\-ECDSA\-AES256\-SHA384 | TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_CBC\_SHA384 | 
| ECDHE\-ECDSA\-AES256\-SHA | TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_CBC\_SHA | 
| Supported RSA ciphers | 
| ECDHE\-RSA\-AES128\-GCM\-SHA256 | TLS\_ECDHE\_RSA\_WITH\_AES\_128\_GCM\_SHA256 | 
| ECDHE\-RSA\-AES128\-SHA256 | TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256  | 
| ECDHE\-RSA\-AES128\-SHA | TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA | 
| ECDHE\-RSA\-AES256\-GCM\-SHA384 | TLS\_ECDHE\_RSA\_WITH\_AES\_256\_GCM\_SHA384  | 
| ECDHE\-RSA\-CHACHA20\-POLY1305 | TLS\_ECDHE\_RSA\_WITH\_CHACHA20\_POLY1305\_SHA256 | 
| ECDHE\-RSA\-AES256\-SHA384 | TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384  | 
| ECDHE\-RSA\-AES256\-SHA | TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA | 
| AES128\-GCM\-SHA256 | TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 | 
| AES256\-GCM\-SHA384 | TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 | 
| AES128\-SHA256 | TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 | 
| AES256\-SHA | TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA | 
| AES128\-SHA | TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA | 
| DES\-CBC3\-SHA  | TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA  | 
| RC4\-MD5 | TLS\_RSA\_WITH\_RC4\_128\_MD5 | 

## Supported signature schemes between viewers and CloudFront<a name="secure-connections-viewer-signature-schemes"></a>

CloudFront supports the following signature schemes for connections between viewers and CloudFront\.
+ TLS\_SIGNATURE\_SCHEME\_RSA\_PSS\_PSS\_SHA256
+ TLS\_SIGNATURE\_SCHEME\_RSA\_PSS\_PSS\_SHA384
+ TLS\_SIGNATURE\_SCHEME\_RSA\_PSS\_PSS\_SHA512
+ TLS\_SIGNATURE\_SCHEME\_RSA\_PSS\_RSAE\_SHA256
+ TLS\_SIGNATURE\_SCHEME\_RSA\_PSS\_RSAE\_SHA384
+ TLS\_SIGNATURE\_SCHEME\_RSA\_PSS\_RSAE\_SHA512
+ TLS\_SIGNATURE\_SCHEME\_RSA\_PKCS1\_SHA256
+ TLS\_SIGNATURE\_SCHEME\_RSA\_PKCS1\_SHA384
+ TLS\_SIGNATURE\_SCHEME\_RSA\_PKCS1\_SHA512
+ TLS\_SIGNATURE\_SCHEME\_RSA\_PKCS1\_SHA224
+ TLS\_SIGNATURE\_SCHEME\_ECDSA\_SHA256
+ TLS\_SIGNATURE\_SCHEME\_ECDSA\_SHA384
+ TLS\_SIGNATURE\_SCHEME\_ECDSA\_SHA512
+ TLS\_SIGNATURE\_SCHEME\_ECDSA\_SHA224
+ TLS\_SIGNATURE\_SCHEME\_ECDSA\_SECP256R1\_SHA256
+ TLS\_SIGNATURE\_SCHEME\_ECDSA\_SECP384R1\_SHA384
+ TLS\_SIGNATURE\_SCHEME\_RSA\_PKCS1\_SHA1
+ TLS\_SIGNATURE\_SCHEME\_ECDSA\_SHA1