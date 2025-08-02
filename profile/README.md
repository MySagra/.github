## 1 - Create .env file
```env
SERVER_PORT=3030
ROOT_PWD="abcdeghi"
PEPPER="123456789"
JWT_SECRET="redondi"
APP_DOMAIN="mysagra.fun"
```
## 2 - Create Docker Compose
```yaml
services:
  mariadb:
    image: "mariadb:latest"
    environment:
      - MYSQL_ROOT_PASSWORD=${ROOT_PWD}
      - MYSQL_DATABASE=mysagra
    ports:
      - "3306:3306"
    volumes:
      - mariadb_data:/var/lib/mysql
    restart: unless-stopped

  api-server:
    image: ghcr.io/mysagra/mysagra-server:latest
    ports:
      - "${SERVER_PORT}:${SERVER_PORT}"
    environment:
      - DATABASE_URL=mysql://root:${ROOT_PWD}@mariadb:3306/mysagra
      - PORT=${SERVER_PORT}
      - PEPPER=${PEPPER}
      - JWT_SECRET=${JWT_SECRET}
      - HOST=${APP_DOMAIN}
    depends_on:
      - mariadb
    restart: unless-stopped
  
  webapp:
    image: "ghcr.io/mysagra/mysagra-client:latest"
    ports:
      - "3000:3000"
    environment:
      - API_URL=http://api-server:${SERVER_PORT}
      - NEXT_PUBLIC_APP_NAME=${APP_NAME}
    networks:
      - default
      - nginx
 
volumes:
  mariadb_data:
```
