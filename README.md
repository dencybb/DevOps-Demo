# DevOps Demo - Laravel CRM

![Docker](https://img.shields.io/badge/docker-ready-brightgreen.svg)
![AWS](https://img.shields.io/badge/deployed-AWS%20EC2-orange.svg)

Production-ready Laravel CRM with multi-container Docker, CI/CD pipeline, and AWS deployment.

🔗 **Live Demo:** [https://denisbasic.me](https://denisbasic.me)

![](https://raw.githubusercontent.com/inertiajs/pingcrm/master/screenshot.png)

---

## 🛠️ Tech Stack

**Backend:** Laravel 11, PHP 8.2, MySQL 8.0  
**Frontend:** Vue.js 3, Inertia.js, Tailwind CSS, Vite  
**DevOps:** Docker, Nginx, Let's Encrypt SSL, GitHub Actions, AWS EC2

---

## 🏗️ Architecture

**4 Docker Containers:**
- `app` - PHP-FPM (Laravel)
- `nginx` - Web server
- `db` - MySQL database
- `frontend` - Node.js (Vite)

**Flow:**
```
HTTPS → Nginx Reverse Proxy → Docker Nginx → PHP-FPM → MySQL
```

---

## 🚀 Quick Start

### Local Development
```bash
git clone https://github.com/dencybb/DevOps-Demo.git
cd DevOps-Demo
cp .env.example .env
docker compose up -d
docker compose exec app composer install
docker compose exec app php artisan key:generate
docker compose exec app php artisan migrate:fresh --seed
```

**Access:** http://localhost:8000  
**Login:** johndoe@example.com / secret

---

## 📦 Docker Commands
```bash
docker compose up -d              # Start containers
docker compose ps                 # Check status
docker compose logs -f app        # View logs
docker compose exec app bash      # Enter container
docker compose down               # Stop containers
```

---

## ☁️ AWS Deployment

**EC2 Instance:** t3.small (Ubuntu 24.04)  
**Security Group:** Ports 22, 80, 443, 8000  
**Domain:** Namecheap DNS → AWS Elastic IP  
**SSL:** Let's Encrypt (auto-renewal)

### Deploy Steps
```bash
# On EC2
git clone https://github.com/dencybb/DevOps-Demo.git
cd DevOps-Demo
docker compose up -d
docker compose exec app composer install --no-dev
docker compose exec app php artisan key:generate
docker compose exec app php artisan migrate:fresh --seed --force

# Nginx reverse proxy + SSL
sudo apt install nginx certbot python3-certbot-nginx
sudo certbot --nginx -d denisbasic.me -d www.denisbasic.me
```

---

## 🔄 CI/CD Pipeline

GitHub Actions automatically:
- ✅ Tests code on every push
- ✅ Builds frontend (Vite)
- ✅ Runs PHPUnit tests
- ✅ Blocks merge if tests fail

**Status:** [View Pipeline](https://github.com/dencybb/DevOps-Demo/actions)

---

## 🐛 Common Issues

**White screen?**
```bash
docker compose exec app rm -f /var/www/public/hot
docker compose restart app
```

**Permission errors?**
```bash
docker compose exec app chown -R www-data:www-data /var/www/storage /var/www/bootstrap/cache
```

**MySQL connection failed?**
```bash
sleep 30  # Wait for MySQL to initialize
docker compose logs db
```

---

## 📊 Project Features

✅ Multi-container Docker orchestration  
✅ Production-ready Nginx reverse proxy  
✅ SSL/TLS encryption (Let's Encrypt)  
✅ Automated CI/CD (GitHub Actions)  
✅ AWS EC2 deployment  
✅ Custom domain with DNS  

---

## 👤 Author

**Denis Basic**

🌐 [denisbasic.me](https://denisbasic.me)  
💼 [GitHub](https://github.com/dencybb)  
📧 denis.dency1999@yahoo.com

---

## 📝 License

MIT License - feel free to use for learning & portfolio purposes.

---

