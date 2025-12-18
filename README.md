# Identity Vault

A small local service for storing **subject ID -> identity information** mappings
in a **single encrypted database file**.

Designed for research and data-collection workflows where identity data must be
kept separate from pseudonymised datasets.

---

## What this is

- A lightweight Python web service
- Stores mappings in one SQLCipher-encrypted SQLite file
- Users access identities via an API, not by opening the database
- Suitable for local or small-network deployment (e.g. Raspberry Pi)

---

## What this is not

- Not a cloud service
- Not a full identity management system
- Not designed for public internet exposure

---

## High-level design

- Identity data is stored in an encrypted database file (`identity.db`)
- The database encryption key is stored separately from the database
- A small API service is the only component that can open the database
- Users authenticate to the API using access tokens
- Research datasets store only `subject_id`, never identity data

Data flow:

User → Identity Vault API → Encrypted database file

---

## Threat model (summary)

This project is designed to protect against:

- Accidental disclosure of identity data
- Theft or copying of database files or backups
- Unauthorized access by researchers or analysts

Out of scope:

- Physical compromise of a running, unlocked system
- Attackers with root access to the host

---

## Running locally (development)

1. Create a virtual environment and install dependencies
2. Create a secret token in `secrets/token.txt`
3. Start the service:

   uvicorn app.main:app

4. The service will create an encrypted database file on first run

---

## API usage examples

Store a mapping:

curl -X POST http://localhost:8000/set \
  -H "Authorization: Bearer <TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"subject_id": "S123", "identity": {"email": "user@example.org"}}'

Retrieve a mapping:

curl http://localhost:8000/get/S123 \
  -H "Authorization: Bearer <TOKEN>"

---

## Repository structure

app/        # Python application code  
data/       # Encrypted database (not committed)  
secrets/    # Keys and tokens (not committed)  

---

## Future improvements

- Per-user roles and audit logging
- Containerised deployment (Podman)
- Simple web interface
- Key rotation and backup tooling

---

## License

MIT


Choose a license
