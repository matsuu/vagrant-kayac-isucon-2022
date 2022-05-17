# -*- mode: ruby -*-
# vi: set ft=ruby :

box = "generic/ubuntu2004"

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = box

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.
  # https://github.com/ppggff/vagrant-qemu
 
  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL

  config.vm.provision "shell", inline: <<-SHELL
    set -exu
    arch=$(uname -m)
    export HOME=/root

    export DEBIAN_FRONTEND=noninteractive
    apt-get update
    apt-get install -y --no-install-recommends curl docker.io git make mysql-client-core-8.0 rsync sudo

    update-alternatives --set editor /usr/bin/vim.basic
    systemctl enable --now docker
    curl -sL https://github.com/docker/compose/releases/download/v2.4.1/docker-compose-linux-${arch} > /usr/local/bin/docker-compose
    chmod +x /usr/local/bin/docker-compose

    useradd -m -G adm,docker -s /bin/bash isucon
    echo 'isucon ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/isucon
    chmod 440 /etc/sudoers.d/isucon

    cd /tmp
    git clone https://github.com/kayac/kayac-isucon-2022.git isucon
    cd /tmp/isucon
    make dataset
    snap install --classic go
    (
      cd bench
      make bench
    )
    rm -rf .git *.tar.gz
    chown -R isucon:isucon ./
    rsync -av ./ /home/isucon/

    cd /home/isucon/webapp
    if [ "$arch" = "aarch64" ]; then
        sed -i -e "s@mysql/mysql-server:8.0.28@mysql/mysql-server:8.0.28-$arch@" docker-compose.yml
    fi
    chmod 1777 /home/isucon/webapp/mysql/logs
    docker-compose up --build -d

    echo "Restore dump data to mysql-server. It may take a few minutes."
    set +xe
    while sleep 5; do
        echo -n .
        mysql -uroot -proot --host 127.0.0.1 --port 13306 -e "select now()" 2> /dev/null
        if [ $? -eq 0 ]; then
            break
        fi
    done
    rm -rf /tmp/isucon*
    echo "ok"
  SHELL
end
