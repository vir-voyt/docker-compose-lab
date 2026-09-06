# docker-compose-lab

Home lab for deploying a web application with Docker Compose, Nginx, Drupal, and PostgreSQL on Ubuntu Server.

## Goals

- Deploy a web application using Docker Compose
- Configure communication between containers
- Connect a containerized application to PostgreSQL running on the Docker host
- Understand Docker Compose networking
- Manage service configuration using environment variables
- Configure persistent Drupal data
- Use bind mounts for custom configuration
- Configure Nginx as a reverse proxy
- Expose the website over HTTP
- Configure public access where available
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
Server IP
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
sudo -u postgres psql
```

Creating a database for Drupal:
```sql
create database drupal_base;
```

Creating a dedicated PostgreSQL user:
```sql
create user drupal_user with password 'p@ssw0rd';
```

Assigning database ownership:
```sql
alter database drupal_base owner to drupal_user;
```

Verifying the created database and user:
```sql
\l
\du
\q
```

---

## 3. Environment variables

Editing the environment file:
```bash
vim .env
```

Defining local configuration:
```dotenv
DRUPAL_IMAGE=drupal:latest
NGINX_IMAGE=nginx:alpine
HTTP_PORT=80
POSTGRES_HOST=host.docker.internal
POSTGRES_PORT=5432
POSTGRES_DB=drupal_base
POSTGRES_USER=drupal_user
POSTGRES_PASSWORD=p@ssw0rd
```

The .env file contains local configuration and credentials and must not be committed to the repository.

Adding .env to .gitignore:
```bash
vim .gitignore
```
```text
.env
```

Creating a safe example configuration:
```bash
cp .env .env.example
vim .env.example
```
```dotenv
DRUPAL_IMAGE=drupal:latest
NGINX_IMAGE=nginx:alpine
HTTP_PORT=80
POSTGRES_HOST=host.docker.internal
POSTGRES_PORT=5432
POSTGRES_DB=drupal_base
POSTGRES_USER=drupal_user
POSTGRES_PASSWORD=
```

---

## 4. Initial Docker Compose configuration

Creating the initial Compose configuration:
```bash
vim compose.yaml
```

At this stage, the stack contains only the Drupal service:
```text
Docker Compose
└── drupal
```

Adding the Drupal service:
```yaml
services:
  drupal:
    image: ${DRUPAL_IMAGE}
```

The Drupal container should not publish a web port directly because Nginx will become the external entry point later.

Configuring access from Drupal to the Ubuntu host:
```yaml
services:
  drupal:
    image: ${DRUPAL_IMAGE}
    extra_hosts:
      - "host.docker.internal:host-gateway"
```

Configuring persistent Drupal application data:
```yaml
services:
  drupal:
    image: ${DRUPAL_IMAGE}
    extra_hosts:
      - "host.docker.internal:host-gateway"
    volumes:
      - drupal_sites:/var/www/html/sites
      - drupal_modules:/var/www/html/modules
      - drupal_themes:/var/www/html/themes
volumes:
  drupal_sites:
  drupal_modules:
  drupal_themes:
```

PostgreSQL storage is not configured here because PostgreSQL runs directly on the Ubuntu host.

Checking the resolved configuration:
```bash
sudo docker compose config
```

---

## 5. First Compose start and networking

Starting the Drupal service:
```bash
sudo docker compose up -d
```

Checking running Compose projects:
```bash
sudo docker compose ls
```

Checking running containers:
```bash
sudo docker ps
```

Docker Compose creates a project network when the service is started.

Checking Docker networks:
```bash
sudo docker network ls
```

Inspecting the Compose network:
```bash
sudo docker network inspect docker-compose-lab_default
```

Determining the Docker network address range:
```text
Subnet: 172.18.0.0/16
Gateway: 172.18.0.1
```

Checking the Drupal container network configuration:
```bash
sudo docker container inspect docker-compose-lab-drupal-1
```

Container IP:
```text
172.18.0.2
```

Checking external name resolution from the Drupal container:
```bash
sudo docker exec docker-compose-lab-drupal-1 getent hosts google.com
```

Resolving the Ubuntu host name from the Drupal container:
```bash
sudo docker exec docker-compose-lab-drupal-1 getent hosts host.docker.internal
```

Output:
```text
172.17.0.1      host.docker.internal
```

---

## 6. PostgreSQL access from Docker

Now that the Compose network exists and its subnet is known, PostgreSQL can be configured to accept connections from it.

PostgreSQL currently listens only on `127.0.0.1:5432`. Access from Docker remains TODO.

Checking the active PostgreSQL configuration files:
```bash
# TODO
```

Configuring PostgreSQL `listen_addresses` to accept connections on `172.17.0.1`:
```text
# TODO
```

Configuring `pg_hba.conf` to allow connections from the Compose subnet `172.18.0.0/16`:
```text
# TODO
```

The PostgreSQL port should be available to the required Docker network but should not be unnecessarily exposed to the Internet.

Restarting PostgreSQL:
```bash
# TODO
```

Checking PostgreSQL status:
```bash
# TODO
```

Checking that the PostgreSQL listener covers `172.17.0.1:5432`:
```bash
# TODO
```

Testing access to PostgreSQL port `5432` from Docker:
```bash
# TODO
```

Testing authentication using the Drupal database credentials:
```bash
# TODO
```

---

## 7. Nginx configuration

Creating the Nginx configuration:
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

The upstream should point to the Drupal Compose service by its service name.

Internal traffic:
```text
nginx:80
   |
   v
