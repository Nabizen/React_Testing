# Vercel Deployment Checklist

## Overview
This project consists of:
- **Client**: React + Vite frontend (static site)
- **Server**: Express.js backend (serverless functions)

## Pre-Deployment Checklist

### 1. Environment Variables Setup

#### Client Environment Variables (in Vercel Dashboard)
- [ ] `VITE_LOCAL_HOST` - Set to your deployed server API URL (e.g., `https://your-server.vercel.app`)
  - **Important**: This must be set before building the client, as Vite environment variables are embedded at build time

#### Server Environment Variables (in Vercel Dashboard)
- [ ] `MONGO_URI` - Your MongoDB connection string (MongoDB Atlas recommended for production)
- [ ] `JWT_SECRET` - Secret key for JWT token signing (use a strong, random string)
- [ ] `PORT` - Optional (Vercel will handle this automatically)

### 2. MongoDB Setup
- [ ] Ensure MongoDB Atlas cluster is set up and accessible
- [ ] Whitelist Vercel's IP ranges (or use `0.0.0.0/0` for all IPs - less secure but easier)
- [ ] Verify connection string includes authentication credentials
- [ ] Test database connection with production credentials

### 3. Server Configuration
- [ ] Review `server/vercel.json` - ensure it's configured correctly for serverless functions
- [ ] Verify server entry point (`server/index.js`) exports the Express app correctly (✅ already done)
- [ ] Ensure all server dependencies are in `server/package.json`
- [ ] Test that server can start without errors locally

### 4. Client Configuration
- [ ] Review `client/vercel.json` - ensure SPA routing is configured correctly
- [ ] Verify `client/vite.config.js` is set up correctly
- [ ] Ensure build output directory is `dist` (Vite default)
- [ ] Test build locally: `cd client && npm run build`

### 5. CORS Configuration
- [ ] Update server CORS to allow your Vercel frontend domain
  - Currently set to `origin: "*"` which works but is less secure
  - Consider restricting to specific domains in production

### 6. Build Settings (Vercel Dashboard)

#### For Client Deployment:
- [ ] **Root Directory**: `client`
- [ ] **Build Command**: `npm run build`
- [ ] **Output Directory**: `dist`
- [ ] **Install Command**: `npm install`
- [ ] **Framework Preset**: Vite

#### For Server Deployment (separate project):
- [ ] **Root Directory**: `server`
- [ ] **Build Command**: (leave empty or `npm install`)
- [ ] **Output Directory**: (leave empty)
- [ ] **Install Command**: `npm install`
- [ ] **Framework Preset**: Other

### 7. Deployment Strategy

**Option A: Two Separate Vercel Projects (Recommended)**
- Deploy client and server as separate Vercel projects
- Easier to manage, scale, and update independently
- Client gets a clean URL (e.g., `yourapp.vercel.app`)
- Server gets its own URL (e.g., `yourapp-api.vercel.app`)

**Option B: Monorepo with Multiple Projects**
- Use Vercel's monorepo support
- Configure both `client` and `server` as separate projects in Vercel
- Link them in the Vercel dashboard

### 8. Post-Deployment Steps

After deploying the server:
1. [ ] Copy the server deployment URL
2. [ ] Update client's `VITE_LOCAL_HOST` environment variable in Vercel
3. [ ] Redeploy the client so it picks up the new API URL

### 9. Testing Checklist
- [ ] Test API endpoints are accessible
- [ ] Test user registration/login flow
- [ ] Test article fetching
- [ ] Test CORS is working correctly
- [ ] Test all routes in the React app (home, about, articles, etc.)
- [ ] Test 404 page handling
- [ ] Verify images are loading correctly

### 10. Security Considerations
- [ ] Change `JWT_SECRET` to a strong, random value (not the development one)
- [ ] Review CORS settings - restrict origins if possible
- [ ] Ensure `.env` files are in `.gitignore` (✅ already done)
- [ ] Never commit secrets to git
- [ ] Use Vercel's environment variable encryption

### 11. Performance Optimizations
- [ ] Enable Vercel's edge caching for static assets
- [ ] Consider adding a CDN for images
- [ ] Review bundle size and optimize if needed

### 12. Monitoring & Logs
- [ ] Set up Vercel Analytics (optional)
- [ ] Monitor serverless function logs in Vercel dashboard
- [ ] Set up error tracking (e.g., Sentry) if needed

## Quick Deployment Commands

### Deploy Client:
```bash
cd client
vercel --prod
```

### Deploy Server:
```bash
cd server
vercel --prod
```

### Or use Vercel CLI from root:
```bash
# Link project first
vercel link

# Deploy client
vercel --cwd client --prod

# Deploy server
vercel --cwd server --prod
```

## Common Issues & Solutions

1. **Client can't reach server**: 
   - Verify `VITE_LOCAL_HOST` is set correctly
   - Rebuild client after setting environment variable
   - Check CORS settings on server

2. **MongoDB connection fails**:
   - Verify IP whitelist includes Vercel IPs
   - Check connection string format
   - Ensure database user has correct permissions

3. **404 errors on client routes**:
   - Verify `client/vercel.json` has correct rewrites configuration
   - Ensure all routes redirect to `index.html`

4. **Serverless function timeout**:
   - Vercel has a 10s timeout on Hobby plan, 60s on Pro
   - Optimize database queries if needed

## Notes
- Vercel automatically handles HTTPS
- Serverless functions are stateless - don't rely on in-memory storage
- Environment variables are encrypted at rest in Vercel
- Build logs are available in Vercel dashboard for debugging

