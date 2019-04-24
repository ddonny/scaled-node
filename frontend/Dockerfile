### STAGE 1: Build ###

# We label our stage as 'builder'
FROM node:10-alpine as builder

COPY source/package.json source/package-lock.json ./

## Storing node modules on a separate layer will prevent unnecessary npm installs at each build
RUN npm i && mkdir -p /frontend-dir-inside-container && mv /node_modules ./

WORKDIR /frontend-dir-inside-container

COPY . .

## Build the angular app in production mode and store the artifacts in dist folder
# RUN ls source/*
RUN cd source && npm rebuild node-sass && $(npm bin)/ng build --prod --output-path=dist


### STAGE 2: Setup ###

FROM nginx:1.15.12-alpine

## Copy our default nginx config
COPY source/nginx/default.conf /etc/nginx/conf.d/

## Remove default nginx website
RUN rm -rf /usr/share/nginx/html/*

## From ‘builder’ stage copy over the artifacts in dist folder to default nginx public folder
COPY --from=builder /frontend-dir-inside-container/source/dist /usr/share/nginx/html
RUN ls /usr/share/nginx/html/*

CMD ["nginx", "-g", "daemon off;"]