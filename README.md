# Prepara DUST Mobile Apps PWA and Deployment

## Deployment

1. Setup a netlify.toml file to specify how the site should be build

```toml
[build]
  command = "npm run build"
  publish = "dist"
  environment = { NODE_VERSION = "18.8.0", NPM_VERSION = "8.18.0" }
```

2. Fix the refresh on `/about` (basically surfing directly to routes in SPA's)

You can create a file called `_redirects` in `dist` (needs to be taken into account when building the app) with the content below:

```
# Fixing Single Page Web app routes
/*    /index.html   200
```

Or you can add the redirect to the `netlify.toml` file:

```toml
[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
  force = false
```

More info @ [https://docs.netlify.com/routing/redirects/](https://docs.netlify.com/routing/redirects/),

### Fixing CORS on Netlify

Source: [https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

I expected us to get CORS problems here, but we don't. Backend is configured to allow all cors but that was not enough in the past.

Anyways, if problems would arise here is a Netlify solution.

```toml
[[redirects]]
  from = "/api/*"
  to = "https://dust.devbitapp.be/api/:splat"
  status = 200
  force = true
```

An asterisk indicates a splat that will match anything that follows it. You can use the splat in your rewrites.

**Make sure to place this before the `/*` redirect!**

More info @ [https://docs.netlify.com/configure-builds/file-based-configuration/#redirects](https://docs.netlify.com/configure-builds/file-based-configuration/#redirects).

Of course you will need to make your API calls to `/api` from now on and not the actual dust endpoint.

TODO: Fix this for local development

## Environment Variables

Vite: [https://vitejs.dev/guide/env-and-mode.html](https://vitejs.dev/guide/env-and-mode.html)

So you can create `.env` files for development `.env.development` with the following content for example:

```
VITE_MY_ENV_VAR="You are working in dev mode"
```

**Make sure it starts with `VITE_` so vite replaces it when building the site**.

Than we can use it inside our app:

```vue
<script setup lang="ts">
// ....

const envVar = import.meta.env.VITE_MY_ENV_VAR
</script>

<template>
  <div class="about">
    <h1>This is an about page</h1>
    <div>Environment variable: {{ envVar }}</div>

```

Of course when we deploy on netlify we need to be able to set the ENV var there too.

Netlify allows you to do just that:

![Netlify ENV vars](./img/netlify_env_vars.png)
