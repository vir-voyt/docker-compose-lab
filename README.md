# docker-compose-lab

Home lab for deploying a web application with Docker Compose, Nginx, Drupal, and PostgreSQL on Ubuntu Server.

## Goals

- Deploy a web application using Docker Compose
- Configure communication between containers
- Configure Nginx as a reverse proxy
- Connect a containerized application to PostgreSQL running on the Docker host
- Manage service configuration using environment variables
- Use bind mounts for custom configuration
- Configure persistent application data
- Understand Docker Compose networking
- Expose the website through a public IP address
- Configure HTTPS
- Verify service connectivity
- Document troubleshooting and configuration decisions

## Environment

| Component     | Configuration             |
| ------------- | ------------------------- |
| Host          | MacBook Pro M1 Pro        |
| Hypervisor    | UTM                       |
| Guest OS      | Ubuntu Server 24.04.4 LTS |
| Architecture  | ARM64 / aarch64           |
| Docker        | Docker Engine             |
| Orchestration | Docker Compose            |
| Reverse proxy | Nginx                     |
| Application   | Drupal                    |
| Database      | PostgreSQL system service |

## Architecture

```text
Client
   |
   | HTTP / HTTPS
   v
Public IP
   |
   v
Nginx container
   |
   | HTTP
   v
Drupal container
   |
   | PostgreSQL :5432
   v
PostgreSQL service
on Ubuntu host
```

Docker-related part:

```text
Ubuntu Server
│
├── PostgreSQL
│   └── TCP :5432
│
└── Docker
    └── Docker Compose
        │
        ├── nginx
        │     │
        │     v
        └── drupal
              │
              └──────> PostgreSQL on Docker host
```

## Project structure

Planned project structure:

```text
docker-compose-lab/
├── compose.yaml
├── .env
├── .env.example
├── .gitignore
├── nginx/
│   └── nginx.conf
└── README.md
```

---

## 1. Project setup

Creating the project directory:

```bash
mkdir docker-compose-lab
```

Creating the directory for Nginx configuration:

```bash
mkdir docker-compose-lab/nginx
```

Entering the project directory:

```bash
cd docker-compose-lab
```

Checking the installed Docker Compose version:

```bash
docker compose version
```

Output:

```text
Docker Compose version v5.4.0
```

Creating the initial project files:

```bash
touch compose.yaml .env .gitignore nginx/nginx.conf
```

Checking the project structure:

```bash
sudo apt install -y tree
tree -a
```

```text
.
├── .env
├── .gitignore
├── compose.yaml
└── nginx
    └── nginx.conf
```

---

## 2. PostgreSQL preparation

PostgreSQL is installed directly on the Ubuntu host rather than running inside Docker.

Checking the PostgreSQL service status:

```bash
sudo systemctl status postgresql --no-pager
```

Checking the PostgreSQL version:

```bash
psql -V
```

Checking which address and port PostgreSQL is listening on:

```bash
sudo ss -ltnp | grep postgres
```

Checking the current PostgreSQL listening configuration:

```bash
sudo -u postgres psql -c "SHOW listen_addresses;"
sudo -u postgres psql -c "SHOW port;"
```

### Database creation

Connecting to PostgreSQL:

```bash
# TODO
```

Creating a database for Drupal:

```sql
-- TODO
```

Creating a dedicated PostgreSQL user:

```sql
-- TODO
```

Granting the required privileges:

```sql
-- TODO
```

Verifying the created database and user:

```sql
-- TODO
```

---

## 3. PostgreSQL access from Docker

By default, PostgreSQL running on the host must be configured to accept connections from Docker containers.

Checking Docker networks:

```bash
# TODO
```

Determining the Docker network address range:

```bash
# TODO
```

Configuring PostgreSQL `listen_addresses`:

```text
# TODO
```

Configuring `pg_hba.conf` to allow connections from the Docker network:

```text
# TODO
```

Restarting PostgreSQL:

```bash
# TODO
```

Checking that PostgreSQL is listening on port `5432`:

```bash
# TODO
```

Testing PostgreSQL connectivity before deploying Drupal:

```bash
# TODO
```

---

## 4. Environment variables

Creating the environment file:

```bash
# TODO
```

Planned variables:

```dotenv
# TODO
```

The `.env` file contains local configuration and credentials and must not be committed to the repository.

Creating `.gitignore`:

