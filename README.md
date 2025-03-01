   # project-servers-docker



### Desarrollo

Iniciamos archivo ``Vagrantfile``.

``Alejandro@DESKTOP-PE1L610 MINGW64 ~/Desktop/project-high-availibity-docker/vagrant
$ vagrant init
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.``

Configuracion de archivo mediante ``VIM``.

Alejandro@DESKTOP-PE1L610 MINGW64 ~/Desktop/project-high-availibity-docker/vagrant    
$ vim Vagrantfile

Alejandro@DESKTOP-PE1L610 MINGW64 ~/Desktop/project-high-availibity-docker/vagrant    
$ cat Vagrantfile 
# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # 1. Imagen base: Ubuntu Jammy 64 bits
  config.vm.box = "ubuntu/jammy64"

  # 2. Configuración de la VM
  config.vm.provider "virtualbox" do |vb|
    vb.name = "ubuntu_jammy_docker"
    vb.memory = 3072   # 3 GB de RAM
    vb.cpus = 3        # Ajusta según tus necesidades
  end

  # (Opcional) Configuración de red
  # config.vm.network "private_network", ip: "192.168.33.10"

  # 3. Aprovisionamiento: instalar Docker, Docker Compose y configurar
  config.vm.provision "shell", inline: <<-SHELL
    echo "=== Actualizando paquetes ==="
    sudo apt-get update -y

    echo "=== Instalando Docker ==="
    sudo apt-get install -y docker.io

    echo "=== Instalando Docker Compose ==="
    sudo apt-get install -y docker-compose

    # Agregar al usuario 'vagrant' al grupo 'docker' para no requerir sudo
    sudo usermod -aG docker vagrant

    # Habilitar Docker en el arranque
    sudo systemctl enable docker

    echo "=== Listo. Docker y Docker Compose instalados. ==="
  SHELL
end

Levantamos VM.

Alejandro@DESKTOP-PE1L610 MINGW64 ~/Desktop/project-high-availibity-docker/vagrant    
$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'ubuntu/jammy64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'ubuntu/jammy64' version '20241002.0.0' is up to date...
==> default: Setting the name of the VM: ubuntu_jammy_docker
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...

Comprobamos estado.

Alejandro@DESKTOP-PE1L610 MINGW64 ~/Desktop/project-high-availibity-docker/vagrant    
$ vagrant status
Current machine states:

default                   running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.

Accedemos a la VM mediante el siguiente comando.

Alejandro@DESKTOP-PE1L610 MINGW64 ~/Desktop/project-high-availibity-docker/vagrant    
$ vagrant ssh
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-130-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Sun Feb  9 16:41:30 UTC 2025

  System load:             0.01
  Usage of /:              5.2% of 38.70GB
  Memory usage:            10%
  Swap usage:              0%
  Processes:               114
  Users logged in:         0
  IPv4 address for enp0s3: 10.0.2.15
  IPv6 address for enp0s3: fd00::e5:4ff:fe58:6a0c


Expanded Security Maintenance for Applications is not enabled.

40 updates can be applied immediately.
32 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status

New release '24.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


Una vez dentro de la vm, comprobamos si se instalo docker correctamente.

vagrant@ubuntu-jammy:~$ docker -v
Docker version 26.1.3, build 26.1.3-0ubuntu1~22.04.1

```vagrant@ubuntu-jammy:~$ docker-compose -v```
```docker-compose version 1.29.2, build unknown```

Creamos directorio ```sv-dockers```.

```vagrant@ubuntu-jammy:~$ mkdir sv-dockers```

Nos posicionamos en la misma y creamos el archivo ```docker-compose.yml```.

```vagrant@ubuntu-jammy:~$ cd sv-dockers/```

```vagrant@ubuntu-jammy:~/sv-dockers$ touch docker-compose.yml```

Creacion de directorio ```node_app```.

```vagrant@ubuntu-jammy:~/sv-dockers$ mkdir node_app```

Nos situamos dentro de la misma y creamos el archivo ```Dockerfile```.

```vagrant@ubuntu-jammy:~/sv-dockers/node_app$ touch Dockerfile```

Iniciamos aplicacion ```Node.js```

vagrant@ubuntu-jammy:~/sv-dockers/node_app$ npm init -y
Wrote to /home/vagrant/sv-dockers/node_app/package.json:

{
  "name": "node_app",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}

Instalacion de ```Express```.

vagrant@ubuntu-jammy:~/sv-dockers/node_app$ npm install express

added 69 packages, and audited 70 packages in 11s

