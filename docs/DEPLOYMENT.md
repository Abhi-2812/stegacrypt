# StegaCrypt Deployment Guide

StegaCrypt deploys as two separate services:

- Backend on Render
- Frontend on Netlify

That split matches the project architecture:

- `backend/` is a Spring Boot API that must stay running as a server.
- `frontend/` is a Vite React app that builds to static files.

## What Is Already Configured

The repo already includes:

- `render.yaml` for the Render backend service
- `backend/Dockerfile` for the Java API container build
- `netlify.toml` for the Netlify frontend build and SPA redirect
- `frontend/.env.example` as the frontend environment variable template

## Deploy Order

Deploy in this order:

1. Deploy the backend to Render.
2. Copy the Render backend URL.
3. Add that URL to Netlify as `VITE_API_BASE_URL`.
4. Deploy the frontend to Netlify.
5. Test the live frontend against the live backend.

## Backend on Render

### Option 1: Use `render.yaml`

In Render:

1. Create a new Blueprint deployment from your Git repository.
2. Render should detect the root-level `render.yaml`.
3. Confirm the backend service settings and deploy.

The current blueprint defines:

```yaml
services:
  - type: web
    name: stegacrypt-backend
    env: docker
    rootDir: backend
    plan: free
    healthCheckPath: /api/health
    envVars:
      - key: FRONTEND_ORIGINS
        value: http://localhost:3000,http://localhost:5173,https://*.netlify.app
```

### Option 2: Create the Render service manually

Use these settings:

- Service type: `Web Service`
- Environment: `Docker`
- Root directory: `backend`
- Dockerfile path: `backend/Dockerfile` if Render asks for it
- Health check path: `/api/health`

### Render Environment Variable

Set this environment variable on Render:

```text
FRONTEND_ORIGINS=http://localhost:3000,http://localhost:5173,https://*.netlify.app
```

This allows:

- local frontend development
- Netlify production URLs
- Netlify preview deploy URLs

### Confirm the Backend Is Live

After Render deploys, copy the backend URL. It will look like:

```text
https://stegacrypt-backend.onrender.com
```

Then open:

```text
https://stegacrypt-backend.onrender.com/api/health
```

You should get a JSON response showing the API is up.

## Frontend on Netlify

The repo already includes this Netlify configuration:

```toml
[build]
  base = "frontend"
  command = "npm run build"
  publish = "dist"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

That means Netlify should use:

- Base directory: `frontend`
- Build command: `npm run build`
- Publish directory: `dist`

### Netlify Environment Variable

Before the frontend build runs, add:

```text
VITE_API_BASE_URL=https://YOUR_RENDER_BACKEND_URL/api
```

Example:

```text
VITE_API_BASE_URL=https://stegacrypt-backend.onrender.com/api
```

You can also keep the same value locally in a file based on [frontend/.env.example](/d:/MIT_WPU_ENGG/StegaCryptWeb/frontend/.env.example:1).

### Netlify Deploy Steps

In Netlify:

1. Add a new site from your Git repository.
2. Let Netlify read `netlify.toml`, or enter the same values manually.
3. Add `VITE_API_BASE_URL` in Site configuration.
4. Trigger the deployment.

After deployment, Netlify will give you a URL like:

```text
https://your-site-name.netlify.app
```

## Final Check Between Netlify and Render

After both deployments are live:

1. Open the Netlify site.
2. Try the health-dependent UI flow.
3. Generate keys.
4. Test embed and extract with a small PNG image.
5. Test login and secure chat if you want the full demo.

## If CORS Needs Tightening

The current backend config already accepts `https://*.netlify.app`, which is usually enough for both production and preview deploys.

If you want stricter CORS later, replace the wildcard with your exact site URL:

```text
FRONTEND_ORIGINS=http://localhost:3000,http://localhost:5173,https://YOUR_SITE_NAME.netlify.app
```

Redeploy the Render backend after changing that variable.

## Common Issues

- `404` on a direct frontend refresh:
  Netlify SPA routing is handled by the redirect rule in `netlify.toml`.
- Frontend loads but API calls fail:
  Check `VITE_API_BASE_URL` in Netlify and make sure it ends with `/api`.
- Browser shows CORS errors:
  Check Render's `FRONTEND_ORIGINS`.
- Backend is sleeping on the free Render plan:
  The first request after inactivity may take a little longer.
- Netlify deploy succeeds but app still points to localhost:
  Recheck the environment variable and redeploy the frontend.
