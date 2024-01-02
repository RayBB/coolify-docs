---
head:
  - - meta
    - name: description
      content: Coolify - Basic Auth with Traefik
  - - meta
    - name: keywords
      content: coolify self-hosting github github-actions docker kubernetes vercel netlify heroku render digitalocean aws gcp azure basic auth traefik
  - - meta
    - name: twitter:card
      content: summary_large_image
  - - meta
    - name: twitter:site
      content: "@coolifyio"
  - - meta
    - name: twitter:title
      content: Coolify - Basic Auth with Traefik
  - - meta
    - name: twitter:description
      content: Self-hosting with superpowers.
  - - meta
    - name: twitter:image
      content: https://coolcdn.b-cdn.net/assets/coolify/basic-auth-with-traefik-og-image.png
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
      content: https://coolcdn.b-cdn.net/assets/coolify/basic-auth-with-traefik-og-image.png
---

# Basic Auth with Traefik

This guide will help you to configure basic auth in Coolify & Traefik.

All you need to do is to set custom labels on your application.

```bash {7-8}
traefik.http.middlewares.custom-auth.basicauth.users=test:$$2y$$12$$ci.4U63YX83CwkyUrjqxAucnmi2xXOIlEF6T/KdP9824f1Rf1iyNG
traefik.http.routers.q04o0gk-0-http.middlewares=custom-auth
```
In the example above, we are using `test` as username and `test` as password.
- `custom-auth` is the name of the middleware. You can change it to whatever you want. 


## How to generate user/password?
You need to set your username and password in the `users` label. You can generate your password with [Online htpasswd generator](https://www.web2generators.com/apache-tools/htpasswd-generator) or [htpasswd](https://httpd.apache.org/docs/current/programs/htpasswd.html) command.

```bash
htpasswd -nbB test test
```