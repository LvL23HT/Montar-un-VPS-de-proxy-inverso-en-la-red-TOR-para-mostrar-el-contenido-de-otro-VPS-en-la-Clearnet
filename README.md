# Montar-un-VPS-de-proxy-inverso-en-la-red-TOR-para-mostrar-el-contenido-de-otro-VPS-en-la-Clearnet

Este proyecto tiene como objetivo configurar un servidor VPS como un proxy inverso en la red TOR para mostrar el contenido de otro VPS en la clearnet. Utiliza Nginx como servidor web y proxy inverso, junto con un certificado SSL autofirmado generado por OpenSSL para proporcionar cifrado y seguridad adicionales.

Si distribuyes esta guía paso a paso de Montar un VPS de proxy inverso en la red TOR para mostrar el contenido de otro VPS en la clearnet conserva los créditos.

Montar un VPS de proxy inverso en la red TOR para mostrar el contenido de otro VPS en la clearnet es un proceso que requiere varios pasos y herramientas. A continuación, te guiaré a través del proceso de configuración:

Nota: Asegúrate de que estás utilizando un VPS con una distribución de Linux compatible, como Ubuntu o Debian.

Actualiza el sistema:
```ruby
sudo apt-get update && sudo apt-get upgrade -y
'''
Instala TOR y Nginx en el VPS:
```ruby
sudo apt-get install tor nginx -y
'''
Configura TOR:

Abre el archivo de configuración de TOR:
```ruby
sudo nano /etc/tor/torrc
'''
Añade las siguientes líneas al final del archivo:
```ruby
HiddenServiceDir /var/lib/tor/hidden_service/ 
HiddenServicePort 80 127.0.0.1:8080
'''
Guarda y cierra el archivo (Ctrl + X, luego Y, y presiona Enter).

Reinicia TOR y obtén tu dirección .onion:
```ruby
sudo systemctl restart tor sudo cat /var/lib/tor/hidden_service/hostname
'''
Toma nota de la dirección .onion generada.

Configura Nginx como proxy inverso:

Crea un archivo de configuración para el sitio web:

 
```ruby
server {
   listen 8080;
   server_name pcxodijkwovkpyhkyrdyqdnfs63ulvan7bh4ukr2qvds6cnwcnqftaid.onion;

   location / {
      proxy_pass http://YOUR_URL_CLEARNET:8080;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
   }
}

server {
   listen 443 ssl;
   server_name pcxodijkwovkpyhkyrdyqdnfs63ulvan7bh4ukr2qvds6cnwcnqftaid.onion;

   ssl_certificate /etc/nginx/ssl/pcxodijkwovkpyhkyrdyqdnfs63ulvan7bh4ukr2qvds6cnwcnqftaid.onion.crt;
   ssl_certificate_key /etc/nginx/ssl/pcxodijkwovkpyhkyrdyqdnfs63ulvan7bh4ukr2qvds6cnwcnqftaid.onion.key;

   location / {
      proxy_pass https://YOUR_URL_CLEARNET:443;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
   }
}
 
'''
generar un certificado autofirmado de OpenSSL

Instala OpenSSL:
```ruby
sudo apt-get install openssl -y
'''
Crea un directorio para almacenar el certificado y la clave:
```ruby
sudo mkdir /etc/nginx/ssl
'''
Genera el certificado y la clave privada autofirmados:
```ruby
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt
'''
Durante el proceso, se te pedirá que proporciones información sobre la organización y el servidor. Puedes rellenar estos campos o dejarlos en blanco presionando Enter.

Habilita el sitio web y reinicia Nginx:
```ruby
sudo ln -s /etc/nginx/sites-available/hidden-service /etc/nginx/sites-enabled/ sudo systemctl restart nginx
 
'''
Configura el firewall (opcional pero recomendado):

Si tu VPS tiene un firewall activo (como ufw), asegúrate de permitir el tráfico de TOR y Nginx:
```ruby
sudo ufw allow 80/tcp sudo ufw allow 8080/tcp sudo ufw allow 443/tcp sudo ufw allow 8443/tcp 
'''
Una vez que hayas completado estos pasos, tu VPS de proxy inverso en la red TOR estará configurado y mostrará el contenido de tu otro VPS en la clearnet a través de la dirección .onion generada.

That's all...

 

Nota: Cabe destacar que si el servidor en la Clearnet pasa por Cloudflare lo bloqueara, y creo que el 80% del trafico de internet pasa por Cloudflare
