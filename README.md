# Docker-Nginx-Cerbot
## Install docker and docker-compose
```
yum install docker

sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
systemctl start docker
systemctl enable docker
```

## Set PATH
```
echo "export PATH=$PATH:/usr/local/bin" >> ~/.bashrc
```

## Pull code
```
git clone https://github.com/nguyenngoctranvu/Docker-Nginx-Cerbot.git
```

## Change config nginx just open port 80 first
Remember to change server_name to yours
path: /data/nginx/conf
```
server {
    listen 80;
    listen [::]:80;

    server_name example.org www.example.org;
    server_tokens off;

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

    location / {
        return 301 https://example.org$request_uri;
    }
}

```

## Dry run
```
docker-compose up -d
docker-compose run --rm  certbot certonly --webroot --webroot-path /var/www/certbot/ --dry-run -d example.org
```
Re-run Certbot without the --dry-run flag to fill the folder with certificates:

```
docker-compose run --rm  certbot certonly --webroot --webroot-path /var/www/certbot/ -d example.org
```

## Open port 443 for domain
```
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    server_name example.org;

    ssl_certificate /etc/nginx/ssl/live/example.org/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/live/example.org/privkey.pem;
    
    location / {
    	# proxy_pass http://ip:port;
    }
}
```

## Renewing the certificates
```
docker compose run --rm certbot renew
```