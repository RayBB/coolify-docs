---
head:
  - - meta
    - name: description
      content: Coolify Documentation
  - - meta
    - name: keywords
      content: coolify self-hosting docker kubernetes vercel netlify heroku render digitalocean aws gcp azure
  - - meta
    - name: twitter:card
      content: summary_large_image
  - - meta
    - name: twitter:site
      content: "@coolifyio"
  - - meta
    - name: twitter:title
      content: Coolify Documentation
  - - meta
    - name: twitter:description
      content: Self-hosting with superpowers.
  - - meta
    - name: twitter:image
      content: https://cdn.coollabs.io/assets/coolify/og-image-docs.png
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
      content: https://cdn.coollabs.io/assets/coolify/og-image-docs.png
---
# Multiple Servers
:::warning
This is an experimental feature.
:::

With this feature, You could deploy the same application to multiple servers, add a load balancer in front of them and you will get a highly available application.


## Requirements
- Each server should be added to Coolify, validated and reachable.
- Each server (and the optional build server) should be the same architecture (AMD64, ARM).
- You must push the built image to a Docker Registry. Coolify automates the process, you just need to [login to the registry](/docker/registry.md#docker-credentials) on the server.


## How to use?
When you configure (or already configured) an application, you selected a server / network where it deploys. This will be your main server.

Any additional servers must be set in the `Servers` menu, simply by clicking on it.

Now everytime you redeploy, restart or stop the application, the action will be done on all servers. 

If the deploy needs a build process, it will be executed on the main server (or on the build server if you have on). The deploy process will upload the built image to the Docker Registry and only after all other servers will be notified to pull and deploy this image.

## How to configure a loadbalancer?
At the moment, it is not automated. So you have to manually setup a loadbalancer. There are two ways to use.

### Port mapping to host
If you set `Ports Mappings` for your application, so one port from the docker container will mapped to a port on the host server, all you need to do is to:

1. Set all the `IP:PORT` as a destination in your loadbalancer.
2. Remove any domains from the `Domains` field in Coolify.

In this case, Coolify Proxy is not used as you can reach the application with IP:PORT

:::tip
This is super simple and effective. But keep in mind, that you need to only allow incoming connections to the selected `PORT` from the loadbalancer, otherwise everyone can reach your application directly, without the loadbalancer.
:::

### Using a domain
In this case, you need to set the **loadbalancer domain with HTTP, (not HTTPS)** in the `Domains` field, and then set the proper configuration for your loadbalancer, with SSL termination.

With this configuration, you can use several domains with one loadbalancer.

