# Deployment Instructions for n8n, Postgres, Ollama, and Qdrant

**Self-hosted n8n, Postgres, Ollama, and Qdrant** This guide provides step-by-step instructions for deploying n8n, Postgres, Ollama, and Qdrant using Docker Compose.

### ⚡️ Prerequisites
1. Install Docker and Docker Compose on your machine.

2. Create Docker networks for your services before deploying:
- `docker network create n8n_ollama_network`
- Make sure that Traefik is running and the traefik-network exists. If not, create it with the     command: `docker network create traefik-network`

3. Edit the .env file and fill in the necessary variables for:
- POSTGRES_VERSION, POSTGRES_USER, POSTGRES_PASSWORD, POSTGRES_DB
- N8N_VERSION, N8N_ENCRYPTION_KEY, N8N_USER_MANAGEMENT_JWT_SECRET, N8N_BASIC_AUTH_USER,  N8N_BASIC_AUTH_PASSWORD
- DOMAIN_NAME, SUBDOMAIN, GENERIC_TIMEZONE
- QDRANT_VERSION, QDRANT_API_KEY
- OLLAMA_VERSION

### ⚡️ Deployment

* ### To deploy the services, follow these steps:

1. Run Docker Compose to bring up the services:

```bash
docker compose -f docker-compose.yml -p n8n-integration up -d
```

2. Check the status of your containers:

```bash
docker ps
```
### ⚡️ Volumes

> `n8n_storage:` Persistent storage for n8n data.
> `n8n_postgres_storage:` Persistent storage for Postgres database.
> `ollama_storage:` Storage for Ollama data.
> `qdrant_storage:` Persistent storage for Qdrant.

### ⚡️ Networks

> `nn8n_ollama_network:` Persistent storage for n8n data.
> `traefik-network:` Persistent storage for Postgres database.


## ⚡️ Services

### Postgres

> Image: postgres:${POSTGRES_VERSION}
> Volumes: Data stored in n8n_postgres_storage.
> Environment Variables: Ensure all Postgres credentials are correctly set in the .env file.

### n8n

> Image: n8nio/n8n:${N8N_VERSION}
> Volumes: Configuration and backup data stored in n8n_storage and ./n8n/backup.
> Environment Variables: Ensure database connection, authentication, and encryption keys are configured.

### Qdrant

> Image: qdrant/qdrant:${QDRANT_VERSION}
> Volumes: Persistent data stored in qdrant_storage.
> Ports: Accessible via 127.0.0.1:6333.

### Ollama (CPU)

> mage: ollama/ollama:${OLLAMA_VERSION}
> Volumes: Persistent data stored in ollama_storage.
> Ports: Accessible via 127.0.0.1:11434.

### Editing the .env File

> Make sure to update the .env file with the appropriate values for all environment variables before starting the services. Example:

```bash
POSTGRES_VERSION=13
POSTGRES_USER=admin
POSTGRES_PASSWORD=password
POSTGRES_DB=n8n
N8N_VERSION=0.190.0
N8N_ENCRYPTION_KEY=your-encryption-key
N8N_USER_MANAGEMENT_JWT_SECRET=your-jwt-secret
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=password
DOMAIN_NAME=example.com
SUBDOMAIN=n8n
GENERIC_TIMEZONE=UTC
QDRANT_VERSION=1.0.0
QDRANT_API_KEY=your-qdrant-api-key
OLLAMA_VERSION=latest
```


### Traefik Integration

**n8n** is set up to work with Traefik. Ensure the following configurations are properly set up in the labels section of the n8n service for SSL, domain, and middleware settings.

### ⚡️ Troubleshooting

> Make sure that all necessary Docker networks (n8n_ollama_network, traefik-network) are created before deployment.
> Check if your containers are healthy with:
```bash
docker inspect --format '{{json .State.Health.Status}}' <container_name>
```

## Author
Konstantin Kochervrin