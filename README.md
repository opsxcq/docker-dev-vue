# Vuejs development environment

[![Docker Pulls](https://img.shields.io/docker/pulls/strm/dev-vue.svg?style=plastic)](https://hub.docker.com/r/strm/dev-vue/)

A complete development environment for VueJS with `yarn` and `vue-cli`, useful
if you need to run builds in a continuous integration system.

# Gitlab-ci

Bellow an example of `.gitlab-ci.yml` using this image (considering the vue code
being in `./frontend`):

```yaml
cache:
    key: ${CI_COMMIT_REF_SLUG}
    paths:
        - frontend/node_modules 

stages:
    - checks
    - build
    - test-unit

lint-frontend:
  stage: checks
  image: strm/dev-vue
  script:
    - cd frontend
    - yarn install 
    - yarn lint 

build-frontend:
  stage: build
  image: strm/dev-vue
  script:
    - cd frontend
    - yarn install
    - yarn build
  artifacts:
      paths:
          - frontend/dist/*

test-frontend:
  stage: test-unit
  image: strm/dev-vue
  script:
    - cd frontend
    - yarn install
    - yarn test
```

# Docker multi-stage build

This build depends on your **nginx.conf** file being in the current directory.

```dockerfile
FROM strm/dev-vue as BUILD

WORKDIR /src
COPY package.json .
COPY yarn.lock .
RUN yarn install

COPY . .
RUN yarn build

FROM nginx
COPY nginx.conf /etc/nginx/nginx.conf
COPY --from=BUILD /src/dist/ /usr/share/nginx/html/
```
