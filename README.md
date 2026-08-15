# docker-compose-lab

Home lab for Docker Compose and multi-container service deployment on Ubuntu Server.

## Goals

- Deploy multiple containers using Docker Compose
- Configure communication between containers
- Connect a containerized application to PostgreSQL running on the Docker host
- Manage service configuration with environment variables
- Use bind mounts for container configuration
- Configure Nginx as a reverse proxy
- Verify service connectivity
- Document troubleshooting and configuration decisions

## Environment

| Component | Configuration |
|-|-|
| Host | MacBook Pro M1 Pro |
| Hypervisor | UTM |
| Guest OS | Ubuntu Server 24.04.4 LTS |
| Architecture | ARM64 / aarch64 |
| Docker | Docker Engine |
| Orchestration | Docker Compose |
| Database | PostgreSQL system service |

## Planned architecture

```text
Ubuntu Server VM
├── PostgreSQL service
│
└── Docker
    └── Docker Compose
        ├── Nginx
        └── Adminer
```

Traffic flow:

```text
MacBook
   |
   | HTTP
   v
Nginx container
   |
   v
Adminer container
   |
   v
PostgreSQL service on Ubuntu
```

## Planned structure

```text
docker-compose-lab/
├── compose.yaml
├── .env
└── nginx/
    └── nginx.conf
```

## 1. Project setup

Creating the project directory:

```bash
mkdir docker-compose-lab
```

Creating the initial project structure:

```bash
mkdir docker-compose-lab/nginx
```

Checking the installed Docker Compose version:

```bash
docker compose version
```

```text
Docker Compose version v5.4.0
```

## 2. Docker Compose

Creating the Compose file:

```bash
# command
```

Defining the initial Adminer service:

```yaml
# configuration
```

Validating the Compose configuration:

```bash
# command
```

Starting the stack:

```bash
# command
```

Checking running services:

```bash
# command
```

Stopping the stack:

```bash
# command
```

## 3. PostgreSQL connection

Checking the PostgreSQL listening address:

```bash
# command
```

Configuring PostgreSQL to accept connections from the Docker network:

```bash
# command
```

Connecting Adminer to PostgreSQL running on the Ubuntu host:

```text
# connection parameters
```

Creating a test table:

```sql
# query
```

Adding test data:

```sql
# query
```

Verifying the stored data:

```sql
# query
```

## 4. Networking

Checking networks created by Docker Compose:

```bash
# command
```

Inspecting the Compose network:

```bash
# command
```

Checking container network configuration:

```bash
# command
```

Testing access from the Adminer container to the Docker host:

```bash
# command
```

## 5. Environment variables

Creating the environment file:

```bash
# command
```

Defining configuration variables:

```text
# variables
```

Using the variables in `compose.yaml`:

```yaml
# configuration
```

Validating the resolved Compose configuration:

```bash
# command
```

## 6. Bind mounts

Creating the Nginx configuration:

```bash
# command
```

Mounting the configuration file into the Nginx container:

```yaml
# configuration
```

Verifying the mounted configuration:

```bash
# command
```

## 7. Reverse proxy

Adding Nginx to the Compose stack:

```yaml
# configuration
```

Configuring Nginx to proxy requests to Adminer:

```nginx
# configuration
```

Starting the complete stack:

```bash
# command
```

Testing access through Nginx:

```bash
# command
```

## 8. Verification

Checking running Compose services:

```bash
# command
```

Verifying access to Adminer through Nginx:

```bash
# command
```

Verifying access from Adminer to PostgreSQL:

```sql
# query
```

## Troubleshooting

Problems encountered during deployment and their solutions will be documented here.
