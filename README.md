# DevOps Demo — Laravel CRM

![CI/CD Pipeline](https://github.com/dencybb/DevOps-Demo/actions/workflows/ci-cd.yml/badge.svg)
![Docker](https://img.shields.io/badge/Docker-4--container-2496ED?logo=docker&logoColor=white)
![Laravel](https://img.shields.io/badge/Laravel-11-FF2D20?logo=laravel&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-EC2%20t3.small-FF9900?logo=amazonaws&logoColor=white)
![SSL](https://img.shields.io/badge/SSL-Let's%20Encrypt-003A70)

Production-ready Laravel CRM demonstrating multi-container Docker orchestration, automated CI/CD pipeline, and AWS EC2 deployment with SSL.

🔗 **Live Demo:** [https://denisbasic.me](https://denisbasic.me)
📧 **Login:** johndoe@example.com / secret

![](https://raw.githubusercontent.com/inertiajs/pingcrm/master/screenshot.png)


> **Note:** Application code based on [Ping CRM](https://github.com/inertiajs/pingcrm) 
> by the Inertia.js team. This project focuses on the DevOps infrastructure: 
> Docker, CI/CD pipeline, and AWS deployment.

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Backend | Laravel 11, PHP 8.2 |
| Frontend | Vue.js 3, Inertia.js, Tailwind CSS, Vite |
| Database | MySQL 8.0 |
| Web Server | Nginx |
| Containerization | Docker, Docker Compose |
| CI/CD | GitHub Actions |
| Cloud | AWS EC2 (t3.small, Ubuntu 24.04) |
| SSL | Let's Encrypt (auto-renewal) |
| DNS | Namecheap → AWS Elastic IP |

---

## 🏗️ Architecture

### Container Setup

```
┌─────────────────────────────────────────────┐
│                  AWS EC2                    │
│                                             │
│   Internet → Nginx (Host) → SSL Termination │
│                   │                         │
│         ┌─────────▼──────────┐             │
│         │   Docker Network   │             │
│         │                    │             │
│         │  ┌──────────────┐  │             │
│         │  │  nginx:alpine │  │ Port 8000  │
│         │  │  (container)  │  │             │
│         │  └──────┬───────┘  │             │
│         │         │          │             │
│         │  ┌──────▼───────┐  │             │
│         │  │  php:8.2-fpm  │  │             │
│         │  │   (Laravel)   │  │             │
│         │  └──────┬───────┘  │             │
│         │         │          │             │
│         │  ┌──────▼───────┐  │             │
│         │  │  mysql:8.0    │  │             │
│         │  └──────────────┘  │             │
│         │                    │             │
│         │  ┌──────────────┐  │             │
│         │  │  node:22      │  │ Port 5173  │
│         │  │  (Vite dev)   │  │             │
│         │  └──────────────┘  │             │
│         └────────────────────┘             │
└─────────────────────────────────────────────┘
```

### Request Flow

```
User → denisbasic.me (HTTPS)
     → Nginx host (SSL termination, port 443)
     → Docker Nginx container (port 8000)
     → PHP-FPM / Laravel (port 9000)
     → MySQL (port 3306)
```

---

## 🔄 CI/CD Pipeline

Every push to `main` triggers the full pipeline automatically:

```
Push to main
     │
     ▼
┌─────────────────────────────────┐
│  JOB 1: Test (~2-3 min)        │
│  ├── composer install           │
│  ├── npm install + build        │
│  ├── php artisan migrate        │
│  └── php artisan test           │
└────────────────┬────────────────┘
                 │ only if tests pass
                 ▼
┌─────────────────────────────────┐
│  JOB 2: Deploy (~1-2 min)      │
│  ├── SSH into EC2               │
│  ├── git pull origin main       │
│  ├── docker compose build app   │
│  ├── docker compose up -d app   │
│  ├── docker compose restart     │
│  │   frontend                   │
│  ├── php artisan migrate --force│
│  └── config/route/view cache    │
└─────────────────────────────────┘
                 │
                 ▼
        ✅ Live on denisbasic.me
```

**[View Pipeline Runs →](https://github.com/dencybb/DevOps-Demo/actions)**

---

## 🚀 Local Development

### Prerequisites
- Docker + Docker Compose
- Git

### Setup

```bash
# 1. Clone the repository
git clone https://github.com/dencybb/DevOps-Demo.git
cd DevOps-Demo

# 2. Configure environment
cp .env.example .env

# 3. Start all containers
docker compose up -d

# 4. Install PHP dependencies
docker compose exec app composer install

# 5. Generate application key
docker compose exec app php artisan key:generate

# 6. Run migrations and seed database
docker compose exec app php artisan migrate:fresh --seed
```

**Access:** http://localhost:8000
**Login:** johndoe@example.com / secret

---

## 📦 Docker Commands

```bash
# Container management
docker compose up -d              # Start all containers (detached)
docker compose down               # Stop and remove containers
docker compose ps                 # Check container status
docker compose restart            # Restart all containers

# Logs
docker compose logs -f            # Follow all logs
docker compose logs -f app        # Follow Laravel logs
docker compose logs -f nginx      # Follow Nginx logs

# Access containers
docker compose exec app bash      # Enter Laravel container
docker compose exec db mysql -u laravel -p pingcrm  # MySQL CLI

# Laravel commands
docker compose exec app php artisan migrate
docker compose exec app php artisan cache:clear
docker compose exec app php artisan config:cache
docker compose exec app php artisan queue:work
```

---

## ☁️ AWS Infrastructure

| Component | Detail |
|---|---|
| Instance | EC2 t3.small |
| OS | Ubuntu 24.04 LTS |
| Region | eu-north-1 (Stockholm) |
| IP | Elastic IP (static) |
| Storage | 20GB gp2 EBS |
| Security Group | 22 (SSH), 80 (HTTP), 443 (HTTPS) |

### DNS Setup
```
denisbasic.me  →  A Record  →  AWS Elastic IP (13.62.15.53)
```

### SSL (Let's Encrypt)
```bash
# Initial certificate
sudo certbot --nginx -d denisbasic.me -d www.denisbasic.me

# Auto-renewal (configured via cron)
sudo certbot renew --dry-run
```

---

## 🔐 GitHub Actions Secrets

The pipeline requires these secrets configured in **Settings → Secrets → Actions**:

| Secret | Description |
|---|---|
| `SSH_HOST` | EC2 public IP address |
| `SSH_USER` | EC2 SSH username (`ubuntu`) |
| `SSH_PRIVATE_KEY` | Private key for EC2 access |

---

## 🐛 Troubleshooting

**White screen / 500 error**
```bash
docker compose exec app rm -f /var/www/public/hot
docker compose exec app php artisan config:clear
docker compose restart app
```

**Permission errors**
```bash
docker compose exec app chown -R www-data:www-data /var/www/storage /var/www/bootstrap/cache
docker compose exec app chmod -R 775 /var/www/storage
```

**MySQL connection failed**
```bash
# Check if MySQL is healthy
docker compose logs db | tail -20
docker compose ps db

# Wait for MySQL to initialize then retry
docker compose restart app
```

**Frontend not updating**
```bash
docker compose restart frontend
```

---

## 📊 Features

- ✅ Multi-container Docker orchestration (4 containers)
- ✅ Automated CI/CD — push to deploy
- ✅ Zero-manual-intervention deployments
- ✅ Database migrations run automatically on deploy
- ✅ Production Nginx reverse proxy with SSL
- ✅ AWS EC2 with Elastic IP
- ✅ Custom domain with HTTPS

---

## 👤 Author

**Denis Basic**
🌐 [denisbasic.me](https://denisbasic.me)
💼 [github.com/dencybb](https://github.com/dencybb)
📧 denis.dency1999@yahoo.com

---

## 📝 License

MIT — free to use for learning and portfolio purposes.
