# DNS and How it works

[⬅ Back](../README.md)

| # | Topic | Type |
|---|-------|------|
| 01 | 📄 [Domain Name System which translates the human-friendly hostnames](01-domain-name-system-which-translates-the-human-friendly-hostn.md) | note |
| 02 | ▫️ Your browser first checks its own local cache (did I visit this before?) | outline only |
| 03 | 📄 [If not found → asks the Local DNS Server](03-if-not-found-asks-the-local-dns-server.md) | note |
| 04 | 📄 [Root says: "I don't know the IP, but .com domains? Go ask the TLD server at 1.2.3.4"](04-root-says-i-don-t-know-the-ip-but-com-domains-go-ask-the-tld.md) | note |
| 05 | 📄 [TLD (.com) says: "I don't know the IP either, but example.com specifically? Go ask 5.6.7.8"](05-tld-com-says-i-don-t-know-the-ip-either-but-example-com-spec.md) | note |
| 06 | 📄 [This one actually knows → replies: "example.com IP is 9.10.11.12"](06-this-one-actually-knows-replies-example-com-ip-is-9-10-11-12.md) | note |
| 07 | ▫️ Local DNS caches it (with a TTL timer) → sends IP to your browser | outline only |
| 08 | 📄 [Browser connects to the web server at 9.10.11.12 ✅](08-browser-connects-to-the-web-server-at-9-10-11-12.md) | note |
