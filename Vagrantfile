# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.require_version ">= 2.0.1"

$cpus   = ENV.fetch("ISLANDORA_VAGRANT_CPUS", "1")
$memory = ENV.fetch("ISLANDORA_VAGRANT_MEMORY", "4096")
$hostname = ENV.fetch("ISLANDORA_VAGRANT_HOSTNAME", "claw")
$virtualBoxDescription = ENV.fetch("ISLANDORA_VAGRANT_VIRTUALBOXDESCRIPTION", "IslandoraCLAW")

$vagrantBox = "ubuntu/xenial64"
$vagrantUser = "vagrant"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.provider "virtualbox" do |v|
    v.name = "Islandora CLAW Ansible"
  end

  config.vm.hostname = $hostname

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = $vagrantBox

  # Configure home directory
  home_dir = "/home/" + $vagrantUser

# Configure sync directory
  $sync_folder = $home_dir + "/islandora"
  config.vm.synced_folder ".", $sync_folder, owner: $vagrantUser, group: $vagrantUser, mount_options: ["dmode=770,fmode=440"]

  config.vm.network :forwarded_port, guest: 8000, host: 8000 # Apache
  config.vm.network :forwarded_port, guest: 8080, host: 8080 # Tomcat
  config.vm.network :forwarded_port, guest: 8181, host: 8181 # Karaf
  config.vm.network :forwarded_port, guest: 8282, host: 8282 # Islandora Microservices
  config.vm.network :forwarded_port, guest: 3306, host: 3306 # MySQL
  config.vm.network :forwarded_port, guest: 5432, host: 5432 # PostgreSQL
  config.vm.network :forwarded_port, guest: 8983, host: 8983 # Solr
  config.vm.network :forwarded_port, guest: 8161, host: 8161 # Activemq
  config.vm.network :forwarded_port, guest: 8081, host: 8081 # API-X

  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--memory", $memory]
    vb.customize ["modifyvm", :id, "--cpus", $cpus]
    vb.customize ["modifyvm", :id, "--description", $virtualBoxDescription]
  end

  config.vm.provision :ansible_local do |ansible|
    ansible.provisioning_path = $sync_folder
    ansible.playbook = "playbook.yml"
    ansible.galaxy_role_file = "requirements.yml"
    ansible.galaxy_command = "ansible-galaxy install --role-file=%{role_file} --roles-path=roles/external"
    ansible.limit = "all"
    ansible.inventory_path = "inventory/vagrant"
    ansible.extra_vars = { "islandora_distro" => $vagrantBox }
  end

end
