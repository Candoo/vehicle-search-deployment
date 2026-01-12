# Vehicle Search Deployment

This repository contains Docker Compose configurations for running the Vehicle Search application in both development and production environments.

## Project Structure

- `../vehicle-search` - Nuxt.js frontend application
- `../vehicles-api` - Go backend API
- `docker-compose.yml` - Development environment (with HMR and hot reload)
- `docker-compose.prod.yml` - Production environment (standalone, do not merge with dev)

## Prerequisites

- Docker and Docker Compose installed
- Both `vehicle-search` and `vehicles-api` repositories cloned as sibling directories

## Quick Links (Development)

Once services are running:

| Service | URL | Purpose |
|---------|-----|---------|
| **Frontend** | http://localhost:3000 | Vehicle search application |
| **API Base** | http://localhost:8080 | REST API endpoint |
| **API Docs** | http://localhost:8080/swagger/index.html | Interactive Swagger documentation |
| **API Health** | http://localhost:8080/health | API health check |
| **Database** | localhost:5432 | PostgreSQL (psql, DBeaver, etc.) |

## Development Setup

1. **Clone all repositories**:
   ```bash
   git clone https://github.com/Candoo/vehicle-search-deployment.git deployment
   git clone https://github.com/Candoo/vehicle-search.git vehicle-search
   git clone https://github.com/Candoo/vehicles-api.git vehicles-api
   ```

2. **Start development environment**:
   ```bash
   cd deployment
   docker-compose up
   ```

3. **Access the application**:
   - Open browser to http://localhost:3000
   - Check API at http://localhost:8080/swagger/index.html for interactive API documentation

## Production Setup

1. **Configure environment variables**:
   ```bash
   cp .env.production.example .env.production
   # Edit .env.production with your production values
   ```

2. **Start production environment**:
   ```bash
   docker-compose -f docker-compose.prod.yml --env-file .env.production up -d --build
   ```

   **Important**: Use `docker-compose.prod.yml` alone. Do NOT merge it with `docker-compose.yml`.

3. **Access the application**:
   - Open browser to http://localhost:3000 (or your configured frontend port)
   - Check API at http://localhost:8080/swagger/index.html (or your configured API port)

### Production Configuration Notes
- Update `NUXT_PUBLIC_API_BASE` in `.env.production` to your production API URL (e.g., `https://api.yourdomain.com`)
- For external database, update the DB_* variables in `.env.production`
- The included PostgreSQL is for local testing; remove or disable it for production by setting `postgres: null` in `docker-compose.prod.yml`

## Environment Variables

### Development (.env.example)
- Uses default local Docker PostgreSQL
- API accessible at `http://localhost:8080`
- Frontend at `http://localhost:3000`

### Production (.env.production)
- Configurable database settings (local or external)
- API and Frontend URLs configurable
- SSL settings for database connections
- Update `NUXT_PUBLIC_API_BASE` for production frontend API calls

#### Key Environment Variables
- `DB_HOST`: Database host (e.g., vehicle-db for local, or external host)
- `DB_USER`, `DB_PASSWORD`, `DB_NAME`: Database credentials
- `DB_SSLMODE`: SSL mode (disable for local, require for production)
- `API_PORT`: API port (default 8080)
- `NUXT_PUBLIC_API_BASE`: Frontend API base URL (e.g., http://localhost:8080 for local, https://api.yourdomain.com for production)

## Services

### Development
- **PostgreSQL**: Local database with persistent volume
- **API**: Go application with hot reload via volume mounts
- **Frontend**: Nuxt.js with HMR via volume mounts

### Production
- **PostgreSQL**: Local database for testing (can be disabled for external DB)
- **API**: Optimized Go binary
- **Frontend**: Built Nuxt.js application

## Useful Commands

```bash
# Development
docker-compose up

# Production (use standalone docker-compose.prod.yml - do NOT merge with docker-compose.yml)
docker-compose -f docker-compose.prod.yml --env-file .env.production up -d --build

# Development logs
docker-compose logs -f

# Production logs
docker-compose -f docker-compose.prod.yml logs -f

# View logs for specific service
docker-compose logs -f api
docker-compose logs -f postgres

# Stop all services (development)
docker-compose down

# Stop all services (production)
docker-compose -f docker-compose.prod.yml down

# Rebuild and restart (development)
docker-compose up --build
```

## Testing Connections

```bash
# Test Frontend
curl http://localhost:3000

# Test API health
curl http://localhost:8080/health

# Get list of vehicles via API
curl "http://localhost:8080/vehicles?page=1&results_per_page=5"

# Access API Swagger documentation
# Open in browser: http://localhost:8080/swagger/index.html
```

## Troubleshooting

**Services won't start:**
```bash
# Check if ports are in use
# Frontend: 3000, API: 8080, Database: 5432
docker-compose ps
docker-compose logs

# Clean up and restart
docker-compose down -v
docker-compose up
```

**Database not initializing:**
```bash
# Reset database
docker-compose down -v
docker-compose up

# Check PostgreSQL logs
docker-compose logs postgres
```

## Security Notes

- Never commit `.env.production` files with real credentials
- Use strong passwords for production databases
- Configure SSL/TLS for production deployments
- Regularly update Docker images for security patches