
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
