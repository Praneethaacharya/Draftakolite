# Quick Start - Deploy Akolite to Production

## 🚀 Fast Deployment (5 minutes)

### Backend Deployment (EC2)

```bash
# 1. SSH into your EC2 instance
ssh -i your-key.pem ubuntu@your-ec2-ip

# 2. Navigate to backend
cd /home/ubuntu/akoliteBackend
bash setup-new-instance.sh

# 3. Done! Backend is running
# Verify with: sudo docker ps
```

### Frontend Deployment (GitHub Pages)

```bash
# 1. Navigate to frontend
cd frontend

# 2. Run deployment script
chmod +x deploy.sh
./deploy.sh

# 3. Wait for deployment to complete
# Frontend will be live at: https://akoliteresin.github.io/akoliteFrontEnd
```

---

## 🔐 HTTPS URLs

| Component | URL |
|-----------|-----|
| **Frontend** | https://akoliteresin.github.io/akoliteFrontEnd |
| **Backend API** | https://dj4haaiis0la7.cloudfront.net |
| **Local Dev Backend** | http://localhost:5000 |

---

## ✅ Quick Verification

### Backend
```bash
sudo docker logs akolite-backend
# Should see: "✅ Server running on HTTPS at..."
```

### Frontend
1. Open https://akoliteresin.github.io/akoliteFrontEnd
2. Open DevTools (F12)
3. Check Console tab - should have no errors
4. Try logging in

---

## 🛠️ Common Commands

### Backend (EC2)
```bash
# View logs
sudo docker logs -f akolite-backend

# Stop container
sudo docker stop akolite-backend

# Start container
sudo docker start akolite-backend

# Remove and recreate
sudo docker rm akolite-backend
bash setup-new-instance.sh
```

### Frontend (GitHub Pages)
```bash
# Build
npm run build

# Deploy
npm run deploy

# Local testing
npm start
```

---

## 📝 Environment Setup

### Backend .env
```bash
NODE_ENV=production
SERVER_HOST=0.0.0.0
SERVER_PORT=5000
MONGO_URI=your_mongodb_connection_string
```

### Frontend .env (optional)
```bash
REACT_APP_BACKEND_URL=https://dj4haaiis0la7.cloudfront.net
```

---

## 🚨 Troubleshooting

| Problem | Solution |
|---------|----------|
| **Blank page** | Check console (F12), verify BACKEND_URL |
| **CORS error** | Backend not running or wrong URL |
| **Login fails** | Check MongoDB connection |
| **Docker error** | Run `sudo docker ps` to check status |
| **API timeout** | Verify EC2 security group allows port 5000 |

---

## 📚 Full Docs

- **DEPLOYMENT_GUIDE.md** - Detailed setup instructions
- **MIGRATION_SUMMARY.md** - Complete migration details

---

**Status**: ✅ Ready for Production  
**Last Updated**: January 22, 2026