14 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities

Creacion de archivo ```index.js```.

```vagrant@ubuntu-jammy:~/sv-dockers/node_app$ touch index.js```

Lo abrimos mediante ```VIM``` y lo dejamos de la siguiente forma:

const express = require('express');
const app = express();
const port = process.env.PORT || 3000;                                                                                                                                                      app.get('/', (req, res) => {
  res.send('¡Hola soy ale!');});                                                                                                                                                                                         app.listen(port, () => {
  console.log(`Servidor corriendo en http://localhost:${port}`);                              
});                      


Probamos la aplicacion de manera local

vagrant@ubuntu-jammy:~/sv-dockers/node_app$ node index.js 
Servidor corriendo en http://localhost:3000

Construccion de imagen Docker

vagrant@ubuntu-jammy:~/sv-dockers/node_app$ docker build -t miapp-node .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  2.865MB
Step 1/7 : FROM node:18-bullseye
18-bullseye: Pulling from library/node
478cb7364610: Pull complete
f0388f0d5bf1: Pull complete
5d473f760e53: Pull complete
66d32181a1ed: Pull complete
dd19209a30ea: Pull complete
cd505b4da3a2: Pull complete
4d9af66d8b58: Pull complete
7f13863cfd45: Pull complete
Digest: sha256:8943be188991017e43e929fd0eb69d8fba247b56fd9d770528bb8443287e248c
Status: Downloaded newer image for node:18-bullseye
 ---> 4669413746ca
Step 2/7 : WORKDIR /usr/src/app
 ---> Running in 3ec869294b00
 ---> Removed intermediate container 3ec869294b00
 ---> f4cb7a98fc77
Step 3/7 : COPY package*.json ./
 ---> cc682b2b1fb0
Step 4/7 : RUN npm install
 ---> Running in e7b694feda8d

added 69 packages, and audited 70 packages in 3s

14 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
npm notice
npm notice New major version of npm available! 10.8.2 -> 11.1.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v11.1.0
npm notice To update run: npm install -g npm@11.1.0
npm notice
 ---> Removed intermediate container e7b694feda8d
 ---> 0d183250ccaf
Step 5/7 : COPY . .
 ---> 38079489089c
Step 6/7 : EXPOSE 3000
 ---> Running in 8e969a0b59a0
 ---> Removed intermediate container 8e969a0b59a0
 ---> 7f79047006fa
Step 7/7 : CMD ["npm", "start"]
 ---> Running in f7be762c80ae
 ---> Removed intermediate container f7be762c80ae
 ---> 689fc92cea65
Successfully built 689fc92cea65
Successfully tagged miapp-node:latest

Verificamos imagen.

vagrant@ubuntu-jammy:~/sv-dockers/node_app$ docker images
REPOSITORY      TAG            IMAGE ID       CREATED         SIZE
miapp-node      latest         689fc92cea65   2 minutes ago   999MB

Ejecutamos el contenedor

vagrant@ubuntu-jammy:~/sv-dockers/node_app$ docker run -p 3000:3000 miapp-node
npm error Missing script: "start"
npm error
npm error Did you mean one of these?
npm error   npm star # Mark your favorite packages
npm error   npm stars # View packages marked as favorites
npm error
npm error To see a list of scripts, run:
npm error   npm run
npm error A complete log of this run can be found in: /root/.npm/_logs/2025-02-10T23_19_49_679Z-debug-0.log

Solucion de Problemas

Editamos el archivo ```package.json```, agregandole la siguiente linea en la parte de Scripts

vagrant@ubuntu-jammy:~/sv-dockers/node_app$ vim package.json

{
  "name": "node_app",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.21.2"
  }
}

Volvemos a recontruir la imagen Docker

vagrant@ubuntu-jammy:~/sv-dockers/node_app$ docker build --no-cache -t miapp-node .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  2.865MB
Step 1/7 : FROM node:18-bullseye
 ---> 4669413746ca
Step 2/7 : WORKDIR /usr/src/app
 ---> Running in 6e5d670c452c
 ---> Removed intermediate container 6e5d670c452c
 ---> 5bc661531734
Step 3/7 : COPY package*.json ./
 ---> 516c0bb2bc34
Step 4/7 : RUN npm install
 ---> Running in 84f9c3250023

added 69 packages, and audited 70 packages in 2s

14 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
npm notice
npm notice New major version of npm available! 10.8.2 -> 11.1.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v11.1.0
npm notice To update run: npm install -g npm@11.1.0
npm notice
 ---> Removed intermediate container 84f9c3250023
 ---> 334166d3c82e
