📄 Travel Memory Application Deployment (AWS + Cloudflare)
1. Introduction

The Travel Memory Application is a full-stack MERN application deployed on the cloud using Amazon Web Services.
The deployment ensures scalability, availability, and domain accessibility using Cloudflare.

2. Architecture Overview

The application follows a distributed architecture:

<img width="302" height="760" alt="image" src="https://github.com/user-attachments/assets/e2db32fb-472e-4095-ab2e-8f1bc514d255" />

2.1 EC2 Instances
<img width="1638" height="324" alt="image" src="https://github.com/user-attachments/assets/4967b410-ead1-4773-9916-db1bf7a4ab59" />


3 Security Groups

Configured security groups:

Frontend SG:
HTTP (80) → 0.0.0.0/0
SSH (22) → My IP
Backend SG:
Port 3001 → Allowed only from Frontend SG

<img width="1428" height="284" alt="image" src="https://github.com/user-attachments/assets/4f96cb85-2606-405b-9616-d9a2b38c2c7b" />

4. Backend Deployment
Cloned repository from GitHub
Navigated to /backend
Configured .env:
MONGO_URI=your_mongodb_connection
PORT=3001
NODE_ENV=production

Started server using:
node index.js

Backend running on:
http://localhost:3001

5. Frontend Deployment
Navigated to /frontend
Installed dependencies:
npm install

Updated API config in url.js:
export const baseUrl = process.env.REACT_APP_BACKEND_URL || ALB url;

Built project:
npm run build

Copied build to Nginx directory:
cp -r build/* /usr/share/nginx/html/

6. Nginx Reverse Proxy Setup

Configured Nginx:
server {
    listen 80;
    server_name _;
    
    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://localhost:3001/;
        proxy_set_header Host $host;
    }
}

Restarted Nginx:
sudo systemctl restart nginx

7. Load Balancer Configuration
Created Application Load Balancer (ALB)
Added listener:
HTTP (80)
Created Target Group:
Attached frontend instances
Health check path: /

<img width="1651" height="283" alt="image" src="https://github.com/user-attachments/assets/740d190f-60a1-48cd-a03b-683c75f81919" />

8. Auto Scaling Setup
Created Launch Templates
Created Auto Scaling Groups:
Frontend ASG
Backend ASG
Scaling policy:
Scale up: CPU > 70%
Scale down: CPU < 30%

<img width="1643" height="251" alt="image" src="https://github.com/user-attachments/assets/46842250-7ffb-40c9-8ad7-d11653e036b8" />

9. Domain Setup with Cloudflare

Domain purchased and configured using Namecheap and Cloudflare.

Steps:
Added domain to Cloudflare
Updated nameservers in Namecheap
Added DNS records:
Type: CNAME
Name: vinamra.online
Target: frontend-843854464.eu-north-1.elb.amazonaws.com

Type: CNAME
Name: www
Target: frontend-843854464.eu-north-1.elb.amazonaws.com

<img width="1307" height="412" alt="image" src="https://github.com/user-attachments/assets/f968503c-8c1c-400c-828e-1ce992bfdb31" />

10. HTTPS Configuration
Enabled SSL via Cloudflare
SSL Mode: Flexible

11. Final Application URL
https://vinamra.online

12. Challenges Faced & Fixes
Issue	Solution
502 Bad Gateway	Fixed Nginx proxy config
SSH issues	Corrected key permissions
Backend not running	Identified port conflict
Cloudflare 521	Fixed ALB + SG
Domain not working	Correct DNS setup

13. Conclusion

The Travel Memory application was successfully deployed with:

Scalable infrastructure
Load balancing
Auto scaling
Custom domain with HTTPS


