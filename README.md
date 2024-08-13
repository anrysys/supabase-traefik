# Supabase Docker with Traefik for deploy production

Note: this setup is unofficial. It is supported by the Supabase community only.

This guide is covering a self-hosted Supabase setup with [Traefik](https://github.com/traefik/traefik) as a reverse proxy.

Please make sure you read the [self-hosting guide](https://supabase.io/docs/guides/self-hosting#running-supabase).

## Setup Instructions

Change the values ​​in all files that start with `YOUR-`
Example:
      `YOUR-TOKEN-FROM-CLOUDFLARE`
      `YOU-EMAIL-FROM-CLOUDFLARE@gmail.com`
      `YOUR-DOMAIN.com`

### Cloning the Repository

First, clone this repository:

```bash
git clone --depth 1 https://github.com/supabase-community/supabase-traefik
```

Navigate to the repository folder:
```bash
cd supabase-traefik
```

### Setting Up Traefik

If you haven't set up Traefik before, navigate to the Traefik directory:

```bash
cd traefik
```

Copy the example environment variables:

```bash
cp .env.example .env
```

In the `.env`, replace all the variable values with your own.


After configuring all the files, you can start Traefik using Docker Compose:

```bash
docker-compose up -d
```

### Setting Up Supabase

Get the Supabase code by cloning the Supabase repository:

```bash
git clone --depth 1 https://github.com/supabase/supabase
```

Navigate to the Docker folder:

```bash
cd supabase/docker
```

Copy the example environment variables:

```bash
cp .env.example .env
```

In the `docker-compose.yml` file, add the following to each service:

```yaml
networks:
  - supabase
```

Change the network name to match the one used by Traefik if necessary.

After configuring all the files, you can start the Supabase services using Docker Compose:

```bash
docker-compose up -d
```

Change `Log level INFO|DEBUG|ERROR` in file https://github.com/anrysys/supabase-traefik/blob/main/traefik/traefik/traefik.yml

---

### Set password (for file .htpasswd)

1. Generate a hashed password (enter your password after entering the command):

```bash
hashed_password=$(openssl passwd -apr1)
```
2. Saving a file with a hashed password:

```bash
echo "YOUR-LOGIN:$hashed_password" > ./traefik/.htpasswd
sudo chown root:root ./traefik/.htpasswd
sudo chmod 600 ./traefik/.htpasswd
```
Here’s the translated description of the interaction schema between clients, Traefik, Supabase, and Kong:

### Textual Interaction Schema:

1. **Clients (Browser, Mobile Application, Other Service)**:
   - At the top of the schema, there are various clients sending requests. These can be browsers, mobile applications, or other services.

2. **Internet**:
   - Requests from clients first go through the Internet. This is the level where requests are directed to the public IP addresses of your service.

3. **Traefik (Reverse Proxy and Load Balancer)**:
   - After passing through the Internet, requests reach Traefik. Traefik serves as a reverse proxy and load balancer. It receives all incoming requests and decides where to route them next, based on the configuration.

4. **Supabase Instances**:
   - Traefik distributes requests across multiple Supabase instances, which may be deployed in your cluster or in the cloud. The schema presents three Supabase instances: **Supabase Instance 1**, **Supabase Instance 2**, and **Supabase Instance 3**. Each instance can reside on a separate server or container.

5. **Kong (API Gateway)**:
   - Inside each Supabase instance, Kong operates as an API Gateway. It manages the requests coming through Traefik, applying security policies, authentication, and other functions before forwarding the requests to internal services.

6. **Internal Services (PostgreSQL, Authentication, Storage, etc.)**:
   - After processing by Kong, the requests are forwarded to the appropriate internal services of Supabase, such as the PostgreSQL database, authentication service, file storage, and others. These services perform the core processing of the requests and return responses back through Kong to the clients.

### Visual Structure:

```
Clients (Browser, Mobile Application, Other Service)
       |
       v
Internet
       |
       v
Traefik (Reverse Proxy and Load Balancer)
       |
       v
Supabase Instance 1  Supabase Instance 2  Supabase Instance 3
       |                    |                    |
       v                    v                    v
Kong (API Gateway)  Kong (API Gateway)  Kong (API Gateway)
       |                    |                    |
       v                    v                    v
PostgreSQL, Authentication, Storage, etc.
```

This schema helps illustrate how requests from clients pass through the Internet, reach Traefik for routing and load balancing, are then processed within Supabase using Kong, and finally are directed to the internal services for final processing.


