
### 1. Instalación de Composer (global)

```bash
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```

### 2. Instalación de extensiones PHP 7.4 necesarias

```bash
sudo apt install php7.4-xml php7.4-curl php7.4-mbstring php7.4-bcmath -y
```
### 3. Clonar y preparar el proyecto

```bash
cd ~
git clone https://github.com/CIP-Junin/CipWebPay.git
sudo mv CipWebPay /var/www/CipWebPay
cd /var/www/CipWebPay
```

### 4. Instalar dependencias de Laravel

```bash
composer install --no-dev --optimize-autoloader   # (recomendado en producció) o simplemente:
composer install
```
### 5. Configurar archivo .env

```bash
cp .env.example .env
php artisan key:generate
```
**Edita el archivo** `.env` según tu entorno (base de datos, APP_URL, etc.):
```bash
nano /var/www/CipWebPay/.env
```
**En el archivo de sitio (Conexion a la base de datos): ** nano /var/www/CipWebPay/.env 
```bash
APP_NAME="Cip Virtual Junin"
APP_ENV=local
APP_KEY=base64:Imq2Bms7LRXE570V33SF6oMcV89m9hwC1He+DXSELdI=
APP_DEBUG=true
APP_URL=localhost

LOG_CHANNEL=stack
LOG_LEVEL=debug

DB_CONNECTION=sqlsrv
DB_HOST=192.168.2.200
DB_PORT=1433
DB_DATABASE=CIPJuninDev
DB_USERNAME=CIP360
DB_PASSWORD=Qz0966lb

#DB_CONNECTION=sqlsrv
#DB_HOST=172.17.30.222
#DB_PORT=1433
#DB_DATABASE=CIPJuninActual
#DB_USERNAME=sa
#DB_PASSWORD=December6-Defective2

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DRIVER=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=smtp.office365.com
MAIL_PORT=587
MAIL_USERNAME=pagotarjeta@cip-junin.org.pe
MAIL_PASSWORD=Cipjunin2021
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=pagotarjeta@cip-junin.org.pe
MAIL_FROM_NAME="Sistemas Colegio de Ingenieros del Perú - CD Junín"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_APP_CLUSTER=mt1

MIX_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
MIX_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
CULQI_test_SK=sk_test_SfIfP6fUomNAHxrS
CULQI_test_PK=pk_test_7KTxCNl2zpdaxLb9
URL_INTRANET_CIP=http://192.168.2.200:8080

FACTILIZA_API_TOKEN=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIzOTY4NiIsImh0dHA6Ly9zY2hlbWFzLm1pY3Jvc29mdC5jb20vd3MvMjAwOC8wNi9pZGVudGl0eS9jbGFpbXMvcm9sZSI6ImNvbnN1bHRvciJ9.6WXac41obJTQufiRmwLbBl60so7viJMcF9QxKy7mSKM
FACTILIZA_API_BASE=https://api.factiliza.com/v1
FACTILIZA_API_TIMEOUT=10

```


### 6. Permisos correctos (importante para que funcione)

```bash
sudo chown -R jesus:www-data /var/www/CipWebPay
sudo chmod -R 775 /var/www/CipWebPay/storage
sudo chmod -R 775 /var/www/CipWebPay/bootstrap/cache

# Alternativa más segura (recomendada):
sudo chown -R www-data:www-data /var/www/CipWebPay/storage /var/www/CipWebPay/bootstrap/cache
sudo chmod -R 775 /var/www/CipWebPay/storage /var/www/CipWebPay/bootstrap/cache
```

### 7. Configuración de Nginx (archivo recomendado)

Crea o edita el archivo:

```bash
sudo nano /etc/nginx/sites-available/cipwebpay
```

**En el editor: ** /etc/nginx/sites-available/cipwebpay 

```nginx
server {
    listen 81;
    server_name cipwebpay.local;

    root /var/www/CipWebPay/public;

    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
 }
```
### 8. Habilitar el sitio y reiniciar Nginx

```bash
# Eliminar enlaces antiguos si existen
sudo rm -f /etc/nginx/sites-enabled/cipwebpay
sudo rm -f /etc/nginx/sites-enabled/CipWebPay

# Crear enlace simbólico
sudo ln -s /etc/nginx/sites-available/cipwebpay /etc/nginx/sites-enabled/

# Probar configuración
sudo nginx -t

# Reiniciar Nginx
sudo systemctl restart nginx
```

Si quieres recargar sin downtime:

```bash
sudo nginx -t && sudo systemctl reload nginx
```
