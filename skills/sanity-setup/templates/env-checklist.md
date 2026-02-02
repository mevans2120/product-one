# Sanity Environment Variables Checklist

Use this checklist when deploying to Vercel or setting up a new environment.

## Required Variables

### Public Variables (NEXT_PUBLIC_*)
These are exposed to the browser and safe to share.

| Variable | Description | Example |
|----------|-------------|---------|
| `NEXT_PUBLIC_SANITY_PROJECT_ID` | Your Sanity project ID | `abc123xy` |
| `NEXT_PUBLIC_SANITY_DATASET` | Dataset name | `production` |
| `NEXT_PUBLIC_SANITY_API_VERSION` | API version (date format) | `2024-01-01` |
| `NEXT_PUBLIC_SITE_URL` | Your production URL | `https://example.com` |

### Private Variables (Server-side only)
These contain secrets and must never be exposed to the browser.

| Variable | Description | How to Get |
|----------|-------------|------------|
| `SANITY_API_TOKEN` | Read token for preview | Sanity Dashboard → API → Tokens |
| `SANITY_REVALIDATE_SECRET` | Webhook secret | Generate with `openssl rand -base64 32` |

## Getting Values

### Project ID
1. Go to [sanity.io/manage](https://www.sanity.io/manage)
2. Select your project
3. Copy project ID from URL or settings

### Dataset
Usually `production` unless you created a custom dataset.

### API Token
1. Go to [sanity.io/manage](https://www.sanity.io/manage)
2. Select your project → API → Tokens
3. Click "Add API token"
4. Name: "Next.js Read Token"
5. Permissions: Viewer (read-only)
6. Copy the token (you can't see it again!)

### Revalidate Secret
Generate a secure random string:
```bash
openssl rand -base64 32
```

## Vercel Configuration

### All Environments (Production, Preview, Development)
- `NEXT_PUBLIC_SANITY_PROJECT_ID`
- `NEXT_PUBLIC_SANITY_DATASET`
- `NEXT_PUBLIC_SANITY_API_VERSION`
- `SANITY_API_TOKEN`
- `SANITY_REVALIDATE_SECRET`

### Production Only
- `NEXT_PUBLIC_SITE_URL` = `https://your-domain.com`

### Preview (Optional - Vercel auto-sets preview URLs)
- `NEXT_PUBLIC_SITE_URL` can be left blank or set dynamically

## Local Development (.env.local)

```bash
# Sanity Configuration
NEXT_PUBLIC_SANITY_PROJECT_ID=your-project-id
NEXT_PUBLIC_SANITY_DATASET=production
NEXT_PUBLIC_SANITY_API_VERSION=2024-01-01

# Server-side secrets
SANITY_API_TOKEN=skXXXXXXXXXXXXXXXXXXXXXXX
SANITY_REVALIDATE_SECRET=your-random-secret

# Local development
NEXT_PUBLIC_SITE_URL=http://localhost:3000
```

## Verification Checklist

After setting environment variables:

- [ ] Studio loads at `/studio` without errors
- [ ] Draft mode toggle works (check Network tab for cookies)
- [ ] Presentation mode preview loads correctly
- [ ] Published content appears on production
- [ ] Webhook triggers revalidation (test by publishing content)

## Common Issues

### "Invalid token"
- Token may have expired
- Token may not have read permissions
- Wrong environment (preview vs production)

### "CORS error"
- Add your domain to Sanity CORS origins
- Don't forget `*.vercel.app` for preview deployments
- Enable credentials for each origin

### "Webhook not firing"
- Check webhook URL includes the secret
- Verify secret matches `SANITY_REVALIDATE_SECRET`
- Check webhook is enabled in Sanity dashboard

### "Draft mode not working"
- Verify `SANITY_API_TOKEN` is set
- Check token has read permissions
- Verify draft mode cookie is being set
