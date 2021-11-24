# Osticket Docker
This repository contain [osTicket](https://osticket.com/) images.

osTicket is a widely-used open source support ticket system. It seamlessly integrates inquiries created via email, phone and web-based forms into a simple easy-to-use multi-user web interface. Manage, organize and archive all your support requests and responses in one place while providing your customers with accountability and responsiveness they deserve.

you can download it from [here](https://osticket.com/download/) or using images of this repository as docker container to install.
Note: to use osticket you must have a database like mysql:

Example usage:

`docker run -d -p 80:80 daghlani/osticket`

or you can use [docker-compose](https://github.com/docker/compose):

for example:

```yaml
version: "3.7"
services:
  osTicket:
    container_name: osTicket
    hostname: osTicket
    restart: always
    image: ${OST_IMAGE:-daghlani/osTicket}:${OST_IMAGE_TAG:-latest}
    ports:
      - ${OST_PORT:-80}:80
    volumes:
      - ./osT_volumes/upload:/var/www/upload
      - ./osT_volumes/scripts:/var/www/scripts
    labels:
      - traefik.frontend.headers.isDevelopment=false
      - traefik.frontend.headers.forceSTSHeader=true
      - traefik.frontend.headers.STSPreload=true
      - traefik.frontend.headers.STSIncludeSubdomains=true
      - traefik.frontend.headers.STSSeconds=31536000
      - traefik.backend.loadbalancer.stickiness=true
      - traefik.frontend.headers.customResponseHeaders=X-Powered-By:Ali Daghlani
      - traefik.enable=true
      - traefik.frontend.rule=Host:support.${OST_DOMAIN:-daghlani.ir}
      - traefik.port=80
      - traefik.docker.network=http_network
    networks:
      ost_net:
      http_network:


  mysql:
    image: docker.raveshmand.ir/wordpress/mysql:5.7
    container_name: osT_mysql
    hostname: mysql.${OST_DOMAIN:-daghlani.ir}
    volumes:
      - ost_db:/var/lib/mysql
    ports:
      - 127.0.0.1:3307:3306
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: <password>
      MYSQL_DATABASE: osticket
      MYSQL_USER: osticket
      MYSQL_PASSWORD: <password>
    networks:
      ost_net:

volumes:
  ost_db:

networks:
  ost_net:
  http_network:
    external: true
```
