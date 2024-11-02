# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "server" do |config|
    config.vm.box = "bento/ubuntu-22.04"

    # Configuración del proveedor VirtualBox
    config.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"  # 2 GB de RAM
      vb.cpus = 2         # 2 CPUs
    end

    config.vm.network :private_network, ip: "192.168.50.7"

    # Aprovisionamiento mediante Shell para instalar Ansible
    config.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt install -y ansible
      sudo apt install -y software-properties-common
      sudo useradd -m -s /bin/bash administrador
      echo 'administrador:admin_password' | sudo chpasswd
      sudo useradd -m -s /bin/bash tester
      echo 'tester:tester_password' | sudo chpasswd
      sudo useradd -m -s /bin/bash appUser
      echo 'appUser:appuser_password' | sudo chpasswd

      # Instalar OpenSSH Server si no está instalado
      sudo apt install -y openssh-server
      sudo systemctl enable ssh
      sudo systemctl start ssh

      # Configurar acceso SSH
      for user in administrador tester appUser; do
        sudo mkdir -p /home/$user/.ssh
        echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDXAiqofTHgEOJWXoyhvDpVPbt8LGkB+IDOV5Zcon+TEd9lP9cdinQJBJb7h6HsXBaMgdqN9UBLGESS70yjb2WDSiV/r5+Occ+uRBLQoF1BA24Eo8Z4pIdKInb3I51FSHyBFNvgmXCqzGsruol3f0+xZ9ZzeFskSVNmysRBdkvts6BvW60jS1dMQfBgD1883CwNwXVAAOV4c0avCkro8vk8kX61CL7BBLmcAcGou992v9gUsWm12vo3lveV7ZYWQd+2L8nxzwiNAhJiYnmZpBgUSzoqthY4+ZskW3bQRiQdKkP702JvKOoKzTuPNwqjJ2SInVuHxx5/p8LEj1TFbUZbpPdymrHpUmgZ6gOtJNZt+ryZtYGjY9jJjnOppyAat7nyKI3Ritn38g1BJYVt/PQbX3atwZcr5hG6peiZ/tYnn3UlnDftPG9d6TomcSPRWdldarAdTp77GlHBZpLhH+iwNyWfSArzLT+5hZgVsbZr58MApB4prwrt8xqDhvGB6ak/tq5V1KlUb2ympz9wG7v+/knvwvppe5XDSxz0GgavTd4+VIV5rEYEsNl9+ZPSFJLp4N01bQ9wyiCs72pHACQnSHpiAOZ/R4pWwyDzNaLuVc8cAYm4qzxwHxK0z5FHE+1h/qO4Us01Bijce1tITz6obKiwdith3qflKIbSFW8tUQ== josem@JoseLaptop" | sudo tee /home/$user/.ssh/authorized_keys
        sudo chown -R $user:$user /home/$user/.ssh
        sudo chmod 700 /home/$user/.ssh
        sudo chmod 600 /home/$user/.ssh/authorized_keys
      done

    SHELL

    # Aprovisionamiento con Ansible local
    config.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "/vagrant/nginx_server.yml"
    end
  end

  # --------------------------------------------------------------------------

  # Segunda máquina virtual - monitoring (Prometheus y Grafana)
  config.vm.define "monitoring" do |monitor|
    monitor.vm.box = "bento/ubuntu-22.04"

    # Configuración de recursos
    monitor.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"  # Ajusta según los requisitos de Prometheus y Grafana
      vb.cpus = 2
    end

    # Configuración de red
    monitor.vm.network "private_network", ip: "192.168.50.8"

    monitor.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt install -y ansible
      sudo apt install -y software-properties-common
      sudo useradd -m -s /bin/bash administrador
      echo 'administrador:admin_password' | sudo chpasswd
      sudo useradd -m -s /bin/bash tester
      echo 'tester:tester_password' | sudo chpasswd
      sudo useradd -m -s /bin/bash appUser
      echo 'appUser:appuser_password' | sudo chpasswd

      # Instalar OpenSSH Server si no está instalado
      sudo apt install -y openssh-server
      sudo systemctl enable ssh
      sudo systemctl start ssh

      # Configurar acceso SSH
      for user in administrador tester appUser; do
        sudo mkdir -p /home/$user/.ssh
        echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDXAiqofTHgEOJWXoyhvDpVPbt8LGkB+IDOV5Zcon+TEd9lP9cdinQJBJb7h6HsXBaMgdqN9UBLGESS70yjb2WDSiV/r5+Occ+uRBLQoF1BA24Eo8Z4pIdKInb3I51FSHyBFNvgmXCqzGsruol3f0+xZ9ZzeFskSVNmysRBdkvts6BvW60jS1dMQfBgD1883CwNwXVAAOV4c0avCkro8vk8kX61CL7BBLmcAcGou992v9gUsWm12vo3lveV7ZYWQd+2L8nxzwiNAhJiYnmZpBgUSzoqthY4+ZskW3bQRiQdKkP702JvKOoKzTuPNwqjJ2SInVuHxx5/p8LEj1TFbUZbpPdymrHpUmgZ6gOtJNZt+ryZtYGjY9jJjnOppyAat7nyKI3Ritn38g1BJYVt/PQbX3atwZcr5hG6peiZ/tYnn3UlnDftPG9d6TomcSPRWdldarAdTp77GlHBZpLhH+iwNyWfSArzLT+5hZgVsbZr58MApB4prwrt8xqDhvGB6ak/tq5V1KlUb2ympz9wG7v+/knvwvppe5XDSxz0GgavTd4+VIV5rEYEsNl9+ZPSFJLp4N01bQ9wyiCs72pHACQnSHpiAOZ/R4pWwyDzNaLuVc8cAYm4qzxwHxK0z5FHE+1h/qO4Us01Bijce1tITz6obKiwdith3qflKIbSFW8tUQ== josem@JoseLaptop" | sudo tee /home/$user/.ssh/authorized_keys
        sudo chown -R $user:$user /home/$user/.ssh
        sudo chmod 700 /home/$user/.ssh
        sudo chmod 600 /home/$user/.ssh/authorized_keys
      done

    SHELL

    monitor.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "/vagrant/prometheus_grafana.yml"
    end
  end

end

