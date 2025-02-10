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















