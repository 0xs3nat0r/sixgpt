## راه‌اندازی SixGPT روی توضیع Linux

### قبل از شروع:
* برای اجرای ماینر **SixGPT** باید وارد وب‌سایت [sixgpt.xyz](https://sixgpt.xyz) شوید و با استفاده از **کیف پول** (Metamask) و **ایمیل** خود وارد شوید.
* مطمئن شوید که کیف پول شما که کلید خصوصی **Vana** به آن متصل است، موجودی کافی از توکن‌های **$VANA** در شبکه مورد نظر (حداقل 0.1) دارد. برای دریافت توکن‌های رایگان می‌توانید از [VANA Fauset](https://faucet.vana.org/satori) استفاده کنید.

### 1. نصب Docker
ابتدا باید **Docker** را بر روی سیستم خود نصب کنید. برای این کار دستورات زیر را وارد کنید:

```bash  
sudo apt update -y && sudo apt upgrade -y
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y && sudo apt upgrade -y

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo chmod +x /usr/local/bin/docker-compose

docker --version
```

### 2. نصب SixGPT

**الف) ایجاد پوشه‌ها**
```bash
mkdir sixgpt
```
```bash
cd sixgpt
```

**ب) تنظیم متغیرها - جایگزینی `your_private_key` با کلید خصوصی خود** : ( Metamask Wallet -> ︙ -> Show Private Key )
```bash
export VANA_PRIVATE_KEY=your_private_key
export VANA_NETWORK=moksha
```

**ج) ایجاد فایل `docker-compose.yml`**
```bash
nano docker-compose.yml
```

در این فایل کد زیر را وارد کنید:

```yaml
version: '3.8'

services:
  ollama:
    image: ollama/ollama:0.3.12
    ports:
      - "11435:11434"
    volumes:
      - ollama:/root/.ollama
    restart: unless-stopped
 
  sixgpt3:
    image: sixgpt/miner:latest
    ports:
      - "3015:3000"
    depends_on:
      - ollama
    environment:
      - VANA_PRIVATE_KEY=${VANA_PRIVATE_KEY}
      - VANA_NETWORK=${VANA_NETWORK}
    restart: always

volumes:
  ollama:
```

**برای ذخیره‌سازی فایل:** `CTRL+X` سپس `Y` و در نهایت `ENTER` را فشار دهید.

### 3. راه‌اندازی ماینر
پس از ایجاد فایل `docker-compose.yml`، با دستور زیر ماینر را راه‌اندازی کنید:
```bash
docker compose up -d
```

### 4. مشاهده لاگ‌ها
برای مشاهده لاگ‌ها و پیگیری وضعیت ماینر، می‌توانید از دستور زیر استفاده کنید:
```bash
docker compose logs -fn 100
```

با اجرای این دستورات، ماینر SixGPT شما شروع به کار خواهد کرد و در حال استخراج داده‌ها و توکن‌های VANA خواهد بود.

برای بررسی مقدار استخراج شده میتوانید از سایت [sixgpt.xyz](https://sixgpt.xyz) استفاده کنید.
