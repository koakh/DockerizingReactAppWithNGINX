# NOTES

- [NOTES](#notes)
  - [Main Note File](#main-note-file)
  - [TLDR](#tldr)
    - [Development Image](#development-image)
      - [Test Hot Reload](#test-hot-reload)
      - [Stop Stack](#stop-stack)
    - [Production Image](#production-image)
      - [Test](#test)
  - [Use starter in other React Projects](#use-starter-in-other-react-projects)
  - [Use Private Registry with docker Images](#use-private-registry-with-docker-images)

## Main Note File

- `/home/mario/Dropbox/Aplicativos/Notable/notes/Docker꞉ Dockerizing a React App.md`

## TLDR

### Development Image

```shell
# tag name
$ IMAGE_TAG="sample:dev"
# build and tag the docker image
$ docker build -t ${IMAGE_TAG} .
# enter container, usefull for debug like npm i, npm run start
$ docker run -it ${IMAGE_TAG} sh
# spin up the container
$ docker run -it --rm -p 3000:3000 ${IMAGE_TAG}
# build and tag the docker image, and lauch with daeomn mode
$ docker-compose up -d --build
# view logs
$ docker-compose logs -f
```

#### Test Hot Reload

1. navigate to <http://localhost:3000>
2. Edit `<code>src/App.js</code>`, change for ex `save to reload` and watch hot reload on browser

#### Stop Stack

```shell
$ docker-compose stop
```

### Production Image

```shell
# build and tag the docker production image
$ docker build -f Dockerfile.prod -t sample:prod .
# spin up the container
$ docker run -it --rm -p 1337:80 sample:prod

# build and tag the docker production image, and lauch with daeomn mode
$ docker-compose -f docker-compose.prod.yml up -d --build
```

#### Test

1. navigate to <http://localhost:1337/>

## Use starter in other React Projects

copy files to target CRA project

- sample/.dockerignore
- sample/docker-compose.prod.yml
- sample/docker-compose.yml
- sample/Dockerfile
- sample/Dockerfile.prod

## Use Private Registry with docker Images

- [301 Moved Permanently](https://docs.npmjs.com/docker-and-private-modules)


occurs in
npm ci

```shell
 ---> Running in 023971bafc3e
npm ERR! Only HTTP(S) protocols are supported

npm ERR! A complete log of this run can be found in:
npm ERR!     /root/.npm/_logs/2021-02-05T16_16_24_299Z-debug.log
```

RUN npm config set registry https://hub.critical-links.com:543


https://stackoverflow.com/questions/8874363/npm-install-without-ssl


https://medium.com/bb-tutorials-and-thoughts/dockerizing-react-app-with-nodejs-backend-typescript-version-55a40389b0ac

https://www.digitalocean.com/community/tutorials/how-to-secure-a-containerized-node-js-application-with-nginx-let-s-encrypt-and-docker-compose



add `.npmrc`

```shell
registry=hub.critical-links.com:543
hub.critical-links.com:543/:_authToken=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyZWFsX2dyb3VwcyI6eyJuYW1lIjoibWF......
```

after this we have configured private registry

and add .... to `Dockerfile`

```dockerfile
ARG NPM_TOKEN 
COPY .npmrc .npmrc  
```

ex

```dockerfile
# pull official base image
FROM node:13.12.0-alpine

# set working directory
WORKDIR /app

# add `/app/node_modules/.bin` to $PATH
ENV PATH /app/node_modules/.bin:$PATH

# install app dependencies
COPY package.json ./
COPY package-lock.json ./

ARG NPM_TOKEN 
COPY .npmrc .npmrc  

RUN npm install --silent
RUN RUN npm install react-scripts@3.4.1 -g --silent

# add app
COPY . ./

# start app
CMD ["npm", "start"]
```
