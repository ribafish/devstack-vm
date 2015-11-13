# -*- mode: ruby -*-
# vi: set ft=ruby :


Vagrant.configure("2") do |config|
    
    # if you have a vagrant-cachier plugin, use it for apt caches
    if Vagrant.has_plugin?("vagrant-cachier")
        config.cache.scope = :box
    end

    config.vm.box = "ubuntu/trusty64"
    config.ssh.forward_agent = true
    # eth1, this will be the endpoint
    config.vm.network :private_network, ip: "192.168.27.100"
    # eth2, this will be the OpenStack "public" network
    # ip and subnet mask should match floating_ip_range var in devstack.yml
    config.vm.network :private_network, ip: "172.24.4.225", :netmask => "255.255.255.0", :auto_config => false
    config.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--memory", 4096]
        vb.customize ["modifyvm", :id, "--cpus", 2]
       	# eth2 must be in promiscuous mode for floating IPs to be accessible
       	vb.customize ["modifyvm", :id, "--nicpromisc3", "allow-all"]
    end
    config.vm.provision :ansible do |ansible|
        ansible.host_key_checking = false
        ansible.playbook = "devstack.yml"
        ansible.verbose = "v"
    end
    config.vm.provision :shell, :inline => "cd devstack; sudo -u vagrant env HOME=/home/vagrant ./stack.sh"
    config.vm.provision :shell, :inline => "ovs-vsctl add-port br-ex eth2"
    config.vm.provision :shell, :inline => "virsh net-destroy default"

end
