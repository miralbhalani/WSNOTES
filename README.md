# Basic docker commands
docker build -t mapp:latest
docker run -p 5000:5000 mapp:latest

# Basics of AWS
* registery 
## STEPS TO UPLOAD ON AWS
1. docker build -t <b>api</b>:latest
2. docker tag <b>api</b>:latest xxxx.dkr.ecr.us-east-2.amazonaws.com/try1:<b>api</b>
3. docker push xxxxx.dkr.ecr.us-east-2.amazonaws.com/try1:<b>api</b>

# task deskriptioon
To mention the image of registery to use for the container.

# Cluster
# Cluster -> service
To mention the taskdescription
## Cluster -> service -> load balancer
To get the internent dns
## Cluster -> service -> target groups
To mention the ---
## Cluster -> service -> security group
To mention the internet ip 0.0.0.0/0


# BASIC DOCKER FILE for API
```
FROM node:18-alpine as nodeapi

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "index.js"]
```

Basic docker file for web
```
# Use an official Node.js runtime as a parent image
FROM node:18-alpine as frontapp

# Set the working directory to /app
WORKDIR /app

# Copy the package.json and package-lock.json files to the container
COPY package*.json ./

# Install app dependencies
RUN npm install

# Copy the rest of the application code to the container
COPY . .

# Build the React app
RUN npm run build

# Use Nginx as the base image for serving the React app
FROM nginx:stable-alpine as nginx

# Copy the build folder from the builder image to the Nginx image
COPY --from=frontapp /app/build /usr/share/nginx/html

# Copy the Nginx configuration file to the container
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Expose port 80 for HTTP traffic
EXPOSE 80

# Start Nginx and keep the container running in the foreground
CMD ["nginx", "-g", "daemon off;"]
```

Basic NGINX
```

server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
        try_files $uri $uri/ /index.html;
    }

    # location /api/ {
    #     proxy_pass http://web:80/;
    #     proxy_set_header Host $host;
    #     proxy_set_header X-Real-IP $remote_addr;
    #     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    #     proxy_http_version 1.1;
    #     proxy_set_header Upgrade $http_upgrade;
    #     proxy_set_header Connection 'upgrade';
    # }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```



Basic composer json for WEB
```
version: '3'

services:
  app:
    image: web
    build: .
    ports:
      - "8081:80"
    networks:
      - my-network

networks:
  my-network:
    external: true
```

Basic composer Json for api
```
version: '3'

services:
  web:
    image: nginx:latest
    container_name: nginxforapi
    ports:
      - "8080:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - api
    networks:
      - my-network
  api:
    build: .
    image: api
    ports:
      - "3000"
    deploy:
      replicas: 5
      update_config:
        parallelism: 3
        # delay: 10s
      restart_policy:
        condition: on-failure
    networks:
      - my-network

networks:
  my-network:
    external: true
```


