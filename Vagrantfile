# -*- mode: ruby -*-
# vi: set ft=ruby :

HOSTNAME  = 'appname'
IPNUMBER  = '192.168.50.NN'
DEVDNS    = 'www.appname.local'
TESTDNS   = 'test.appname.local'
STAGEDNS  = 'staging.appname.local'
BOX       = 'ubuntu/wily64'

#############################################################
# You should not have to edit anything below this line
#############################################################

dir = Dir.pwd
# require dir + '/vagrant/Vagrant'

Vagrant.require_version ">= 1.5"

def which(cmd)
    exts = ENV['PATHEXT'] ? ENV['PATHEXT'].split(';') : ['']
    ENV['PATH'].split(File::PATH_SEPARATOR).each do |path|
        exts.each { |ext|
            exe = File.join(path, "#{cmd}#{ext}")
            return exe if File.executable? exe
        }
    end
    return nil
end

Vagrant.configure("2") do |config|

    config.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--memory", 2048]
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    end

    config.vm.box = BOX
    #config.vm.hostname = HOSTNAME

    # Local Machine Hosts
    if defined? VagrantPlugins::HostsUpdater
      # Parse through the vvv-hosts files in each of the found paths and put the hosts
      # that are found into a single array.
      hosts = []
      hosts << DEVDNS
      hosts << TESTDNS
      hosts << STAGEDNS
      # Pass the final hosts array to the hostsupdate plugin so it can perform magic.
      config.hostsupdater.aliases = hosts
    end

    # Default Box IP Address
    config.vm.network :private_network, ip: IPNUMBER
    config.ssh.forward_agent = true
    config.vm.synced_folder ".", "/vagrant", owner: "vagrant", group: "www-data", mount_options: ["dmode=775,fmode=774"]
    config.vm.synced_folder "www/", "/srv/www/", :owner => "www-data", :mount_options => [ "dmode=775", "fmode=774" ]
    #############################################################
    # Ansible provisioning (you need to have ansible installed)
    #############################################################

    provisioner = Vagrant::Util::Platform.windows? ? :guest_ansible : :ansible

    config.vm.provision provisioner do |ansible|
        ansible.playbook = "ansible/playbook.yml"
        if Vagrant::Util::Platform.windows?
		    ansible.raw_arguments = ["--inventory IPNUMBER"]
		end
        ansible.limit = 'all'
        ansible.extra_vars = {
            "ansible_vagrant_devdns" => DEVDNS,
            "ansible_vagrant_testdns" => TESTDNS,
            "ansible_vagrant_stagedns" => STAGEDNS,
            "ansible_vagrant_ip" => IPNUMBER,
            "ansible_vagrant_hostname" => HOSTNAME,
			"ansible_ssh_user" => 'vagrant'
        }
    end

    #config.vm.provision "shell", inline: "sudo service nginx restart", run: "always", privileged: true

end
