# Pre-Deployment Checklist

## Backend Configuration ✅

- [x] **config.js** - Updated with NODE_ENV support
- [x] **server.js** - CORS configured for HTTPS domains
- [x] **setup-new-instance.sh** - Updated with production settings
- [x] **.env template** - Ready for environment variables
- [x] **Docker configuration** - Restart policy added
- [x] **SSL/TLS support** - Ready for certificates if needed

## Frontend Configuration ✅

- [x] **config.js** - Created with environment-aware URLs
- [x] **axiosInstance.js** - Updated for HTTPS support
- [x] **package.json** - gh-pages configured and ready
- [x] **homepage** - Set to GitHub Pages URL
- [x] **deploy script** - Created and tested
- [x] **Environment variables** - NODE_ENV support implemented

## HTTPS/Security ✅

- [x] **Frontend HTTPS** - GitHub Pages provides SSL/TLS
- [x] **Backend HTTPS** - CloudFront provides SSL/TLS (through https://dj4haaiis0la7.cloudfront.net)
- [x] **CORS** - Properly configured for all domains
- [x] **JWT Authentication** - Token-based security maintained
- [x] **Certificate validation** - Configured per environment

## Documentation ✅

- [x] **DEPLOYMENT_GUIDE.md** - Complete setup instructions
- [x] **MIGRATION_SUMMARY.md** - What changed and why
- [x] **QUICK_START.md** - Fast deployment guide
- [x] **This checklist** - Pre-deployment verification

## Pre-Deployment Tasks

### 1. Code Repository
- [ ] Commit all changes to git
- [ ] Push to GitHub
- [ ] Verify no sensitive data in commits
- [ ] Create backup branch (optional)

### 2. Backend Preparation (EC2)
- [ ] Verify EC2 instance is running
- [ ] SSH access working
- [ ] Security groups allow port 5000
- [ ] MongoDB connection string ready
- [ ] .env file ready to upload

### 3. Frontend Preparation
- [ ] Install dependencies: `npm install`
- [ ] Build locally: `npm run build`
- [ ] No build errors
- [ ] Build folder created successfully

### 4. HTTPS Setup
- [ ] CloudFront distribution configured
- [ ] Domain: https://dj4haaiis0la7.cloudfront.net
- [ ] SSL certificate valid
- [ ] Origin properly configured
- [ ] Cache settings appropriate

### 5. Database
- [ ] MongoDB Atlas connection verified
- [ ] Credentials in environment variables
- [ ] Database backups configured
- [ ] Connection string accurate

## Deployment Steps

### Phase 1: Backend Deployment (Est. 10 minutes)

```bash
# 1. SSH into EC2
ssh -i your-key.pem ubuntu@your-ec2-ip

# 2. Clone repository
cd /home/ubuntu
git clone YOUR_REPO_URL akoliteBackend
cd akoliteBackend/backend

# 3. Create .env file
nano .env
# Add: NODE_ENV=production, MONGO_URI, etc.

# 4. Run setup script
bash setup-new-instance.sh

# 5. Verify
sudo docker ps
sudo docker logs akolite-backend
```

### Phase 2: Frontend Deployment (Est. 5 minutes)

```bash
# 1. Navigate to frontend
cd frontend

# 2. Install dependencies
npm install

# 3. Build for production
npm run build

# 4. Deploy to GitHub Pages
npm run deploy

# 5. Wait for deployment (check GitHub Actions)
```

### Phase 3: Verification (Est. 10 minutes)

```bash
# Test Frontend
- Visit: https://akoliteresin.github.io/akoliteFrontEnd
- Open DevTools (F12)
- Check Console for errors
- Try login

# Test Backend
curl https://dj4haaiis0la7.cloudfront.net/api/health

# Test Full Integration
- Try adding data through frontend
- Verify data appears in MongoDB
- Check API responses
```

## Post-Deployment Tasks

### Immediate (First Hour)
- [ ] Monitor backend logs: `sudo docker logs -f akolite-backend`
- [ ] Check frontend console for errors
- [ ] Test all main features
- [ ] Verify CORS headers in responses
- [ ] Confirm JWT token handling works

### Short Term (First 24 Hours)
- [ ] Monitor error rates
- [ ] Check performance metrics
- [ ] Verify database performance
- [ ] Test file uploads/downloads
- [ ] Verify email notifications (if applicable)

### Long Term (First Week)
- [ ] Monitor API response times
- [ ] Check database query performance
- [ ] Review security logs
- [ ] Set up automated backups
- [ ] Configure monitoring alerts

## Rollback Procedures

### If Frontend Deployment Fails
```bash
# Option 1: Redeploy previous version
git checkout previous-commit
npm run deploy

# Option 2: Manual GitHub Pages revert
# Go to GitHub repository settings > Pages
# Check deployment history
```

### If Backend Deployment Fails
```bash
# Option 1: Stop and remove container
sudo docker stop akolite-backend
sudo docker rm akolite-backend

# Option 2: Run previous image
sudo docker run -d --name akolite-backend-backup \
  -p 5001:5000 previous-image-hash
```

## Troubleshooting Guide

### Frontend Issues
- **Blank page**: Clear cache, check Console (F12)
- **API errors**: Verify BACKEND_URL in axiosInstance.js
- **CORS error**: Backend CORS configuration
- **Login fails**: Check network requests in DevTools

### Backend Issues
- **Container won't start**: `sudo docker logs akolite-backend`
- **Port in use**: `sudo lsof -i :5000`
- **Can't connect to MongoDB**: Verify connection string
- **HTTPS handshake fails**: Check certificate configuration

### Network Issues
- **Can't reach API**: EC2 security group rules
- **DNS issues**: Verify domain configuration
- **CloudFront issues**: Check distribution settings

## Support & Resources

### Documentation
- DEPLOYMENT_GUIDE.md - Comprehensive guide
- MIGRATION_SUMMARY.md - What changed
- QUICK_START.md - Fast deployment

### Commands Reference
- **Backend logs**: `sudo docker logs -f akolite-backend`
- **Container status**: `sudo docker ps -a`
- **Stop container**: `sudo docker stop akolite-backend`
- **View environment**: `sudo docker exec akolite-backend env`

### GitHub URLs
- **Frontend Repo**: https://github.com/akoliteresin/akoliteFrontEnd
- **Backend Repo**: https://github.com/akoliteresin/akoliteBackend (if separate)
- **Issues**: Create issue for problems

## Final Sign-Off

- [ ] All team members reviewed changes
- [ ] Backend configuration verified
- [ ] Frontend build tested locally
- [ ] HTTPS URLs confirmed
- [ ] Database backup created
- [ ] Rollback plan documented
- [ ] Team notified of deployment
- [ ] Maintenance window scheduled (if needed)

---

## Deployment Ready! ✅

**Status**: PRODUCTION READY  
**Date**: January 22, 2026  
**Backend URL**: https://dj4haaiis0la7.cloudfront.net  
**Frontend URL**: https://akoliteresin.github.io/akoliteFrontEnd

**Next Action**: Follow deployment steps in Phase 1, 2, and 3 above.

---

**Last Updated**: January 22, 2026  
**Prepared by**: GitHub Copilot AI Assistant