```bash
# TODO
```

Adding `.env` to `.gitignore`:

```text
.env
```

Creating a safe example configuration:

```bash
# TODO
```

Example structure:

```dotenv
POSTGRES_DB=
POSTGRES_USER=
POSTGRES_PASSWORD=
```

---

## 5. Docker Compose configuration

Creating the Compose file:

```bash
# TODO
```

Initial services:

```yaml
# TODO
```

The Compose stack will contain:

```text
nginx
drupal
```

PostgreSQL remains a system service on the Ubuntu host.

Validating the Compose file:

```bash
# TODO
```

Checking the resolved Compose configuration:

```bash
# TODO
```

---

## 6. Drupal service

Adding the Drupal service to `compose.yaml`:

```yaml
# TODO
```

The Drupal container should not be directly exposed to the Internet.

Expected traffic flow:

```text
Nginx
   |
   v
Drupal:80
```

Configuring access to the PostgreSQL host:

```yaml
# TODO
```

Configuring persistent Drupal data:

```yaml
# TODO
```

Starting Drupal:

```bash
# TODO
```

Checking the container:

```bash
# TODO
```

Checking Drupal logs:

```bash
# TODO
```

---

## 7. Docker Compose networking

Starting the current stack:

```bash
# TODO
```

Checking Compose services:

```bash
# TODO
```

Checking Docker networks:

```bash
# TODO
```

Inspecting the network created by Docker Compose:

```bash
# TODO
```

Checking the Drupal container network configuration:

```bash
# TODO
```

Testing DNS resolution inside the Compose network:

```bash
# TODO
```

Testing access from the container to the Ubuntu host:

```bash
# TODO
```

Testing access to PostgreSQL port `5432`:

```bash
# TODO
```

---

## 8. Drupal database connection

Opening the Drupal installer:

```text
http://<SERVER_IP>:<TEMPORARY_PORT>
```

Database configuration:

```text
Database type: PostgreSQL
Database name: TODO
Database username: TODO
Database password: stored in .env
Database host: TODO
Database port: 5432
```

Completing the Drupal installation:

```text
TODO
```

Verifying that Drupal created its database tables:

```sql
-- TODO
```

---

## 9. Nginx configuration

Creating the Nginx configuration file:

```bash
# TODO
```

File:

```text
nginx/nginx.conf
```

Initial configuration:

```nginx
# TODO
```

The upstream should point to the Drupal service using its Docker Compose service name.

Expected internal traffic:

```text
nginx:80
   |
   v
drupal:80
```

---

## 10. Bind mount

The custom Nginx configuration will be stored on the host and mounted into the container.

Adding the bind mount to `compose.yaml`:

```yaml
# TODO
```

Starting Nginx:

```bash
# TODO
```

Checking the Nginx container:

```bash
# TODO
```

Checking the mounted configuration:

```bash
# TODO
```

Checking Nginx configuration syntax:

```bash
# TODO
```

---

## 11. Reverse proxy

Adding Nginx to the full Compose stack:

```yaml
# TODO
```

Only Nginx should publish the web port to the host.

Expected architecture:

```text
host:80
   |
   v
nginx:80
   |
   v
drupal:80
```

Starting the complete stack:

```bash
# TODO
```

Checking services:

```bash
# TODO
```

Testing the reverse proxy locally:

```bash
# TODO
```

Checking Nginx logs:

```bash
# TODO
```

Checking Drupal logs:

```bash
# TODO
```

---

## 12. Persistent data

Checking the storage currently used by Drupal:

```bash
# TODO
```

Adding persistent storage:

```yaml
# TODO
```

Checking Docker volumes:

```bash
# TODO
```

Inspecting the volume:

```bash
# TODO
```

Testing persistence:

1. Create or modify content in Drupal.
2. Stop and remove the containers.
3. Start the stack again.
4. Verify that the data is still available.

Stopping the stack:

```bash
# TODO
```

Starting it again:

```bash
# TODO
```

Result:

```text
TODO
```

---

## 13. Public access

Checking the server network configuration:

```bash
# TODO
```

Checking listening ports:

```bash
# TODO
```

Expected web listener:

```text
0.0.0.0:80
```

Testing locally from Ubuntu:

```bash
# TODO
```

Testing through the server IP:

```bash
# TODO
```

Testing from another machine:

```bash
# TODO
```

Expected result:

