# Deployment Guide for Render.com

This guide will help you deploy the SnapCart application on Render.com.

## Prerequisites

- GitHub account with your code pushed
- Render.com account (free tier available)
- MongoDB Atlas account (for database)

## Architecture

This application consists of two services:
1. **Socket Server** - WebSocket server for real-time features
2. **Next.js App** - Main application frontend and backend

## Deployment Steps

### Option 1: Using render.yaml (Recommended)

1. **Go to Render Dashboard**
   - Visit https://dashboard.render.com/
   - Click "New" → "Blueprint"

2. **Connect Your Repository**
   - Select your GitHub repository: `develo110/grocery`
   - Render will automatically detect the `render.yaml` file

3. **Configure Environment Variables**
   
   For **Socket Server**:
   - `NEXT_PUBLIC_API_URL` - URL of your Next.js app (will be available after deploying the app)
   
   For **Next.js App**:
   - `MONGODB_URL` - Your MongoDB connection string
   - `AUTH_SECRET` - Random secret string for authentication
   - `GOOGLE_CLIENT_ID` - Your Google OAuth client ID
   - `GOOGLE_CLIENT_SECRET` - Your Google OAuth client secret
   - `NEXT_PUBLIC_BASE_URL` - Your app URL (e.g., https://snapcart-app.onrender.com)
   - `CLOUDINARY_CLOUD_NAME` - Your Cloudinary cloud name
   - `CLOUDINARY_API_KEY` - Your Cloudinary API key
   - `CLOUDINARY_API_SECRET` - Your Cloudinary API secret
   - `NEXT_PUBLIC_SOCKET_URL` - Socket server URL (e.g., https://snapcart-socket-server.onrender.com)
   - `MAIL_PASS` - Your email password for nodemailer
   - `MAIL_USER` - Your email address
   - `NEXT_PUBLIC_GEMINI_API_KEY` - Your Gemini API key

4. **Deploy**
   - Click "Apply" to deploy both services
   - Wait for the build to complete (5-10 minutes)

### Option 2: Manual Deployment

#### Deploy Socket Server First

1. **Create New Web Service**
   - Go to Render Dashboard
   - Click "New" → "Web Service"
   - Connect your GitHub repository

2. **Configure Socket Server**
   - Name: `snapcart-socket-server`
   - Region: Choose closest to your users
   - Branch: `main`
   - Root Directory: `snapCartFolder/socketServer`
   - Environment: `Node`
   - Build Command: `npm install`
   - Start Command: `npm start`
   - Plan: Free

3. **Add Environment Variables**
   - `NEXT_PUBLIC_API_URL` - (Add after deploying Next.js app)

4. **Deploy**
   - Click "Create Web Service"
   - Copy the service URL (e.g., https://snapcart-socket-server.onrender.com)

#### Deploy Next.js App

1. **Create New Web Service**
   - Click "New" → "Web Service"
   - Connect your GitHub repository

2. **Configure Next.js App**
   - Name: `snapcart-app`
   - Region: Choose closest to your users
   - Branch: `main`
   - Root Directory: `snapCartFolder/snapcart`
   - Environment: `Node`
   - Build Command: `npm install && npm run build`
   - Start Command: `npm start`
   - Plan: Free

3. **Add Environment Variables**
   - Add all the environment variables listed above
   - Use the Socket Server URL you copied earlier for `NEXT_PUBLIC_SOCKET_URL`

4. **Deploy**
   - Click "Create Web Service"
   - Copy the service URL

#### Update Socket Server

1. Go back to Socket Server settings
2. Add the Next.js app URL to `NEXT_PUBLIC_API_URL`
3. Trigger a manual deploy

## Post-Deployment

### Update OAuth Redirect URLs

1. **Google OAuth Console**
   - Add your Render URLs to authorized redirect URIs:
     - `https://your-app.onrender.com/api/auth/callback/google`

### Test Your Deployment

1. Visit your Next.js app URL
2. Test user registration/login
3. Test real-time features (socket connection)
4. Check MongoDB for data persistence

## Important Notes

### Free Tier Limitations

- Services spin down after 15 minutes of inactivity
- First request after spin-down takes 30-60 seconds
- 750 hours/month of runtime per service

### Custom Domain (Optional)

1. Go to service settings
2. Click "Custom Domain"
3. Add your domain and configure DNS

### Monitoring

- Check logs in Render Dashboard
- Monitor service health
- Set up alerts for downtime

## Troubleshooting

### Build Failures

- Check build logs in Render Dashboard
- Verify all dependencies are in package.json
- Ensure Node version compatibility

### Environment Variables

- Double-check all required variables are set
- Verify MongoDB connection string
- Test API keys locally first

### Socket Connection Issues

- Ensure CORS is properly configured
- Verify socket server URL in Next.js app
- Check firewall/network settings

## Support

For issues:
1. Check Render logs
2. Review MongoDB Atlas logs
3. Test locally with production environment variables

## Useful Commands

```bash
# Test build locally
cd snapCartFolder/snapcart
npm install
npm run build
npm start

# Test socket server locally
cd snapCartFolder/socketServer
npm install
npm start
```

## Security Checklist

- [ ] All sensitive data in environment variables
- [ ] .env files not committed to git
- [ ] MongoDB network access configured
- [ ] OAuth redirect URLs updated
- [ ] API keys rotated if exposed
- [ ] CORS properly configured

---

**Note**: After deployment, update the URLs in your environment variables and redeploy if needed.
