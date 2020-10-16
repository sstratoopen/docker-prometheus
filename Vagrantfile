# -*- mode: ruby -*-
# # vi: set ft=ruby :

# We set the last octet in IPV4 address here
nodes = {
 'controller' => [1, 200],
}

Vagrant.configure("2") do |config| 
  #  config.vm.box = "ubuntu/" + (ENV['UBUNTU_RELEASE'] || "trusty") + "64" 

  config.vm.box = "bento/ubuntu-18.04"
#  config.vm.box = "ubuntu/" + (ENV['UBUNTU_RELEASE'] || "trusty") + "64" + "/"+(ENV['SIZE'])
  # VMware Fusion / Workstation
  
  # Default is 2200..something, but port 2200 is used by forescout NAC agent.
  config.vm.usable_port_range= 2800..2900
  nodes.each do |prefix, (count, ip_start)|
    count.times do |i|
      hostname = "%s" % [prefix, (i+1)]

      config.vm.define "#{hostname}" do |box|
        box.vm.hostname = hostname 
        box.vm.network :private_network, ip: "192.168.100.#{ip_start+i}", :netmask => "255.255.255.0"
        box.vm.provider :virtualbox do |vbox|
          # Defaults
          vbox.customize ["modifyvm", :id, "--memory", 4096]
          vbox.customize ["modifyvm", :id, "--cpus", 4]
          vbox.customize ["modifyvm", :id, "--nicpromisc3", "allow-all"]
          vbox.customize ["modifyvm", :id, "--nicpromisc4", "allow-all"]
      	  $script = <<SCRIPT
	        apt-get update 
          sudo ufw disable
          apt-get remove docker docker-engine docker.io containerd runc -y
          export DEBIAN_FRONTEND=noninteractive

          apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
          curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
          add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
          apt-get update 
          apt-get install -y docker-ce docker-ce-cli containerd.io
          sudo apt-get install docker-compose git -y
SCRIPT
          if prefix == "controller" 
            vbox.gui = true
            config.vm.provision "shell", inline: $script 
            config.vm.network "forwarded_port", guest: 80, host: 8080
          end 
        end # box.vm virtualbox
      end # config.vm.define 
    end # count.times
  end # nodes.each
end # Vagrant.configure("2")