Step 5/7 : COPY . .
 ---> d9db2102769e
Step 6/7 : EXPOSE 3000
 ---> Running in 2cb4f6ea7fdf
 ---> Removed intermediate container 2cb4f6ea7fdf
 ---> 552c57abd346
Step 7/7 : CMD ["npm", "start"]
 ---> Running in bb1934a88d9f
 ---> Removed intermediate container bb1934a88d9f
 ---> cf93ef74dbc1
Successfully built cf93ef74dbc1
Successfully tagged miapp-node:latest


Volvemos a ejecutarlo.


vagrant@ubuntu-jammy:~/sv-dockers/node_app$ docker run -p 3000:3000 miapp-node

> node_app@1.0.0 start
> node index.js

Servidor corriendo en http://localhost:3000

El siguiente paso sera integrarlo en ```docker-compose.yml```.

Usando ```VIM``` agregamos la app a nuestro archivo ```docker-compose.yml```.

```vagrant@ubuntu-jammy:~/sv-dockers$ vim docker-compose.yml```

# Use root/example as user/password credentials
version: '3.1'

services:

  db:
    image: mysql:8.0
    container_name: mysql_master
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: alesil
      MYSQL_DATABASE: masterdb
      MYSQL_USER: alesil
      MYSQL_PASSWORD: master_alesil
    volumes:
      - ./master_data:/var/lib/mysql
    ports:
      - "3306:3306"

  redis:
      image: bitnami/redis:7.0
      environment:
        ALLOW_EMPTY_PASSWORD: "no"
        REDIS_PASSWORD: alesil
        REDIS_AOF_ENABLED: "yes"
      volumes:
        - ./redis_data:/data
      ports:
        - "6379:6379"

  rabbitmq:
    image: rabbitmq:3-management
    container_name: rabbitmq
    environment:
      RABBITMQ_DEFAULT_USER: alesil
      RABBITMQ_DEFAULT_PASS: alesil123

    volumes:
      - ./rabbit_data:/var/lib/rabbitmq
    ports:
      - "5672:5672"
      - "15672:15672"

  app:
    build:
      context: ./node_app
    ports:
      - "3000:3000"
    depends_on:
      - db
      - redis
      - rabbitmq

    # (this is just an example, not intended to be a production configuration)

Levantamos todos los servicios.

vagrant@ubuntu-jammy:~/sv-dockers$ docker-compose up -d --build
Creating network "sv-dockers_default" with the default driver
Building app
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  2.865MB
Step 1/7 : FROM node:18-bullseye
 ---> 4669413746ca
Step 2/7 : WORKDIR /usr/src/app
 ---> Using cache
 ---> 5bc661531734
Step 3/7 : COPY package*.json ./
 ---> Using cache
 ---> 516c0bb2bc34
Step 4/7 : RUN npm install
 ---> Using cache
 ---> 334166d3c82e
Step 5/7 : COPY . .
 ---> Using cache
 ---> d9db2102769e
Step 6/7 : EXPOSE 3000
 ---> Using cache
 ---> 552c57abd346
Step 7/7 : CMD ["npm", "start"]
 ---> Using cache
 ---> cf93ef74dbc1
Successfully built cf93ef74dbc1
Successfully tagged sv-dockers_app:latest
Creating rabbitmq           ... done
Creating sv-dockers_redis_1 ... done
Creating mysql_master       ... done
Creating sv-dockers_app_1   ... done


Verificamos estado

vagrant@ubuntu-jammy:~/sv-dockers$ docker-compose ps
       Name                     Command               State                Ports
----------------------------------------------------------------------------------------------
mysql_master         docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp,:::3306- 
                                                              >3306/tcp, 33060/tcp
rabbitmq             docker-entrypoint.sh rabbi ...   Up      15671/tcp, 0.0.0.0:15672-       
                                                              >15672/tcp,:::15672->15672/tcp, 
                                                              15691/tcp, 15692/tcp, 25672/tcp,
                                                              4369/tcp, 5671/tcp, 0.0.0.0:5672
                                                              ->5672/tcp,:::5672->5672/tcp    
sv-dockers_app_1     docker-entrypoint.sh npm start   Up      0.0.0.0:3000->3000/tcp,:::3000- 
                                                              >3000/tcp
sv-dockers_redis_1   /opt/bitnami/scripts/redis ...   Up      0.0.0.0:6379->6379/tcp,:::6379- 
                                                              >6379/tcp













