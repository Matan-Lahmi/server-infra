# server-infra

Server infrastructure with Traefik reverse proxy and automatic SSL.

## Structure
```
server-infra/
├── traefik/
│   ├── docker-compose.yml  — Traefik reverse proxy
│   ├── .env.example        — Environment template
└── README.md
```

## Requirements
- Server with Docker installed
- Wildcard DNS A record: *.yourdomain.dev → server IP

## Setup

### 1. Clone
```bash
git clone https://github.com/matanlahmi/server-infra.git
cd server-infra/traefik
cp .env.example .env
nano .env
```

### 2. Generate Traefik dashboard password
```bash
docker run --rm httpd:alpine htpasswd -nb admin yourpassword
# Copy output to .env under TRAEFIK_AUTH
# Replace every $ with $$
```

### 3. Create Docker network
```bash
docker network create traefik-public
```

### 4. Start Traefik
```bash
docker compose up -d
```

### 5. Access dashboard
https://traefik.YOUR_DOMAIN

## Adding a new service
Add these labels to any docker-compose service:
```yaml
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.myapp.rule=Host(`myapp.YOUR_DOMAIN`)"
  - "traefik.http.routers.myapp.tls=true"
  - "traefik.http.routers.myapp.tls.certresolver=letsencrypt"
  - "traefik.http.services.myapp.loadbalancer.server.port=3000"
networks:
  - traefik-public

networks:
  traefik-public:
    external: true
```
