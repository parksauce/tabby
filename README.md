# Goals
- Rebase the image from ubuntu to alpine to support more architectures
- Switch to NGINX as the webserver
- Add PUID and PGID environment variables

# Known Issues
- Emails not sending

# Requirements
- Docker
- An internet connection
- Optional:
	- Docker Compose

# Quick Start
This is the bare minimum you need to get the container up and running. Note: this method still needs a database to function properly.
```bash
docker run -d \
--name=tabby \
--network=tabby-backend \
-p 8010:80 \
--restart unless-stopped \
thealpaka/tabby
```
Now you can access the container from `http://HOST_IP:8010`

# Advanced Configuration
This section covers a more in-depth guide on deploying Tabby on your server.

## Docker CLI
First create a network to connect both of the services to.
```bash
docker network create tabby-backend
```
Run the following command to start Tabby
```bash
docker run -d \
--name=tabby \
--network=tabby-backend \
-p 8010:80 \
--restart unless-stopped \
thealpaka/tabby
```
Then run this command to start the database
```bash
docker run -d \
  --name=mariadb \
  --network=tabby-backend \
  -e PUID=1000 # Run 'id' in your terminal to get this value \
  -e PGID=1000 # Run 'id' in your terminal to get this value \
  -e MYSQL_ROOT_PASSWORD=ROOT_ACCESS_PASSWORD \
  -e TZ=America/New_York \
  -e MYSQL_DATABASE=tabby \
  -e MYSQL_USER=tabby \
  -e MYSQL_PASSWORD=tabby \
  -v path_to_data:/config \
  --restart unless-stopped \
  linuxserver/mariadb
```
## Docker Compose
Create a file named `docker-compose.yml` and then run `docker-compose pull && docker-compose up -d`
```bash
version: '3'
services:

  tabby:
    image: thealpaka/tabby
    container_name: tabby
    ports:
      - 8010:80
    restart: unless-stopped
    
  db:
    image: linuxserver/mariadb
    container_name: tabby-db
    environment:
      - PUID=1000 # Run 'id' in your terminal to get this value
      - PGID=1000 # Run 'id' in your terminal to get this value
      - MYSQL_ROOT_PASSWORD=ROOT_ACCESS_PASSWORD
      - TZ=America/New_York
      - MYSQL_DATABASE=tabby
      - MYSQL_USER=tabby
      - MYSQL_PASSWORD=tabby
    volumes:
      - ./db:/config
    restart: unless-stopped
```

# Build
This section covers building the container.

```bash 
git clone https://github.com/parksauce/tabby.git
cd tabby && docker build -t tabby .
```