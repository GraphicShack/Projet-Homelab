# Nginx Proxy Manager

Nginx Proxy Manager provides a graphical interface for reverse-proxy configuration.

Instead of remembering multiple service addresses and ports, internal names can point to Nginx Proxy Manager, which forwards requests to the correct backend service.

Example concept:

```text
service.example.internal
        -> Nginx Proxy Manager
        -> backend-service:port
```

## Deployment

Nginx Proxy Manager runs in Docker inside a dedicated LXC.
