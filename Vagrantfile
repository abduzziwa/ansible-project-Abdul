Vagrant.configure("2") do |config|
  name = "Abdul"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024
    vb.cpus = 1
  end

  # Control Node
  config.vm.define "ansible-#{name}" do |ans|
    ans.vm.box = "ubuntu/jammy64"
    ans.vm.hostname = "ansible-#{name}"
    ans.vm.network "private_network", ip: "192.168.56.10"

    ans.vm.provision "shell", privileged: true, inline: <<-SHELL
      useradd -m -s /bin/bash #{name} || true
      mkdir -p /home/#{name}/.ssh
      chown #{name}:#{name} /home/#{name}/.ssh
      chmod 700 /home/#{name}/.ssh
    SHELL

    ans.vm.provision "file", source: "id_ansible", destination: "/home/#{name}/.ssh/id_ansible"
    ans.vm.provision "file", source: "id_ansible.pub", destination: "/home/#{name}/.ssh/id_ansible.pub"

    ans.vm.provision "shell", privileged: true, inline: <<-SHELL
      chown #{name}:#{name} /home/#{name}/.ssh/id_ansible*
      chmod 600 /home/#{name}/.ssh/id_ansible
      cat /home/#{name}/.ssh/id_ansible.pub >> /home/#{name}/.ssh/authorized_keys
      apt-get update -y
      apt-get install -y software-properties-common
      apt-add-repository --yes --update ppa:ansible/ansible
      apt-get install -y ansible python3-apt
    SHELL
  end

  # Web server
  config.vm.define "web-#{name}" do |web|
    web.vm.box = "ubuntu/jammy64"
    web.vm.hostname = "web-#{name}"
    web.vm.network "private_network", ip: "192.168.56.11"

    web.vm.provision "shell", privileged: true, inline: <<-SHELL
      useradd -m -s /bin/bash #{name} || true
      mkdir -p /home/#{name}/.ssh
      cat /vagrant/id_ansible.pub >> /home/#{name}/.ssh/authorized_keys
      chown -R #{name}:#{name} /home/#{name}/.ssh
      apt-get update
      apt-get install -y python3 python3-apt unzip
    SHELL
  end

  # Database server
  config.vm.define "db-#{name}" do |db|
    db.vm.box = "centos/stream9"
    db.vm.hostname = "db-#{name}"
    db.vm.network "private_network", ip: "192.168.56.12"

    db.vm.provision "shell", privileged: true, inline: <<-SHELL
      useradd -m -s /bin/bash #{name} || true
      mkdir -p /home/#{name}/.ssh
      cat /vagrant/id_ansible.pub >> /home/#{name}/.ssh/authorized_keys
      chown -R #{name}:#{name} /home/#{name}/.ssh
      dnf install -y python3
    SHELL
  end
end
