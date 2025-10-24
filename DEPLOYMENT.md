# Hostel Management System - Render Deployment Guide

## How This Deployment Works

This is a **single deployment** that serves both your backend API and frontend React app:

1. **Backend (Express Server)**: Located in `server/` folder, handles all API requests
2. **Frontend (React App)**: Located in `frontend/` folder, gets built and served as static files
3. **Root Directory**: Contains the main `package.json` that orchestrates both

**Deployment Flow:**
1. Render builds the React app (`npm run build`)
2. Render starts the Express server (`npm start`)
3. Express server serves both API routes AND the built React app
4. Users access your app through a single URL

## Prerequisites

1. **MongoDB Atlas Account**: Create a free MongoDB Atlas cluster
2. **Render Account**: Sign up at [render.com](https://render.com)
3. **GitHub Repository**: Push your code to GitHub

## Project Structure

```
Hostel-Management/
├── package.json          # Main package.json (orchestrates both frontend & backend)
├── server/               # Backend Express server
│   ├── index.js         # Server entry point
│   ├── controllers/     # API controllers
│   ├── models/          # Database models
│   └── routes/          # API routes
├── frontend/            # React frontend
│   ├── package.json     # Frontend dependencies
│   ├── src/             # React source code
│   └── public/          # Static assets
└── Procfile             # Deployment configuration
```

**Key Points:**
- Root `package.json` has scripts that handle both frontend and backend
- `npm run build` builds the React app into `frontend/build/`
- `npm start` starts the Express server, which serves both API and static files
- Express server serves the built React app for all non-API routes

## Step 1: Prepare MongoDB Atlas

1. Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create a new cluster (free tier available)
3. Create a database user with read/write permissions
4. Whitelist all IP addresses (0.0.0.0/0) for Render access
5. Get your connection string (it will look like: `mongodb+srv://username:password@cluster.mongodb.net/hostel-management?retryWrites=true&w=majority`)

## Step 2: Deploy to Render

### 2.1 Create a New Web Service

1. Log in to [Render Dashboard](https://dashboard.render.com)
2. Click "New +" → "Web Service"
3. Connect your GitHub repository
4. Select your repository: `Hostel-Management`

### 2.2 Configure Build Settings

**Build Command:**
```bash
npm run build
```

**Start Command:**
```bash
npm start
```

**Node Version:** 18.x (or latest)

**Root Directory:** Leave empty (uses project root)

**Important:** This is a **single deployment** that includes both:
- **Backend API** (Express server in `server/` folder)
- **Frontend** (React app in `frontend/` folder, built and served as static files)

### 2.3 Environment Variables

**Step 1: Initial Deployment (without REACT_APP_API_URL)**
Add these environment variables in Render dashboard:

```
NODE_ENV=production
PORT=5000
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/hostel-management?retryWrites=true&w=majority
JWT_SECRET=your-super-secret-jwt-key-here-make-it-long-and-random
```

**Step 2: After First Deployment**
1. Deploy your app first (without REACT_APP_API_URL)
2. Render will give you a URL like: `https://hostel-management-abc123.onrender.com`
3. Go back to Environment Variables in Render dashboard
4. Add: `REACT_APP_API_URL=https://hostel-management-abc123.onrender.com`
5. Redeploy your app

**Important Notes:**
- Use a strong, random JWT_SECRET (at least 32 characters)
- Replace the MongoDB connection string with your actual Atlas URI
- The REACT_APP_API_URL will be your actual Render URL after first deployment

### 2.4 Advanced Settings

- **Auto-Deploy:** Yes (recommended)
- **Branch:** main (or your default branch)
- **Root Directory:** Leave empty (uses project root - this is correct!)

## Step 3: Post-Deployment Configuration

### 3.1 Get Your Render URL

After your first deployment:
1. Go to your Render dashboard
2. Click on your deployed service
3. You'll see your URL in the format: `https://your-app-name-abc123.onrender.com`
4. Copy this URL

### 3.2 Update Environment Variables

1. In Render dashboard, go to your service → Environment
2. Add the new environment variable:
   ```
   REACT_APP_API_URL=https://your-actual-app-name-abc123.onrender.com
   ```
3. Click "Save Changes"
4. Your app will automatically redeploy with the correct API URL

### 3.2 Test Your Application

1. Visit your Render URL
2. Register a new admin user
3. Test all functionality:
   - User registration/login
   - Student management
   - Attendance tracking
   - Data analysis

## Step 4: Database Seeding (Optional)

If you want to populate your database with sample data:

1. SSH into your Render instance (if available)
2. Run the seeder script:
```bash
node server/seeder.js
```

## Troubleshooting

### Common Issues:

1. **Build Failures:**
   - **Dependency Conflicts**: If you get `ERESOLVE` errors, the build script now uses `--legacy-peer-deps` to resolve conflicts
   - **Module Resolution Errors**: If you get `Cannot find module 'ajv/dist/compile/codegen'` errors, the build script now cleans and reinstalls dependencies
   - **React DatePicker Issues**: Updated to version 4.25.0 for React 17 compatibility
   - **AJV/AJV-Keywords Issues**: Added explicit versions and overrides for these dependencies
   - Check Node.js version compatibility (>=18.0.0)
   - Ensure all dependencies are in package.json
   - Verify build command is correct

2. **Database Connection Issues:**
   - Verify MongoDB Atlas IP whitelist includes 0.0.0.0/0
   - Check connection string format
   - Ensure database user has proper permissions

3. **Frontend API Issues:**
   - **Problem**: "Network Error" or API calls failing
   - **Solution**: Make sure REACT_APP_API_URL is set to your actual Render URL
   - **Check**: Go to Render dashboard → Environment → Verify REACT_APP_API_URL
   - **Test**: Open browser dev tools → Network tab → Check if API calls are going to correct URL
   - **Common mistake**: Using localhost URL instead of Render URL

4. **Authentication Issues:**
   - Verify JWT_SECRET is set
   - Check token expiration settings
   - Ensure proper headers in API calls

### Performance Optimization:

1. **Enable Gzip Compression** (already configured in Express)
2. **Use CDN** for static assets (Render provides this automatically)
3. **Database Indexing** for better query performance
4. **Caching** for frequently accessed data

## Security Considerations

1. **Environment Variables:**
   - Never commit .env files to version control
   - Use strong, unique secrets
   - Rotate secrets regularly

2. **Database Security:**
   - Use strong database passwords
   - Enable MongoDB Atlas security features
   - Regular security updates

3. **API Security:**
   - Implement rate limiting
   - Use HTTPS (Render provides this automatically)
   - Validate all inputs

## Monitoring and Maintenance

1. **Render Dashboard:**
   - Monitor application logs
   - Check resource usage
   - Set up alerts for downtime

2. **MongoDB Atlas:**
   - Monitor database performance
   - Set up alerts for connection issues
   - Regular backups

## Cost Optimization

1. **Render Free Tier:**
   - 750 hours/month free
   - Automatic sleep after 15 minutes of inactivity
   - Perfect for development/testing

2. **MongoDB Atlas Free Tier:**
   - 512MB storage
   - Shared clusters
   - Sufficient for small to medium applications

## Support

- **Render Documentation:** https://render.com/docs
- **MongoDB Atlas Documentation:** https://docs.atlas.mongodb.com
- **Project Issues:** Create issues in your GitHub repository

---

**Deployment Checklist:**

- [ ] MongoDB Atlas cluster created and configured
- [ ] Environment variables set in Render
- [ ] Build and start commands configured
- [ ] Application deployed successfully
- [ ] Database connection working
- [ ] Frontend-backend communication working
- [ ] Authentication system working
- [ ] All features tested
- [ ] Performance optimized
- [ ] Security measures implemented