drupal:80
```

---

## 8. Bind mount and reverse proxy

Adding Nginx to `compose.yaml`:
```yaml
# TODO
```

Mounting the custom Nginx configuration into the container:
```yaml
# TODO
```

Only Nginx should publish the web port to the Ubuntu host.

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

Checking the mounted Nginx configuration:
```bash
# TODO
```

Checking Nginx configuration syntax:
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

## 9. Drupal installation and database connection

Opening the Drupal installer through Nginx:
```text
http://<SERVER_IP>
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

Verifying that the Drupal website is available:
```text
TODO
```

Connecting to PostgreSQL:
```bash
# TODO
```

Verifying that Drupal created its database tables:
```sql
-- TODO
```

---

## 10. Persistent data verification

PostgreSQL data is stored by the PostgreSQL service on the Ubuntu host.

This section verifies persistence of Drupal files managed by Docker.

Checking configured Docker volumes:
```bash
# TODO
```

Inspecting the Drupal volume:
```bash
# TODO
```

Creating or modifying content in Drupal:
```text
TODO
```

Stopping and removing the Compose containers:
```bash
# TODO
```

Checking that the containers were removed:
```bash
# TODO
```

Starting the stack again:
```bash
# TODO
```

Verifying that the Drupal configuration and content are still available:
```text
TODO
```

Result:
```text
TODO
```

---

## 11. Network access

Checking the Ubuntu network configuration:
```bash
# TODO
```

Checking listening ports:
```bash
# TODO
```

Expected HTTP listener:
```text
0.0.0.0:80
```

Testing from the Ubuntu VM:
```bash
# TODO
```

Testing from the MacBook:
```bash
# TODO
```

Expected result on the local network:
```text
http://<SERVER_IP>
```

Request path:
```text
MacBook
   |
   v
Ubuntu VM:80
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

## 12. Public access

Public access depends on the network configuration outside the Ubuntu VM.

Checking the current public IP:
```bash
# TODO
```

Checking whether inbound connections can reach the server:
```text
TODO
```

Possible requirements:
```text
Public IP
Router/NAT port forwarding
Host firewall configuration
Provider firewall configuration
```

Testing external HTTP access:
```text
http://<PUBLIC_IP>
```

Result:
```text
TODO
```

If the environment does not provide a reachable public IP, this stage can be reproduced later on a VPS.

---

## 13. HTTPS

HTTPS should be configured only after HTTP access works correctly.

Planned domain:
```text
TODO
```

DNS record:
```text
TODO
```

Expected DNS mapping:
```text
domain
   |
   v
public IP
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

Expected traffic:
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

## 14. Verification

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

Checking PostgreSQL connectivity from Docker:
```bash
# TODO
```

Checking Drupal data in PostgreSQL:
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

## 15. Stack management

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
- Compose network subnet
- Docker host address used by Drupal
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

Things to check:

- Compose network
- host gateway configuration
- PostgreSQL listening address
- `pg_hba.conf`
- firewall

Solution:
```text
TODO
```

---

## Website works on Ubuntu but not from the MacBook

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
- UTM network configuration

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

- public IP availability
- router/NAT configuration
- port forwarding
- host firewall
- provider firewall
- CGNAT
- public routing

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
Client
   |
   | HTTP / HTTPS
   v
Nginx container
   |
   v
Drupal container
   |
   v
PostgreSQL service
on Ubuntu host
```

The website should first be accessible from the host network over HTTP.

Where a reachable public IP and domain are available, the same application can then be exposed publicly over HTTPS.

The lab demonstrates practical experience with:

- Docker
- Docker Compose
- Linux containers
- Docker networking
- container-to-host networking
- PostgreSQL
- Drupal
- Nginx
- reverse proxy configuration
- environment variables
- bind mounts
- persistent storage
- container logs
- HTTP/HTTPS
- service deployment
- troubleshooting
