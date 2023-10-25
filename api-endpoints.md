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

# Endpoints

This is just a temporary place for endpoints. We will have a better documentation soon for API endpoints.

:::warning
As API is still under heavy development, endpoints could change without notice. We are still looking into the best option for API versioning, structure, etc.
:::

## Deploy Webhook

GET `<instanceUrl>/api/v1/deploy?uuid=<resourceUuid>&force=false`

Example: `https://app.coolify.io/api/v1/deploy?uuid=hg04w48`


```bash
curl -H "Authorization: Bearer 4|bBx6dwcuY4IL05SxDvUjfFs547vOgZOJTx3Fp95rd76ff2dc" https://app.coolify.io/api/v1/deploy?uuid=hg04w48
```

### Query Parameters
- `uuid`: Could be found in the URL of the resource page.
- `force`: If set to `true`, the deployment won't use cache. Default is `false`.

