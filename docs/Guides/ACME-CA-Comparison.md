# ACME CA Comparison

As more public certificate authorities hop on the [ACME][rfc00] bandwagon, it is important to understand the details and limitations of their implementations. This page will attempt to keep track of that data.

## ACME CA Info

| Name                       | Free SAN Limit | Free Wildcards     | Free Lifetime | Chain Info                                                              | Rate Limits    | Directory Endpoint           | Notes                                                    |
| ----                       | -------------- | :------------:     | ------------- | ----------                                                              | -----------    | ------------------           | -----                                                    |
| [Let's&nbsp;Encrypt][le01] | 100 names      | :white_check_mark: | 90 days       | [Chains][le06]                                                          | [Policy][le02] | [RSA + ECC][le10]            | [Service Status][le09]<br />[Staging Environment][le03]  |
| [BuyPass][bp01]            | 5 names        | :x:                | 180 days      | [Roots][bp04] "Go SSL"                                                  | [Policy][bp02] | [RSA + ECC][bp05]            | [Test Environment][bp03]                                 |
| [ZeroSSL][z01]             | 100+ names     | :white_check_mark: | 90 days       | RSA [Iss1][z02]/[Iss2][z03]/[Root][z04]<br />ECC [Iss1][z05]/[Iss2][z06]/[Root][z07] | ??             | [RSA + ECC][z08]             |                                                          |
| [SSL.com][ss01]            | 1 name + www   | :x:                | 90 days       | RSA [Iss][ss02]/[Root][ss03]<br />ECC [Iss][ss06]/[Root][ss07]          | ??             | [RSA][ss04]<br />[ECC][ss05] | See Warning below                                        |

* Wildcard names (if supported) count towards Subject Alternative Name (SAN) limits.
* `1 name + www` means one domain name plus its www name variant such as `example.com` and `www.example.com`
* Using Let's Encrypt's ECDSA-only chain currently requires your ACME account be [added to an allow-list](https://community.letsencrypt.org/t/ecdsa-availability-in-production-environment/150679). Otherwise, your ECDSA cert will be signed by the RSA chain.
* ZeroSSL supports a custom REST API that some clients use instead of pure ACME.
* **SSL.com Warning:** If your SSL.com account has funds available, you will be charged for a paid 1-year certificate instead of a free 90-day certificate. There is no known way to request only a free certificate.

## ACME Spec and Feature Support

Some of the features in the ACME protocol are optional. Others are mandatory, but not yet supported by some implementations. Here is the status of those various features in each CA.

*NOTE: Multi-perspective validation is not technically part of the ACME protocol. But it is an important security feature for the integrity of domain validation.*

| Feature                                      | [Let's&nbsp;Encrypt][le01] | [BuyPass][bp01]                              | [ZeroSSL][z01]     | [SSL.com][ss01]                              |
| -------                                      | :------------------------: | :-------------:                              | :------------:     | :-------------:                              |
| [(EAB) External<br />Account Binding][rfc01] | n/a                        | n/a                                          | Required*          | Required                                     |
| [Multi-perspective<br />Validation][le05]    | :white_check_mark:         | :x:                                          | :x:                | :x:                                          |
| [Account<br />Key Rollover][rfc02]           | :white_check_mark:         | :white_check_mark:                           | :x:                | :x:*                                         |
| [Account<br />Deactivation][rfc03]           | :white_check_mark:         | :white_check_mark:                           | :white_check_mark: | :white_check_mark:                           |
| [Account<br />Orders][rfc04]                 | :x: *([Planned][le07])*    | :x:                                          | :x:                | :x:*                                         |
| [IP Address<br />Identifiers][rfc05]         | :x: *([Planned][le08])*    | :x:                                          | :x:*               | :x:                                          |
| [Pre-Authorization][rfc06]                   | :x:                        | :white_check_mark:                           | :x:                | :x:                                          |
| [Authorization<br />Deactivation][rfc07]     | :white_check_mark:         | :white_check_mark:                           | :white_check_mark: | :white_check_mark:                           |
| [Cert<br />Revocation][rfc08]                | :white_check_mark:         | :warning:<br />*(Only using account key)*    | :white_check_mark: | :white_check_mark:                           |
| [Challenge<br />Retrying][rfc09]             | :x:                        | :warning:<br />*(Client must request retry)* | :white_check_mark: | :warning:<br />*(Client must request retry)* |

* :white_check_mark: = Feature supported
* :x: = Feature unsupported
* :warning: = Feature partially supported.
* :question: = Support unknown or untested
* SSL.com throws "Missing Authentication Token" errors when making some calls against Account endpoints which is why those features are labeled Unsupported.
* SSL.com requires an email address in the ACME account contact field, but doesn't enforce it on creation time. Instead, it throws an "badCSR" error when you try to finalize an order from an account with an empty address.
* ZeroSSL's EAB credentials can only be used once to establish a new ACME account. Creating additional accounts requires generating new EAB credentials.
* ZeroSSL does support IP address based certificates, but not via the ACME protocol.


[wk01]: https://en.wikipedia.org/wiki/Domain-validated_certificate
[le01]: https://letsencrypt.org/
[le02]: https://letsencrypt.org/docs/rate-limits/
[le03]: https://letsencrypt.org/docs/staging-environment/
[le04]: https://acme-v02.api.letsencrypt.org/directory
[le05]: https://letsencrypt.org/2020/02/19/multi-perspective-validation.html
[le06]: https://letsencrypt.org/certificates/
[le07]: https://github.com/letsencrypt/boulder/issues/3335
[le08]: https://github.com/letsencrypt/boulder/issues/2706
[le09]: https://letsencrypt.status.io/
[le10]: https://acme-v02.api.letsencrypt.org/directory
[rfc00]: https://datatracker.ietf.org/doc/html/rfc8555
[rfc01]: https://datatracker.ietf.org/doc/html/rfc8555#section-7.3.4
[rfc02]: https://datatracker.ietf.org/doc/html/rfc8555#section-7.3.5
[rfc03]: https://datatracker.ietf.org/doc/html/rfc8555#section-7.3.6
[rfc04]: https://datatracker.ietf.org/doc/html/rfc8555#section-7.1.2.1
[rfc05]: https://datatracker.ietf.org/doc/html/rfc8738
[rfc06]: https://datatracker.ietf.org/doc/html/rfc8555#section-7.4.1
[rfc07]: https://datatracker.ietf.org/doc/html/rfc8555#section-7.5.2
[rfc08]: https://datatracker.ietf.org/doc/html/rfc8555#section-7.6
[rfc09]: https://datatracker.ietf.org/doc/html/rfc8555#section-8.2
[bp01]: https://www.buypass.com/
[bp02]: https://community.buypass.com/t/m2r5cj/rate-limits
[bp03]: https://api.test4.buypass.no/acme/directory
[bp04]: https://www.buypass.com/security/buypass-root-certificates
[bp05]: https://api.buypass.com/acme/directory
[z01]: https://zerossl.com/
[z02]: https://crt.sh/?q=c81a8bd1f9cf6d84c525f378ca1d3f8c30770e34
[z03]: https://crt.sh/?q=d89e3bd43d5d909b47a18977aa9d5ce36cee184c
[z04]: https://crt.sh/?q=d1eb23a46d17d68fd92564c2f1f1601764d8e349
[z05]: https://crt.sh/?q=7f95276d4951499fd756df344aa24fb38ceaf678
[z06]: https://crt.sh/?q=ca7788c32da1e4b7863a4fb57d00b55ddacbc7f9
[z07]: https://crt.sh/?q=d1eb23a46d17d68fd92564c2f1f1601764d8e349
[z08]: https://acme.zerossl.com/v2/DV90
[ss01]: https://www.ssl.com/
[ss02]: https://crt.sh/?q=33ee4e370a8d90fd4b1445e672226c4b829cc6d2
[ss03]: https://crt.sh/?q=b7ab3308d1ea4477ba1480125a6fbda936490cbb
[ss04]: https://acme.ssl.com/sslcom-dv-rsa
[ss05]: https://acme.ssl.com/sslcom-dv-ecc
[ss06]: https://crt.sh/?q=9219612e901c4904f9835ee8c2add54ff0b797fd
[ss07]: https://crt.sh/?q=c3197c3924e654af1bc4ab20957ae2c30e13026a
