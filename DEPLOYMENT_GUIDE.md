# Deployment Guide - EC2 Backend + HTTPS Frontend

## Overview
This guide covers deploying the Akolite application with:
- **Backend**: Node.js/Express on AWS EC2 with HTTPS support
- **Frontend**: React on GitHub Pages with environment-aware API URLs
- **HTTPS**: CloudFront domain (https://dj4haaiis0la7.cloudfront.net)

---

## Backend Setup (EC2)

### Prerequisites
- AWS EC2 instance (Ubuntu 22.04 LTS recommended)
- Docker installed
- MongoDB connection string
- SSL certificates (if using HTTPS directly on EC2)

### Configuration Files

#### 1. **config.js** (Updated)
The backend now supports environment-based configuration:
```javascript
- NODE_ENV=production → Uses HTTPS CloudFront URL
- NODE_ENV=development → Uses http://localhost:5000
```

#### 2. **server.js** (Updated)
CORS is now configured for HTTPS:
```javascript
origin: [
  'https://akoliteresin.github.io',      // GitHub Pages frontend
  'https://dj4haaiis0la7.cloudfront.net', // CloudFront domain
  'http://localhost:3000',                 // Local development
]
```

### Deployment Steps

#### Step 1: SSH into EC2 Instance
```bash
ssh -i your-key.pem ubuntu@your-ec2-ip
```

#### Step 2: Update Environment Variables
Create/update `.env` file in `/home/ubuntu/akoliteBackend/backend/`:
```bash
NODE_ENV=production
SERVER_HOST=0.0.0.0
SERVER_PORT=5000
MONGO_URI=your_mongodb_connection_string
SSL_KEY_PATH=/etc/ssl/private/server.key
SSL_CERT_PATH=/etc/ssl/certs/server.crt
```

#### Step 3: Build Docker Image
```bash
cd /home/ubuntu/akoliteBackend/backend
sudo docker build -t akolite-backend:latest .
```

#### Step 4: Run Docker Container
```bash
sudo docker run -d \
  --name akolite-backend \
  -p 5000:5000 \
  --env-file .env \
  akolite-backend:latest
```

#### Step 5: Verify Deployment
```bash
# Check if container is running
sudo docker ps

# View logs
sudo docker logs akolite-backend

# Test API endpoint
curl https://dj4haaiis0la7.cloudfront.net/api/health
```

---

## Frontend Setup (GitHub Pages)

### Configuration Files

#### 1. **axiosInstance.js** (Updated)
Now supports environment-aware API URLs:
```javascript
- NODE_ENV=production → https://dj4haaiis0la7.cloudfront.net
- NODE_ENV=development → http://localhost:5000
```

#### 2. **package.json**
Already configured with:
- `homepage`: https://akoliteresin.github.io/akoliteFrontEnd
- `deploy` script: `npm run build && gh-pages -d build`

### Deployment Steps

#### Step 1: Install Dependencies
```bash
cd frontend
npm install
```

#### Step 2: Build for Production
```bash
npm run build
```

This creates an optimized build in the `build/` directory with NODE_ENV=production.

#### Step 3: Deploy to GitHub Pages
```bash
npm run deploy
```

The `gh-pages` package will:
1. Create a `gh-pages` branch if it doesn't exist
2. Push the `build/` contents to that branch
3. GitHub Pages automatically serves the site

#### Step 4: Verify Deployment
- Visit: https://akoliteresin.github.io/akoliteFrontEnd
- Check browser console for any API errors
- Test login and API calls

---

## HTTPS Configuration

### CloudFront Setup
1. The CloudFront domain (https://dj4haaiis0la7.cloudfront.net) is already configured
2. It should point to your EC2 instance or load balancer
3. SSL/TLS is handled by CloudFront

### Backend HTTPS
For direct HTTPS on EC2 (optional if using CloudFront):
1. Obtain SSL certificates (Let's Encrypt recommended)
2. Place at:
   - `/etc/ssl/private/server.key`
   - `/etc/ssl/certs/server.crt`
3. Backend will automatically use HTTPS if certificates exist

---

## Environment Variables

### Frontend
```bash
# .env or set at build time
REACT_APP_BACKEND_URL=https://dj4haaiis0la7.cloudfront.net
NODE_ENV=production
```

### Backend
```bash
# .env file
NODE_ENV=production
SERVER_HOST=0.0.0.0
SERVER_PORT=5000
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/
SSL_KEY_PATH=/etc/ssl/private/server.key
SSL_CERT_PATH=/etc/ssl/certs/server.crt
```

---

## Troubleshooting

### Frontend Issues
1. **CORS Errors**: Ensure backend CORS includes GitHub Pages domain
2. **Blank Page**: Check browser console for errors
3. **API Not Working**: Verify BACKEND_URL in axiosInstance.js
4. **Cache Issues**: Clear GitHub Pages cache or use `npm run deploy` with `--force`

### Backend Issues
1. **Container Won't Start**: Check logs with `sudo docker logs akolite-backend`
2. **Port Already in Use**: `sudo lsof -i :5000` and kill process
3. **CORS Still Failing**: Verify server.js corsOptions includes all required domains
4. **HTTPS Not Working**: Verify certificate paths in environment variables

### DNS/Network Issues
1. **Cannot reach backend**: Verify EC2 security group allows port 5000
2. **CloudFront not working**: Check CloudFront distribution configuration
3. **DNS resolution fails**: Verify domain registrar settings

---

## Monitoring & Logs

### Backend Logs
```bash
# Real-time logs
sudo docker logs -f akolite-backend

# Container status
sudo docker ps -a

# Stop container
sudo docker stop akolite-backend

# Start container
sudo docker start akolite-backend
```

### Frontend Logs
- Check browser DevTools (F12)
- GitHub Actions logs: Repository → Actions tab

---

## Rollback Procedures

### Frontend Rollback
```bash
# GitHub Pages keeps previous builds
# To rollback: redeploy previous version
git checkout previous-commit
npm run deploy
```

### Backend Rollback
```bash
# Keep previous Docker image
sudo docker images

# Run previous image
sudo docker run -d --name akolite-backend-old -p 5001:5000 akolite-backend:old-hash
```

---

## Performance Tips

1. **Frontend**: Use GitHub Pages CDN for static assets
2. **Backend**: Enable caching headers in responses
3. **Database**: Ensure MongoDB indexes are properly configured
4. **API**: Implement rate limiting and pagination
5. **CloudFront**: Enable compression and caching policies

---

## Security Checklist

- [x] HTTPS enabled on frontend (GitHub Pages)
- [x] CORS properly configured
- [x] JWT tokens used for authentication
- [x] Environment variables not committed to git
- [ ] SSL certificates valid and not expired
- [ ] EC2 security group restricts ports appropriately
- [ ] Database credentials in secure .env file
- [ ] API rate limiting implemented

---

## Next Steps

1. **Test all API endpoints** in production
2. **Monitor error logs** for first 24 hours
3. **Set up GitHub Actions** for automated deployments (optional)
4. **Configure monitoring** (CloudWatch, etc.)
5. **Document any custom domain** setup if applicable

---

**Last Updated**: January 22, 2026
**Deployment Status**: READY FOR PRODUCTION
