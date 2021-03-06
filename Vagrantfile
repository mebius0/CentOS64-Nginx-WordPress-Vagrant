# -*- mode: ruby -*-
# vi: set ft=ruby :

dir = Dir.pwd
vagrant_dir = File.expand_path(File.dirname(__FILE__))

VAGRANTFILE_API_VERSION = "2"
Vagrant.require_version ">= 1.5"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

	vagrant_version = Vagrant::VERSION.sub(/^v/, '')

	config.vm.provider "virtualbox" do |vb|
		vb.customize ["modifyvm", :id, "--memory", 512]
		vb.customize ["modifyvm", :id, "--natdnsproxy1", "off"]
		vb.customize ["modifyvm", :id, "--natdnshostresolver1", "off"]
	end

	config.vm.box = "chef/centos-6.5"	# 64bit
#	config.vm.box = "chef/centos-6.5-i386"	# 32bit

	config.vm.hostname = "local.dev"
	config.vm.network :private_network, ip: "192.168.100.10"

	config.omnibus.chef_version = :latest

	config.hostsupdater.aliases = ["local.dev.clients", "local.dev.themes", "local.dev.plugins", "local.dev.own", "local.dev.database"]
	config.hostsupdater.remove_on_suspend = true

	config.vm.synced_folder "www/", "/var/www", :create => true, :owner => "vagrant", :mount_options => [ "dmode=755", "fmode=755" ]
	config.vm.synced_folder "cache/nginx/", "/var/cache/nginx", :create => true, :owner => "vagrant", :mount_options => [ "dmode=755", "fmode=755" ]

	config.ssh.forward_agent = true

	config.vm.provision "shell", inline: "sudo yum -y update"

	config.vm.provision "chef_solo" do |chef|

		chef.cookbooks_path = ["cookbooks", "site-cookbooks"]

		chef.json = {
			"nginx" => {
				"user"         => "vagrant",
				"group"        => "vagrant",
				"listen_ports" => ["80", "8080", "443"]
			},
			"mysql" => {
				"bind_address"           => "127.0.0.1",
				"server_debian_password" => "wordpress",
				"server_root_password"   => "wordpress",
				"server_repl_password"   => "wordpress"
			}
		}

		chef.add_recipe "yum::epel"
		chef.add_recipe "yum::remi"
		chef.add_recipe "iptables"
		chef.add_recipe "nginx"
		chef.add_recipe "php"
		chef.add_recipe "mysql"
		chef.add_recipe "wp-cli"
		chef.add_recipe "wp-setup"

	end

end
