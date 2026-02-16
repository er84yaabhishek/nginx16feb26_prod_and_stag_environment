

---

```markdown
# 🚀 NGINX + Docker Reverse Proxy Setup (Production & Staging)
## 📁 Project Structure


abhi16feb26/
│
├── prod_test_app/
│   └── index.html
│
├── stag_test_app/
│   └── index.html
│
├── docker-compose.prod.yml
└── docker-compose.stag.yml

````

> ✅ No Dockerfile needed — using official NGINX image.

---

## 🐳 Docker Setup

### 🟢 Production Docker Compose

📄 `docker-compose.prod.yml`

```yaml
version: "3.8"

services:
  prod_test_app:
    image: nginx:latest
    container_name: prod_test_app
    ports:
      - "8081:80"
    volumes:
      - ./prod_test_app:/usr/share/nginx/html
    restart: always
````

Run:

```bash
docker-compose -f docker-compose.prod.yml up -d
```

---

### 🟡 Staging Docker Compose

📄 `docker-compose.stag.yml`

```yaml
version: "3.8"

services:
  stag_test_app:
    image: nginx:latest
    container_name: stag_test_app
    ports:
      - "8082:80"
    volumes:
      - ./stag_test_app:/usr/share/nginx/html
    restart: always
```

Run:

```bash
docker-compose -f docker-compose.stag.yml up -d
```

---

### 🌐 Direct Testing (Docker Only)

```
http://SERVER-IP:8081  → Production
http://SERVER-IP:8082  → Staging
```

Example:

```
http://167.86.88.33:8081
http://167.86.88.33:8082
```

✅ Agar yaha chal gaya → Docker setup perfect.

---

## 🔁 NGINX Reverse Proxy Setup (Server Level)

### 1️⃣ Install NGINX

```bash
apt update
apt install nginx -y
```

---

### 🔵 Production Reverse Proxy Config

📄 `/etc/nginx/sites-available/prod.soft84ya.shop`

```nginx
server {
    listen 80;
    server_name prod.soft84ya.shop;

    location / {
        proxy_pass http://localhost:8081;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Enable site:

```bash
ln -s /etc/nginx/sites-available/prod.soft84ya.shop /etc/nginx/sites-enabled/
```

---

### 🟡 Staging Reverse Proxy Config

📄 `/etc/nginx/sites-available/stag.soft84ya.shop`

```nginx
server {
    listen 80;
    server_name stag.soft84ya.shop;

    location / {
        proxy_pass http://localhost:8082;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Enable site:

```bash
ln -s /etc/nginx/sites-available/stag.soft84ya.shop /etc/nginx/sites-enabled/
```

---

## 🔐 SSL Setup (Let's Encrypt)

Install Certbot:

```bash
apt install certbot python3-certbot-nginx -y
```

Run for each domain:

```bash
certbot --nginx -d prod.soft84ya.shop
certbot --nginx -d stag.soft84ya.shop
```

> ✅ Certbot automatically:
>
> * Adds SSL
> * Redirects HTTP → HTTPS
> * Sets auto-renewal

---

## 🧪 Final Check

```bash
nginx -t
systemctl restart nginx
docker ps
```

---

## 🎯 Final Working URLs

* **Production:** `https://prod.soft84ya.shop`
* **Staging:** `https://stag.soft84ya.shop`

---

## 🧠 Architecture

```
Internet
   ↓
NGINX (Reverse Proxy + SSL)
   ↓
Docker Container (NGINX)
   ↓
Your App Files
```

---

## 💪 Features / Benefits

* ✅ Separate containers for prod & stag
* ✅ Separate ports & volumes
* ✅ Reverse proxy with proper headers
* ✅ SSL ready with Let's Encrypt
* ✅ Clean, professional DevOps structure
* ✅ Easy to extend for Laravel / Node.js / PHP apps




