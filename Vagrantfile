# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV['VAGRANT_DEFAULT_PROVIDER'] = 'libvirt'
ENV['VAGRANT_NO_PARALLEL'] = 'yes'

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|

  # We are using boxes from here: https://app.vagrantup.com/generic

  # Main Box
  config.vm.define "collection", primary: true do |srv|
    srv.vm.box = "generic/ubuntu2204"
    srv.vm.network :private_network,
        :type                       => "dhcp",
        :libvirt__network_name      => "test_network",
        :libvirt__domain_name       => "test.local",
        :libvirt__dhcp_enabled      => true,
        :libvirt__network_address   => "192.168.125.42",
        :ip                         => "192.168.125.0",
        :libvirt__netmask           => "255.255.255.0",
        :libvirt__dhcp_start        => "192.168.125.201",
        :libvirt__dhcp_stop         => "192.168.125.250"
    srv.ssh.insert_key = false
    srv.vm.provider "libvirt" do |libv|
      libv.default_prefix = "test_"
      libv.description = 'collection'
      libv.memory = 6144
      libv.cpus = 4
      libv.title = 'collection'
      libv.keymap = "de"
      libv.memorybacking :access, :mode => 'shared'
      libv.memorybacking :source, :type => 'memfd'
    end
    $script = <<-SCRIPT
    apt-get -y update --quiet
    apt-get -y install python3-pip ca-certificates curl gnupg lsb-release
    python3 -m pip install pip --upgrade
    python3 -m pip install -r /vagrant/requirements.txt
    sudo -u vagrant ansible-galaxy collection install -f -r /vagrant/requirements.yml
    mkdir -p /home/vagrant/ansible_collections/checkmk/general
    mkdir -p /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
    apt-get update
    apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
    usermod -aG docker vagrant
    grep "alias ic=" /home/vagrant/.bashrc || echo "alias ic='ansible-galaxy collection build --force ~/ansible_collections/checkmk/general && ansible-galaxy collection install -f ./checkmk-general-*.tar.gz && rm ./checkmk-general-*.tar.gz'" >> /home/vagrant/.bashrc
    grep "alias ap=" /home/vagrant/.bashrc || echo "alias ap='ansible-playbook -i vagrant, '" >> /home/vagrant/.bashrc
    hostnamectl set-hostname collection
    SCRIPT
    srv.vm.synced_folder "./", "/vagrant"
    srv.vm.provision "shell", inline: $script
    srv.vm.synced_folder "./", "/home/vagrant/ansible_collections/checkmk/general/"
  end
  
  # Main Box Old
  config.vm.define "molecule", autostart: false , primary: false do |srv|
    srv.vm.box = "generic/ubuntu2004"
    srv.vm.network :private_network,
        :type                       => "dhcp",
        :libvirt__network_name      => "test_network",
        :libvirt__domain_name       => "test.local",
        :libvirt__dhcp_enabled      => true,
        :libvirt__network_address   => "192.168.125.42",
        :ip                         => "192.168.125.0",
        :libvirt__netmask           => "255.255.255.0",
        :libvirt__dhcp_start        => "192.168.125.201",
        :libvirt__dhcp_stop         => "192.168.125.250"
    srv.ssh.insert_key = false
    srv.vm.provider "libvirt" do |libv|
      libv.default_prefix = "test_"
      libv.description = 'molecule'
      libv.memory = 6144
      libv.cpus = 4
      libv.title = 'molecule'
      libv.keymap = "de"
      libv.memorybacking :access, :mode => 'shared'
      libv.memorybacking :source, :type => 'memfd'
    end
    $script = <<-SCRIPT
    apt-get -y update --quiet
    apt-get -y install python3-pip ca-certificates curl gnupg lsb-release
    python3 -m pip install pip --upgrade
    python3 -m pip install -r /vagrant/requirements.txt
    python3 -m pip install molecule molecule-plugins[docker]
    sudo -u vagrant ansible-galaxy collection install -f -r /vagrant/requirements.yml
    mkdir -p /home/vagrant/ansible_collections/checkmk/general
    mkdir -p /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
    apt-get update
    apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
    usermod -aG docker vagrant
    grep "alias ic=" /home/vagrant/.bashrc || echo "alias ic='ansible-galaxy collection build --force ~/ansible_collections/checkmk/general && ansible-galaxy collection install -f ./checkmk-general-*.tar.gz && rm ./checkmk-general-*.tar.gz'" >> /home/vagrant/.bashrc
    grep "alias ap=" /home/vagrant/.bashrc || echo "alias ap='ansible-playbook -i vagrant, '" >> /home/vagrant/.bashrc
    hostnamectl set-hostname molecule
    SCRIPT
    srv.vm.provision "shell", inline: $script
    srv.vm.synced_folder "./", "/home/vagrant/ansible_collections/checkmk/general/"
  end

  # Ubuntu
  config.vm.define "ansibuntu", autostart: false , primary: false do |srv|
    srv.vm.box = "generic/ubuntu2204"
    srv.vm.network :private_network,
          :libvirt__network_name      => "test_network",
          :libvirt__domain_name       => "test.local",
          :ip                         => "192.168.125.61",
          :libvirt__netmask           => "255.255.255.0",
          :libvirt__host_ip           => "192.168.125.1" 
    srv.ssh.insert_key = false
    srv.vm.provider "libvirt" do |libvirt|
      libvirt.default_prefix = "test_"
      libvirt.description = "ansibuntu"
      libvirt.memory = 2048
      libvirt.cpus = 2
      libvirt.title = "ansibuntu"
      libvirt.memorybacking :access, :mode => 'shared'
      libvirt.memorybacking :source, :type => 'memfd'
    end
    srv.vm.provision "shell",
          inline: "apt-get -y update --quiet && apt-get -y install vim htop curl wget git"
  end

  # Debian
  config.vm.define "debsible", autostart: false , primary: false do |srv|
    srv.vm.box = "generic/debian12"
    srv.vm.network :private_network,
          :libvirt__network_name      => "test_network",
          :libvirt__domain_name       => "test.local",
          :ip                         => "192.168.125.62",
          :libvirt__netmask           => "255.255.255.0",
          :libvirt__host_ip           => "192.168.125.1" 
    srv.ssh.insert_key = false
    srv.vm.provider "libvirt" do |libvirt|
      libvirt.default_prefix = "test_"
      libvirt.description = "debsible"
      libvirt.memory = 2048
      libvirt.cpus = 2
      libvirt.title = "debsible"
      libvirt.memorybacking :access, :mode => 'shared'
      libvirt.memorybacking :source, :type => 'memfd'
    end
    srv.vm.provision "shell",
      inline: "apt-get -y update --quiet && apt-get -y install vim htop curl wget git"
  end

  # CentOS Stream
  config.vm.define "anstream", autostart: false , primary: false do |srv|
    srv.vm.box = "generic/centos9s"
    srv.vm.network :private_network,
          :libvirt__network_name      => "test_network",
          :libvirt__domain_name       => "test.local",
          :ip                         => "192.168.125.63",
          :libvirt__netmask           => "255.255.255.0",
          :libvirt__host_ip           => "192.168.125.1" 
    srv.ssh.insert_key = false
    srv.vm.provider "libvirt" do |libvirt|
      libvirt.default_prefix = "test_"
      libvirt.description = "anstream"
      libvirt.memory = 2048
      libvirt.cpus = 2
      libvirt.title = "anstream"
      libvirt.memorybacking :access, :mode => 'shared'
      libvirt.memorybacking :source, :type => 'memfd'
    end
    srv.vm.provision "shell",
      inline: "dnf --quiet check-update ; dnf -y install vim curl wget git"
  end

  # openSUSE
  config.vm.define "ansuse", autostart: false , primary: false do |srv|
    srv.vm.box = "generic/opensuse42"
    srv.vm.network :private_network,
    :libvirt__network_name      => "test_network",
    :libvirt__domain_name       => "test.local",
    :ip                         => "192.168.125.64",
    :libvirt__netmask           => "255.255.255.0",
    :libvirt__host_ip           => "192.168.125.1" 
    srv.ssh.insert_key = false
    srv.vm.provider "libvirt" do |libvirt|
      libvirt.default_prefix = "test_"
      libvirt.description = "ansuse"
      libvirt.memory = 2048
      libvirt.cpus = 2
      libvirt.title = "ansuse"
      libvirt.memorybacking :access, :mode => 'shared'
      libvirt.memorybacking :source, :type => 'memfd'
    end
    srv.vm.provision "shell",
      inline: "zypper --quiet up -y"
  end

  # SLES15 (No Box for libvirt found!!)
  config.vm.define "ansles", autostart: false , primary: false do |srv|
    srv.vm.box = "saltstack/cicd-sles15"
    srv.vm.network :private_network,
    :libvirt__network_name      => "test_network",
    :libvirt__domain_name       => "test.local",
    :ip                         => "192.168.125.65",
    :libvirt__netmask           => "255.255.255.0",
    :libvirt__host_ip           => "192.168.125.1" 
    srv.ssh.insert_key = false
    srv.vm.provider "libvirt" do |libvirt|
      libvirt.default_prefix = "test_"
      libvirt.description = "ansles"
      libvirt.memory = 2048
      libvirt.cpus = 2
      libvirt.title = "ansles"
      libvirt.memorybacking :access, :mode => 'shared'
      libvirt.memorybacking :source, :type => 'memfd'
    end
    srv.vm.provision "shell",
      inline: "zypper --quiet up -y"
  end


  # Oracle Linux
  config.vm.define "ansoracle", autostart: false , primary: false do |srv|
    srv.vm.box = "generic/oracle8"
    srv.vm.network :private_network,
    :libvirt__network_name      => "test_network",
    :libvirt__domain_name       => "test.local",
    :ip                         => "192.168.125.66",
    :libvirt__netmask           => "255.255.255.0",
    :libvirt__host_ip           => "192.168.125.1" 
    srv.ssh.insert_key = false
    srv.vm.provider "libvirt" do |libvirt|
      libvirt.default_prefix = "test_"
      libvirt.description = "ansoracle"
      libvirt.memory = 2048
      libvirt.cpus = 2
      libvirt.title = "ansoracle"
      libvirt.memorybacking :access, :mode => 'shared'
      libvirt.memorybacking :source, :type => 'memfd'
    end
    srv.vm.provision "shell",
      inline: "dnf --quiet check-update ; dnf -y install vim curl wget git"
  end

  # Windows
  config.vm.define "ansidows", autostart: false , primary: false do |srv|
    srv.vm.box = "peru/windows-server-2019-standard-x64-eval"
    srv.vm.network :private_network,
    :libvirt__network_name      => "test_network",
    :libvirt__domain_name       => "test.local",
    :ip                         => "192.168.125.67",
    :libvirt__netmask           => "255.255.255.0",
    :libvirt__host_ip           => "192.168.125.1" 
    srv.winrm.max_tries = 100
    srv.winrm.retry_delay = 2
    srv.vm.boot_timeout = 180
    srv.vm.hostname = "ansidows"
    srv.vm.provider "libvirt" do |libvirt|
      libvirt.default_prefix = "test_"
      libvirt.description = "ansidows"
      libvirt.memory = 4096
      libvirt.cpus = 2
      libvirt.title = "ansidows"
      libvirt.keymap = "de"
      # libvirt.gui = "false"
      libvirt.memorybacking :access, :mode => 'shared'
      libvirt.memorybacking :source, :type => 'memfd'
    end
    srv.vm.provision "shell",
      inline: "powershell Set-NetFirewallRule -name 'FPS-ICMP4-ERQ-In*' -Enabled true"
  end

end
