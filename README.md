# 🚀 MERN Blog App – Full Cloud Deployment on AWS

This project demonstrates a full deployment of a **MERN stack blog application** (MongoDB, Express, React, Node.js) on **Amazon Web Services (AWS)** using **Terraform** for infrastructure provisioning and **Ansible** for backend automation. It includes image upload functionality via S3 and environment variable management via Ansible templating.

---

## 📦 Technologies & Tools Used

### 🔹 MERN Stack
- **MongoDB Atlas** – Cloud database to store posts
- **Express.js** – Backend server and routing
- **React.js** – Frontend single-page application
- **Node.js** – Server runtime

### 🔹 Cloud Infrastructure & Automation
- **AWS EC2** – Host the backend server
- **AWS S3** – Host the frontend build + store uploaded media
- **AWS IAM** – Secure access control to S3
- **Terraform** – Infrastructure as code (creates EC2, security group)
- **Ansible** – Automates server setup (git clone, .env creation, pm2)

---

## 🗂️ Project Structure

mern-infra/
├── ansible/
│ ├── inventory
│ ├── backend-playbook.yml
│ └── roles/backend/
│ ├── tasks/main.yml
│ ├── vars/main.yml
│ └── templates/env.j2
├── main.tf
├── init.sh
├── outputs.tf
└── variables.tf

---

## ⚙️ Deployment Steps

### ✅ 1. Create Two S3 Buckets
- **`mern-blog-media`** → For uploaded image files
- **`mern-blog-frontend`** → For React build files

### ✅ 2. Configure IAM
- Create an IAM user with **programmatic access**
- Attach a custom inline policy for uploading to `mern-blog-media`
- Use access keys in Ansible `.env` template

### ✅ 3. Setup MongoDB Atlas
- Create a free cluster
- Add EC2 IP or `0.0.0.0/0` to the IP whitelist
- Create database user
- Copy the connection string for the `.env` file

### ✅ 4. Provision EC2 Instance via Terraform

```bash
terraform init
terraform apply

- Creates EC2 instance in eu-north-1

- Uses user_data script (init.sh) to install Node, PM2, Git, etc.

- Outputs the EC2 Public IP and DNS

✅ 5. Connect to EC2 via SSH

ssh -i "mern-key.pem" ubuntu@<public-dns>

🔧 Backend Automation via Ansible

✅ Setup
From WSL or Linux terminal:

ansible-playbook -i inventory backend-playbook.yml

The playbook does the following:

1- Clones the blog app repository from GitHub

2- Generates a .env file from a Jinja template using secure variables

3- Installs backend dependencies using npm

4- Starts the backend server using pm2

5- Saves the PM2 process for reboot persistence

🌐 Frontend Deployment to S3

✅ Build the React App

cd frontend
npm run build

✅ Upload to S3
Upload contents of the /build folder to mern-blog-frontend bucket

Enable Static Website Hosting

Set:

Index document: index.html

Error document: index.html

The app will be publicly accessible at:


http://mern-blog-frontend.s3-website.eu-north-1.amazonaws.com/

🔐 .env File (Generated via Ansible)

The .env file is created dynamically from env.j2 using these variables:

PORT=5000
HOST=0.0.0.0
MODE=production

MONGODB={{ mongodb_uri }}
JWT_SECRET={{ lookup('password', '/dev/null length=32 chars=hex') }}
JWT_EXPIRE=30min
JWT_REFRESH={{ lookup('password', '/dev/null length=32 chars=hex') }}
JWT_REFRESH_EXPIRE=3d

AWS_ACCESS_KEY_ID={{ aws_access_key }}
AWS_SECRET_ACCESS_KEY={{ aws_secret_key }}
AWS_REGION={{ aws_region }}
S3_BUCKET={{ s3_bucket }}
MEDIA_BASE_URL={{ media_url }}

DEFAULT_PAGINATION=20

📸 Screenshots (Suggested for Submission)
Terraform Output (showing public IP / DNS)

1- SSH into EC2 (and pm2 list)

2- MongoDB Atlas Cluster and whitelist config

3- S3 Media Bucket Contents (with images)

4- S3 Frontend Bucket Settings (static hosting enabled)

5- Live Website Screenshot (from browser)

✅ Summary
This project showcases a full deployment workflow for a MERN application on AWS, using modern DevOps practices. All infrastructure is defined as code and automated for repeatable and scalable deployments.

Author: Rawan Al-Khaldi
Stack: MERN + AWS + Terraform + Ansible