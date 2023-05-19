# Despliegue en servidor con contenedores

## NGINX REVERSE PROXY

Vamos a hacer uso de un nginx reverse proxy para gestionar todas las peticiones y poder tener muchos contenedores en un mismo servidor, con distintos subdominios

El proxy inverso se encarga de recibir la petición del cliente, enviarla al contenedor que tenga ese subdominio en sus variables de entorno, y generar los certificados LET'S ENCRYPT

![nginx](/img/webproxy.jpg)

Tendremos que cambiar en el `.env` la IP del servidor, y el nombre de la red. Todos los contenedores deben estar en la misma red para que se comuniquen entre sí (La red se crea sola al ejecutar los contenedores)


```
#
# Set the IP address of the external access Interface
#
IP=0.0.0.0

#
# Default Network
#
NETWORK=proxy
```

Para iniciar el contenedor usamos `sudo ./start.sh`

## Contenedores

Todos tendrán un **docker-compose.yml**, con las variables de entorno que determinan el subdominio y la red para el contenedor REVERSE PROXY que genera los certificados, de este estilo:


```

version: '2'
services:
 app:
  build: .
  restart: unless-stopped
  environment:
   - VIRTUAL_HOST=api.midominio.com
   - LETSENCRYPT_HOST=api.midominio.com
   - LETSENCRYPT_EMAIL=info@midominio.com

networks:
  default:
    external:
      name: proxy


```

Para iniciar los contenedores usamos `sudo docker-compose up -d`


### REQUISITOS

`sudo apt install docker.io docker-compose`


### Otros ejemplos de servicios siguiendo este esquema

- Photoprism
- Moodle
- Wordpress
- Wireguard
- Nextcloud
- ...

https://blog.pctripsesp.com/services.html

