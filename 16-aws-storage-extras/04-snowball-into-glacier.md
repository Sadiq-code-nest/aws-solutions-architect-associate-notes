# Snowball into Glacier

**Snowball → Glacier (via S3)**

- Snowball **can't** import directly into Glacier.
- Flow: **Snowball → S3 (import) → Glacier (via S3 Lifecycle Policy)**

**Memory trick:** *"Snowball melts into S3, then freezes into Glacier."*
S3 is always the middleman — Snowball dumps data into S3, and a lifecycle policy transitions it to Glacier for cold storage.
