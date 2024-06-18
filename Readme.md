# Docker Image Repository

## Docker Image Build and Push Docker Workflow

This repository contains Dockerfiles for various base images. Each Dockerfile builds a specific base image tagged with the following format: `docker.io/prasanth595/base-{image_name}:latest`.

## Steps to Build and Push Docker Images

1. **Checkout Repository**: This action checks out the repository content.
   
2. **Set up Docker Buildx**: Sets up Docker Buildx for multi-platform builds.
   
3. **Login to Docker Hub**: Authenticates with Docker Hub using provided credentials.
   
4. **Build and Push Docker Image**: Iterates through each base image defined in the matrix, builds the Docker image using the specified Dockerfile and base image, tags it with `docker.io/prasanth595/base-{image_name}:latest`, and pushes it to Docker Hub.

## Docker Image Pull URLs

To pull any of the built Docker images, use the following URLs with the specified image names:

- Alpine: `docker pull docker.io/prasanth595/base-alpine:latest`
- Ubuntu: `docker pull docker.io/prasanth595/base-ubuntu:latest`
- Debian: `docker pull docker.io/prasanth595/base-debian:latest`
- CentOS: `docker pull docker.io/prasanth595/base-centos:latest`
- Fedora: `docker pull docker.io/prasanth595/base-fedora:latest`
- BusyBox: `docker pull docker.io/prasanth595/base-busybox:latest`
- Node.js: `docker pull docker.io/prasanth595/base-node:latest`
- Python: `docker pull docker.io/prasanth595/base-python:latest`
- OpenJDK: `docker pull docker.io/prasanth595/base-openjdk:latest`
- Golang: `docker pull docker.io/prasanth595/base-golang:latest`
- Nginx: `docker pull docker.io/prasanth595/base-nginx:latest`
- Redis: `docker pull docker.io/prasanth595/base-redis:latest`
- MySQL: `docker pull docker.io/prasanth595/base-mysql:latest`
- PostgreSQL: `docker pull docker.io/prasanth595/base-postgres:latest`
- MongoDB: `docker pull docker.io/prasanth595/base-mongo:latest`
- PHP: `docker pull docker.io/prasanth595/base-php:latest`
- Ruby: `docker pull docker.io/prasanth595/base-ruby:latest`
- Rust: `docker pull docker.io/prasanth595/base-rust:latest`


## Workflow Overview

### Trigger

The workflow triggers on:
- Push events to the `main` branch.
- Pull request events targeting the `main` branch.

### Jobs

#### Build and Push Docker Images

The `build-and-push` job runs on an Ubuntu environment and utilizes a matrix strategy to build Docker images for different base images.

#### Steps

1. **Checkout repository**: This step ensures that the GitHub repository is checked out for subsequent operations.

2. **Set up Docker Buildx**: Initializes Docker Buildx for multi-platform builds.

3. **Login to Docker Hub**: Uses Docker Hub credentials stored in GitHub secrets to authenticate and authorize Docker image pushes.

4. **Build and push Docker image**: Iterates through each base image defined in the matrix and performs the following actions:
   - Builds a Docker image using `Dockerfile` with the specified `BASE_IMAGE`.
   - Tags the built image with `$DOCKER_USERNAME/base-$image_name:latest`.
   - Pushes the tagged image to Docker Hub.

5. **Create GitHub Release**: After successfully pushing each Docker image to Docker Hub, a GitHub release is created using the `actions/create-release` action. Each release corresponds to the Docker image built, tagged, and pushed.

### Included Base Images

- Alpine
- Ubuntu
- Debian
- CentOS
- Fedora
- BusyBox
- Node.js
- Python
- OpenJDK
- Golang
- Nginx
- Redis
- MySQL
- PostgreSQL
- MongoDB
- PHP
- Ruby
- Rust

### Workflow YAML

```yaml
name: Build and Push Docker Images

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        include:
          - base_image: alpine:latest
            image_name: alpine
          - base_image: ubuntu:latest
            image_name: ubuntu
          - base_image: debian:latest
            image_name: debian
          - base_image: centos:latest
            image_name: centos
          - base_image: fedora:latest
            image_name: fedora
          - base_image: busybox:latest
            image_name: busybox
          - base_image: node:latest
            image_name: node
          - base_image: python:latest
            image_name: python
          - base_image: openjdk:latest
            image_name: openjdk
          - base_image: golang:latest
            image_name: golang
          - base_image: nginx:latest
            image_name: nginx
          - base_image: redis:latest
            image_name: redis
          - base_image: mysql:latest
            image_name: mysql
          - base_image: postgres:latest
            image_name: postgres
          - base_image: mongo:latest
            image_name: mongo
          - base_image: php:latest
            image_name: php
          - base_image: ruby:latest
            image_name: ruby
          - base_image: rust:latest
            image_name: rust

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push Docker image - ${{ matrix.image_name }}
        run: |
          docker build --file Dockerfile --build-arg BASE_IMAGE=${{ matrix.base_image }} -t ${{ secrets.DOCKER_USERNAME }}/base-${{ matrix.image_name }}:latest .
          docker push ${{ secrets.DOCKER_USERNAME }}/base-${{ matrix.image_name }}:latest

      - name: Create GitHub Release
        if: success() && github.event_name == 'push'
        id: create_release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: v${{ github.run_number }}-${{ matrix.image_name }}
          release_name: Release ${{ matrix.image_name }} v${{ github.run_number }}
          body: |
            Release of ${{ matrix.image_name }} Docker image.
          draft: false
          prerelease: false
```

## Forking and Contributing

Feel free to fork this repository and contribute by adding more base images or improving the existing ones.