```text
http://<PUBLIC_IP>
```

The request path should be:

```text
Client
   |
   v
Public IP:80
   |
   v
Nginx
   |
   v
Drupal
   |
   v
PostgreSQL
```

---

## 14. HTTPS

HTTPS should be configured only after the website works correctly over HTTP.

Planned domain:

```text
TODO
```

DNS record:

```text
TODO
```

Expected final traffic flow:

```text
Client
   |
   | HTTPS :443
   v
Nginx
   |
   | HTTP
   v
Drupal
```

Checking DNS resolution:

```bash
# TODO
```

Obtaining a TLS certificate:

```bash
# TODO
```

Configuring Nginx for HTTPS:

```nginx
# TODO
```

Testing HTTPS:

```bash
# TODO
```

Testing HTTP to HTTPS redirect:

```bash
# TODO
```

Expected result:

```text
https://<DOMAIN>
```

---

## 15. Verification

Checking running Compose services:

```bash
# TODO
```

Checking containers:

```bash
# TODO
```

Checking Docker networks:

```bash
# TODO
```

Checking listening ports:

```bash
# TODO
```

Checking the website:

```bash
# TODO
```

Checking the reverse proxy:

```bash
# TODO
```

Checking PostgreSQL connectivity:

```bash
# TODO
```

Checking application data in PostgreSQL:

```sql
-- TODO
```

Checking Nginx logs:

```bash
# TODO
```

Checking Drupal logs:

```bash
# TODO
```

---

## 16. Stack management

Starting the stack:

```bash
docker compose up -d
```

Checking service status:

```bash
docker compose ps
```

Viewing logs:

```bash
docker compose logs
```

Following logs in real time:

```bash
docker compose logs -f
```

Viewing logs for a specific service:

```bash
docker compose logs nginx
```

```bash
docker compose logs drupal
```

Stopping the services without removing them:

```bash
docker compose stop
```

Starting stopped services:

```bash
docker compose start
```

Restarting services:

```bash
docker compose restart
```

Stopping and removing the stack:

```bash
docker compose down
```

Checking the resolved configuration:

```bash
docker compose config
```

---

# Troubleshooting

## Drupal cannot connect to PostgreSQL

Symptoms:

```text
TODO
```

Diagnosis:

```bash
# TODO
```

Things to check:

- PostgreSQL service status
- `listen_addresses`
- `pg_hba.conf`
- PostgreSQL port `5432`
- Docker network subnet
- host address used by the Drupal container
- PostgreSQL username and password
- host firewall

Solution:

```text
TODO
```

---

## Nginx returns `502 Bad Gateway`

Symptoms:

```text
TODO
```

Diagnosis:

```bash
# TODO
```

Things to check:

- Drupal container status
- Drupal logs
- Nginx logs
- Nginx upstream configuration
- Docker Compose service name
- internal container port
- Docker network connectivity

Solution:

```text
TODO
```

---

## Container cannot reach the Docker host

Symptoms:

```text
TODO
```

Diagnosis:

```bash
# TODO
```

Solution:

```text
TODO
```

---

## Website works locally but is not publicly accessible

Symptoms:

```text
TODO
```

Diagnosis:

```bash
# TODO
```

Things to check:

- Nginx container status
- Docker port publishing
- `ss -tlnp`
- Ubuntu firewall
- network/router configuration
- VPS/provider firewall if applicable
- public IP routing

Solution:

```text
TODO
```

---

## Environment variables are not resolved

Symptoms:

```text
TODO
```

Checking the resolved Compose configuration:

```bash
docker compose config
```

Solution:

```text
TODO
```

---

## Nginx configuration error

Checking configuration syntax:

```bash
# TODO
```

Checking logs:

```bash
# TODO
```

Solution:

```text
TODO
```

---

# Result

The completed lab should provide the following architecture:

```text
Internet
   |
   | HTTPS
   v
Nginx
   |
   v
Drupal
   |
   v
PostgreSQL
```

The website should be accessible through a public IP address and, after DNS and TLS configuration, through a domain over HTTPS.

The lab demonstrates practical experience with:

- Docker
- Docker Compose
- Linux containers
- Docker networking
- PostgreSQL
- Nginx
- reverse proxy configuration
- environment variables
- bind mounts
- persistent storage
- container logs
- HTTP/HTTPS
- public service deployment
- troubleshooting
