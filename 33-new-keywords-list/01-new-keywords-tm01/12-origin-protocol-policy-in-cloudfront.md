# What is Origin Protocol Policy?

CloudFront communicates with your origin, such as S3 or an HTTP server.

The Origin Protocol Policy determines how CloudFront communicates with the origin.

Common choices include:

### HTTP Only

```
CloudFront → HTTP → Origin
```

### HTTPS Only

```
CloudFront → HTTPS → Origin
```

### Match Viewer

CloudFront communicates with the origin using the same protocol the viewer used to connect to CloudFront.

Conceptually:

```
Viewer HTTP
    ↓
CloudFront
    ↓
HTTP Origin

Viewer HTTPS
    ↓
CloudFront
    ↓
HTTPS Origin
```
