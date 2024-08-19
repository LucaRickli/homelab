# Traefik

L4/7 proxy for docker services. Handles tls certificates for some services.

## Configuration

### Files

- `docker-compose.yaml`

  Contains the docker service configuration.

### Letsencrypt email

The email is optional to inform you about certificate expirations.

```sh
echo "LETSENCRYPT_EMAIL=<mail>"  >> .env
# or
export LETSENCRYPT_EMAIL=<mail>
```

## Installation

```sh
docker compose up -d
```
