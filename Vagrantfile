Vagrant.configure("2") do |vagrant|

  ENV["LC_ALL"] = "en_US.UTF-8"

  vagrant.vm.define "ldap1" do |config|
    config.vm.box = "bento/centos-7"
    config.vm.hostname = "ldap1.sso.local"

    config.vm.synced_folder ".", "/vagrant", disabled: true

    config.vm.network "private_network", ip: "10.55.0.10"

    config.vm.provider "virtualbox" do |v|
      v.name = "sso-ldap1"
    end

    config.vm.provision "ansible" do |ansible|
      #ansible.verbose = "v"
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "master-1.yml"
    end

	config.vm.provision "acis", type: "ansible", run: "never" do |ansible|
		ansible.compatibility_mode = "2.0"
		ansible.playbook = "master-1.yml"
		ansible.tags = ['acis']
	end
  end

  vagrant.vm.define "ldap2" do |config|
	  config.vm.box = "bento/centos-7"
	  config.vm.hostname = "ldap2.sso.local"

	  config.vm.synced_folder ".", "/vagrant", disabled: true

	  config.vm.network "private_network", ip: "10.55.0.20"

	  config.vm.provider "virtualbox" do |v|
		  v.name = "sso-ldap2"
		  #v.customize ["modifyvm", :id, "--memory", "1024"]
		  #v.customize ["modifyvm", :id, "--cpus", "2"]
		  #v.customize ["modifyvm", :id, "--ioapic", "on"]
	  end

	  config.vm.provision "ansible" do |ansible|
		  #ansible.verbose = "v"
		  ansible.compatibility_mode = "2.0"
		  ansible.playbook = "master-2.yml"
	  end

	  config.vm.provision "acis", type: "ansible", run: "never" do |ansible|
		  ansible.compatibility_mode = "2.0"
		  ansible.playbook = "master-2.yml"
		  ansible.tags = ['acis']
	  end
  end

#   vagrant.vm.define "keycloak" do |config|
# 	  config.vm.box = "bento/centos-7"
# 	  config.vm.hostname = "keycloak.sso.local"
#
# 	  config.vm.synced_folder ".", "/vagrant", disabled: true
#
# 	  config.vm.network "private_network", ip: "10.38.9.199"
#
# 	  config.vm.provider "virtualbox" do |v|
# 		  v.name = "keycloak-389ds"
# 		  v.customize ["modifyvm", :id, "--memory", "2048"]
# 		  v.customize ["modifyvm", :id, "--cpus", "2"]
# 	  end
#
# 	  config.vm.provision "ansible" do |ansible|
# 		  ansible.compatibility_mode = "2.0"
# 		  ansible.playbook = "keycloak.yml"
# 	  end
#   end

  vagrant.vm.define "wso2" do |config|
	  config.vm.box = "bento/centos-7"
	  config.vm.hostname = "wso2.sso.local"

	  config.vm.synced_folder ".", "/vagrant", disabled: true

	  config.vm.network "private_network", ip: "10.55.0.30"

	  config.vm.provider "virtualbox" do |v|
		  v.name = "wso2-389ds"
		  #v.customize ["modifyvm", :id, "--memory", "2048"]
		  v.customize ["modifyvm", :id, "--cpus", "2"]
	  end

	  config.vm.provision "ansible" do |ansible|
		  #ansible.verbose = "v"
		  ansible.compatibility_mode = "2.0"
		  ansible.playbook = "wso2.yml"
	  end
  end
end
