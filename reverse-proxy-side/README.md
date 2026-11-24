# Reverse Proxy Server

Redacted config from my reverse proxy server.
It sits in front of my home server and routes public traffic to it.

> All secrets, IPs, domains and keys are replaced with placeholders.

## Stack

| Service | Purpose |
|---|---|
| nginx | Reverse proxy, TLS termination |
| docker-socket-proxy | Restricted Docker API access for WUD |
| wud | Watches containers, auto-updates and notifies via Discord |

## Structure

```
reverse-proxy-side
├── docker
│   ├── appdata
│   │   └── tailscale
│   │       └── state
│   └── compose
│       ├── proxy
│       │   ├── compose.yml
│       │   └── nginx.conf
│       └── tunnel
│           └── compose.yml
└── README.md
```

## Setup

1. Copy the env file and fill in values:
   ```bash
   cp .env.example .env
   ```
2. Place TLS certs in `/etc/nginx/ssl`.
3. Start the stack:
   ```bash
   sudo docker compose up -d
   ```

## Update Policy

| Container | Behaviour |
|---|---|
| nginx | Auto-updates via WUD |
| docker-socket-proxy | Notify only, update manually |
| wud | Notify only, update manually |

Manual update:
```bash
sudo docker compose pull <service>
sudo docker compose up -d <service>
```

## Security Notes

- WUD never mounts `docker.sock` directly. It goes through the socket proxy.
- The socket proxy sits on an internal network with no internet access.
- Port 2375 is not published to the host.
- Real `.env` files are gitignored.