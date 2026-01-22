# HTTPS Setup Guide for EC2 Backend

## Quick Setup Steps

### 1. SSH into EC2 Instance
```bash
ssh -i C:\Users\Prajwal\Downloads\akolitekey2.pem ubuntu@13.60.97.186
```

### 2. Generate Self-Signed SSL Certificate (Quick Method)
```bash
# Create SSL directory
sudo mkdir -p /etc/ssl/private
sudo mkdir -p /etc/ssl/certs

# Generate self-signed certificate (valid for 365 days)
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/server.key \
  -out /etc/ssl/certs/server.crt \
  -subj "/C=IN/ST=Telangana/L=Hyderabad/O=Akolite/CN=13.60.97.186"

# Set permissions
sudo chmod 644 /etc/ssl/certs/server.crt
sudo chmod 644 /etc/ssl/private/server.key
```

### 3. Verify Certificates Exist
```bash
ls -la /etc/ssl/certs/server.crt
ls -la /etc/ssl/private/server.key
```

### 4. Update Backend Code
The updated `server.js` is ready:
- It checks for SSL certificates
- Falls back to HTTP if certificates don't exist
- Automatically loads HTTPS when certificates are present

### 5. Stop and Restart Backend Server
```bash
# Navigate to backend directory
cd ~/Akolite/backend

# Stop any running process
pkill -f "node server.js"

# Start the server
node server.js
```

You should see:
```
✅ SSL certificates loaded. Starting HTTPS server...
✅ Server running on HTTPS at https://13.60.97.186:5000
```

### 6. Deploy Frontend
```bash
cd ~/Akolite/frontend
npm run deploy
```

## For Production (Better Security - Optional)

To use Let's Encrypt certificates (requires a domain name):

```bash
# Install Certbot
sudo apt install certbot -y

# Get certificate (replace with your domain)
sudo certbot certonly --standalone -d yourdomain.com \
  --agree-tos -m your-email@example.com

# Update paths in backend .env:
# SSL_KEY_PATH=/etc/letsencrypt/live/yourdomain.com/privkey.pem
# SSL_CERT_PATH=/etc/letsencrypt/live/yourdomain.com/fullchain.pem
```

## Troubleshooting

### Browser shows "Your connection is not private"
This is expected with self-signed certificates. It's secure; you can proceed.

### Server won't start
Check permissions:
```bash
sudo chmod 644 /etc/ssl/certs/server.crt
sudo chmod 600 /etc/ssl/private/server.key
```

### Port already in use
```bash
# Kill existing process
sudo lsof -i :5000
sudo kill -9 <PID>
```

### Check if HTTPS is working
```bash
curl -k https://13.60.97.186:5000/
# The -k flag ignores certificate warnings
```
