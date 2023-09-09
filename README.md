# Vaultwarden-Deployment

This repository contains a Docker Compose configuration for securely deploying Vaultwarden, Traefik, and basic auth (Authelia coming soon) for dashboard/admin with TLS certificate support.

This configuration was intended to deploy vaultwarden and traefik on subdomains of the same domain. Configuring separate domains for each service should work but has not been tested.


## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Setup](#setup)
3. [Usage](#usage)
4. [Credits](#credits)
5. [License](#license)

## Prerequisites

Before you begin, ensure you have the following prerequisites:

- Docker Compose installed on your server
- A domain name with DNS records pointing to your server
- [Your own SSL/TLS certificates](#tls-certificates)


## Setup

### Clone the repository

```bash
# clone the repository on your server
git clone https://github.com/ace6807/vaultwarden-deployment.git

# move into the repository directory
cd vaultwarden-deployment
```

### TLS Certificates

Before deploying, make sure to provide your own SSL/TLS certificates for secure connections. Place your certificate and private key in `certs/orign.cert` and `keys/origin.key` respectively.

Note: Support for Let's Encrypt automatic certificate generation and renewal may be added in the future.

### Basic Auth
To configure basic auth for Traefik dashboard and Vaultwarden admin, you'll need to populate `traefik-config/usersfile`. Each line should contain a username and hashed password separated by a colon. More info can be found in the [Traefik documentation](https://doc.traefik.io/traefik/middlewares/http/basicauth/).

To generate a hashed password, run the following command and follow the prompts:

```bash
htpasswd -nb <username> <password>
```

### Environment Variables

To configure your deployment, you'll need to set the environment variables in `.env`. Examples can be found in `.env.example`.

The following variables are required:

- `TRAEFIK_DOMAIN`: The domain where your Traefik dashboard will be accessed. (scheme should be omitted)
- `VW_DOMAIN`: The domain where your Vaultwarden instance will be accessed. (scheme should be omitted)
- `VW_ADMIN_TOKEN`: The hashed admin token for your Vaultwarden instance.

NOTE: To generate a secure `VW_ADMIN_TOKEN`, run the following and follow the prompts:

```bash
docker run --rm -it vaultwarden/server /vaultwarden hash --preset owasp
```
see [Vaultwarden Documentation](https://github.com/dani-garcia/vaultwarden/wiki/Enabling-admin-page#secure-the-admin_token) for additional information and options.


## Usage

### Deploy
Run the following command to start the deployment:

```bash
docker compose up -d
```

### Configure Vaultwarden
Vaultwarden [recoomends](https://github.com/dani-garcia/vaultwarden/wiki/Configuration-overview) using either environment variables OR the admin panel to configure your instance, but not both. This repository currently configures only the admin token via environment variable to avoid conflicts. The rest of the configurtion should be done via the admin panel. This is likely to change in the future to use environment variables for all configuration as recommended by Vaultwarden.

To access the admin panel, navigate to `https://<VW_DOMAIN>/admin` and enter your admin token. Login and configure your instance as desired.


## TODO

Check out the [TODO.md](TODO.md) file for a list of planned features and improvements.

## Credits

- [Authalia](https://github.com/authelia/authelia): Authentication service for Traefik dashboard and Vaultwarden admin.
- [Traefik](https://traefik.io/): Reverse proxy and load balancer.
- [Vaultwarden](https://github.com/dani-garcia/vaultwarden): Self-hosted Bitwarden password manager.


## License

© 2023 Andrew Yatsko

This project is licensed under the [MIT License](LICENSE.md) - see [LICENSE.md](LICENSE.md) file for details.