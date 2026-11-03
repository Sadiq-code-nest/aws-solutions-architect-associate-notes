# AWS SAA-C03 Notes: Subject Alternative Name (SAN)

## 1. What is SAN?

Subject Alternative Name (SAN) is a field inside an SSL/TLS certificate that specifies the domain names or other identities the certificate is valid for.

Easy example: One company has three websites:

- `www.example.com`
- `api.example.com`
- `shop.example.com`

Instead of using a separate certificate for every hostname, the company can use one SAN certificate containing all three names.

One TLS certificate

Subject Alternative Name (SAN)

Names covered by the certificate

`www.example.com`

`api.example.com`

`shop.example.com`

Memory hook: SAN = Several Addresses/Names on one certificate.

A certificate only covers the names actually listed in its SAN extension, including any applicable wildcard names. It does not automatically cover every subdomain.

## 2. How does SAN work with a Load Balancer?

Imagine an application uses an AWS Application Load Balancer (ALB) to route traffic to different services.

Users / browsers

HTTPS requests to different hostnames

www.example.com

api.example.com

shop.example.com

ALB HTTPS listener

Uses a certificate whose SAN covers the requested hostname

ALB listener rules

Can route requests by hostname to different target groups

### Step-by-step

1. A user visits `https://api.example.com`.
2. The browser checks the TLS certificate presented by the load balancer.
3. The certificate must be valid for `api.example.com`; its SAN extension can establish that hostname match.
4. The ALB terminates the HTTPS connection.
5. The ALB can use a host-based listener rule to forward the request to the API target group.

Important exam distinction: SAN helps establish which hostnames a certificate covers. ALB listener rules decide where requests go. They are two separate functions.

## 3. When do you use SAN? — Exam scenarios

[Wildcard Certificates](https://images.openai.com/static-rsc-4/VOLwNzRHalU8nJwXsIKcpf7Knf0O0Y2Uting3rP8s3K4EKeChBPtWkZhf588rSn5MZ_EZ3PWooMc-hO6fKW_4pSHdtl2A_NBn3SBMfve-tmOH0ItSbBqD9c1r4KZpm9bP7Uox3O8l84sOPN1R7xt4E98KwK0o1y2CE8P9pAptD0?purpose=inline)

Scenario A: Multiple subdomains

A company hosts `app.example.com`, `api.example.com`, and `admin.example.com` behind an ALB. It wants one certificate to cover all three hostnames.

Think SAN certificate

Request an ACM certificate listing all required names.

[Découvrez la haute disponibilité - OpenClassrooms](https://images.openai.com/static-rsc-4/wBxUSUspmgE4PDm9A1PSpnlwCg5zotoueibpmLQR3DrJcBHQ7gRptI92_oxZUkJbREN0-jtoaQ2RJQEwjqoKQUkEyFNHELKpxXKXwp551yQvKJ5F0bQmzmhPhOwPacwxdUpRpycVTCUK2cKM5yjevhCOyULDJrdiuBurn0w6TF8?purpose=inline)

Scenario B: Several HTTPS applications on one ALB

Several applications use different domain names but share one load balancer and HTTPS port 443.

Think SAN or multiple certificates with SNI

A SAN certificate can cover multiple names. Alternatively, an ALB can use multiple certificates and SNI to select a suitable certificate for each hostname.

Elastic Load Balancing

+1

[How to Generate the Certificate Signing Request (CSR) for Wildcard SSL
 – Trustico® Online Security](https://images.openai.com/static-rsc-4/m2Jb_YYE_ntv25-tDDkLoc5K7JOCtYhK0Q2ASS2n8RL6g80UBeV3hEQF5B2lN_fIWGufKfucxwSioLGkKiDnt7Ur9S3v4lHumZtic5AD3hGRPN8sl_6o17jzXqU1MAot2KlXYXD3loeADeMbQsElHoom-AwhmmEfFmWFxPL_c84?purpose=inline)

Scenario C: Many subdomains

A company wants to cover `app.example.com`, `shop.example.com`, and other first-level subdomains.

Think wildcard certificate

- `.example.com` can cover those first-level subdomains, but it does not cover the apex `example.com` or deeper names such as `test.app.example.com`.

Elastic Load Balancing

+1

## 4. SAN vs. wildcard vs. SNI

| Term | What it means | Exam clue |
| --- | --- | --- |
| SAN | Lists specific names covered by a certificate | One certificate for several named domains |
| Wildcard certificate | Covers matching names at a particular subdomain level | Many subdomains under one domain |
| SNI | Lets a client indicate the hostname during TLS negotiation so the server can select a suitable certificate | Multiple certificates on one ALB HTTPS listener |
| ACM | Requests, manages, and renews supported certificates | AWS-managed TLS certificate |

## 5. Exam trigger phrases

Look for these clues in SAA-C03 questions:

- “One SSL/TLS certificate for multiple domain names” → SAN certificate
- “Multiple subdomains, such as `api.example.com` and `shop.example.com`” → SAN or wildcard, depending on the requirement.
- “Multiple HTTPS websites using one ALB” → ACM certificates; SAN or multiple certificates with SNI
- “Encrypt traffic between clients and the load balancer” → HTTPS listener with an SSL/TLS certificate
- “Route requests to different target groups based on hostname” → ALB host-based routing, not SAN itself.

Official references: AWS Certificate Manager documentation for [requesting SAN certificates](https://docs.aws.amazon.com/acm/latest/APIReference/API_RequestCertificate.html?utm_source=chatgpt.com) and Elastic Load Balancing documentation for [HTTPS listener certificates](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/https-listener-certificates.html?utm_source=chatgpt.com).

Final memory hook:

- SAN = Which names does the certificate cover?
- SNI = Which certificate should the load balancer present?
- ALB host-based routing = Where should the request go?
