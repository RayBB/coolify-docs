---
head:
  - - meta
    - name: description
      content: Coolify - Wildcard SSL Certificates
  - - meta
    - name: keywords
      content: coolify self-hosting github github-actions docker kubernetes vercel netlify heroku render digitalocean aws gcp azure wildcard ssl certificates
  - - meta
    - name: twitter:card
      content: summary_large_image
  - - meta
    - name: twitter:site
      content: "@coolifyio"
  - - meta
    - name: twitter:title
      content: Coolify - Wildcard SSL Certificates
  - - meta
    - name: twitter:description
      content: Self-hosting with superpowers.
  - - meta
    - name: twitter:image
      content: https://coolcdn.b-cdn.net/assets/coolify/wildcard-certificates-og-image.png
  - - meta
    - property: og:type
      content: website
  - - meta
    - property: og:url
      content: https://coolify.io
  - - meta
    - property: og:title
      content: Coolify
  - - meta
    - property: og:description
      content: Self-hosting with superpowers.
  - - meta
    - property: og:site_name
      content: Coolify
  - - meta
    - property: og:image
      content: https://coolcdn.b-cdn.net/assets/coolify/wildcard-certificates-og-image.png
---

# Wildcard SSL Certificates

This guide will help you to configure wildcard subdomain redirects (with Traefik wildcard certificates) in Coolify.

## Prerequisites
- You need to have a domain name and a DNS provider that supports wildcard subdomains. 
- You need to use [dnsChallenge](https://doc.traefik.io/traefik/https/acme/#dnschallenge) in Traefik to get wildcard certificates from Let's Encrypt.
- You need to use one of the supported DNS [providers](https://doc.traefik.io/traefik/https/acme/#providers).

## Configuration
1. Setup your wildcard subdomain DNS records, `*.coolify.io`.
2. Go to your Proxy settings (Servers / Proxy menu) and add the following configuration based on your [providers](https://doc.traefik.io/traefik/https/acme/#providers). The example will use `Hetzner` as a provider.

The important part is highlighted in the example below.

```bash{10-11,40-44,51-53}
version: '3.8'
networks:
  coolify:
    external: true
services:
  traefik:
    container_name: coolify-proxy
    image: 'traefik:v2.10'
    restart: unless-stopped
    environment:
      - HETZNER_API_KEY=<API Key>
    extra_hosts:
      - 'host.docker.internal:host-gateway'
    networks:
      - coolify
    ports:
      - '80:80'
      - '443:443'
      - '8080:8080'
    healthcheck:
      test: 'wget -qO- http://localhost:80/ping || exit 1'
      interval: 4s
      timeout: 2s
      retries: 5
    volumes:
      - '/var/run/docker.sock:/var/run/docker.sock:ro'
      - '/data/coolify/proxy:/traefik'
    command:
      - '--ping=true'
      - '--ping.entrypoint=http'
      - '--api.dashboard=true'
      - '--api.insecure=false'
      - '--entrypoints.http.address=:80'
      - '--entrypoints.https.address=:443'
      - '--entrypoints.http.http.encodequerysemicolons=true'
      - '--entrypoints.https.http.encodequerysemicolons=true'
      - '--providers.docker.exposedbydefault=false'
      - '--providers.file.directory=/traefik/dynamic/'
      - '--providers.file.watch=true'
      - '--certificatesresolvers.letsencrypt.acme.httpchallenge=true'
      - '--certificatesresolvers.letsencrypt.acme.httpchallenge.entrypoint=http'
      - '--certificatesresolvers.letsencrypt.acme.dnschallenge.provider=hetzner'
      - '--certificatesresolvers.letsencrypt.acme.dnschallenge.delaybeforecheck=0'
      - '--certificatesresolvers.letsencrypt.acme.storage=/traefik/acme.json'
      - '--providers.docker=true'
    labels:
      - traefik.enable=true
      - traefik.http.routers.traefik.entrypoints=http
      - traefik.http.routers.traefik.middlewares=traefik-basic-auth@file
      - traefik.http.routers.traefik.service=api@internal
      - traefik.http.routers.traefik.tls.certresolver=letsencrypt
      - traefik.http.routers.traefik.tls.domains[0].main=coolify.io
      - traefik.http.routers.traefik.tls.domains[0].sans=*.coolify.io
      - traefik.http.services.traefik.loadbalancer.server.port=8080
      - traefik.http.middlewares.redirect-to-https.redirectscheme.scheme=https
      - traefik.http.middlewares.gzip.compress=true
```
> You can also set `env_file` instead of `environment` in the example above, but then you need to create a `.env` file with the `HETZNER_API_KEY` variable on the server.

> Change `--certificatesresolvers.letsencrypt.acme.dnschallenge.provider=hetzner` to your provider.

Now you have two options to configure your wildcard subdomain for your resources.

### Normal
If you would like to use one (wildcard) certificate for all of your resources, you can use this option. 

It is useful, because Traefik do not need to generate a new certificate for every resource, so new deployments will be available immediately without waiting for the certificate generation.

- In your application, set your FQDN to a subdomain you would like to use: `https://example.coolify.io`.
  
```bash
traefik.enable=true
traefik.http.routers.fowo4wg-0-https.rule=Host(`example.coolify.io`) && PathPrefix(`/`)
traefik.http.routers.fowo4wg-0-https.entryPoints=https
traefik.http.routers.fowo4wg-0-https.middlewares=gzip
traefik.http.routers.fowo4wg-0-https.service=fowo4wg-0-https
traefik.http.services.fowo4wg-0-https.loadbalancer.server.port=80
traefik.http.routers.fowo4wg-0-https.tls=true
traefik.http.routers.fowo4wg-0-https.tls.certresolver=letsencrypt
traefik.http.routers.fowo4wg-0-http.rule=Host(`example.coolify.io`) && PathPrefix(`/`)
traefik.http.routers.fowo4wg-0-http.entryPoints=http
traefik.http.routers.fowo4wg-0-http.middlewares=redirect-to-https
```

### SaaS
Redirect all subdomains to one application. You can use this option if you want to use Coolify as a SaaS provider.

- In your application, leave the FQDN configuration `empty`.
- Add the following custom label configuration:
  
```bash
traefik.enable=true
traefik.http.routers.fowo4wg-0-https.rule=HostRegexp(`{subdomain:[a-zA-Z0-9-]+}.coolify.io`)
traefik.http.routers.fowo4wg-0-https.entryPoints=https
traefik.http.routers.fowo4wg-0-https.middlewares=gzip
traefik.http.routers.fowo4wg-0-https.service=fowo4wg-0-https
traefik.http.services.fowo4wg-0-https.loadbalancer.server.port=80
traefik.http.routers.fowo4wg-0-https.tls=true
traefik.http.routers.fowo4wg-0-https.tls.certresolver=letsencrypt
traefik.http.routers.fowo4wg-0-http.rule=HostRegexp(`{subdomain:[a-zA-Z0-9-]+}.coolify.io`)
traefik.http.routers.fowo4wg-0-http.entryPoints=http
traefik.http.routers.fowo4wg-0-http.middlewares=redirect-to-https
```




> `fowo4wg-0-https` and `fowo4wg-0-http` should be unique between your applications. It can be anything, but it should be unique.
 
> `traefik.http.routers.fowo4wg-0-https.tls.certresolver` should be the same as your `certresolver` name in Traefik proxy configuration, by default `letsencrypt`.

> `traefik.http.services.fowo4wg-0-https.loadbalancer.server.port` should be the same as your application listens on. Port 80 if you use a static deployment.


::: danger
You cannot use both configurations (Normal & SaaS) at the same time on one server.
:::