$ansiblePrereq = <<-SHELL
      sudo apt-add-repository ppa:ansible/ansible -y
      sudo apt-get update
      sudo apt-get install ansible -y
SHELL

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
    v.cpus = 2
  end
  
  config.vm.define "master" do |master|
    master.vm.box = "ubuntu/bionic64"
    master.vm.network "private_network", ip: "192.168.56.10"
    master.vm.hostname = "master"
    master.vm.provision "shell", inline: $ansiblePrereq
    master.vm.synced_folder "./ubuntu", "/opt/scripts"
    master.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "/opt/scripts/site.yml"
      ansible.verbose = true
      ansible.host_vars = {
        "master" => {
          "remote_user" => "vagrant",
          "node_ip" => "192.168.56.10"
        }
      }
    end
  end
  
  (1..2).each do |i|
    config.vm.define "worker-#{i}" do |worker|
      worker.vm.box = "ubuntu/bionic64"
      worker.vm.network "private_network", ip: "192.168.56.#{i + 10}"
      worker.vm.hostname = "worker-#{i}"
      worker.vm.provision "shell", inline: $ansiblePrereq
      worker.vm.synced_folder "./ubuntu", "/opt/scripts"
      worker.vm.provision "ansible_local" do |ansible|
        ansible.playbook = "/opt/scripts/site.yml"
        ansible.host_vars = {
          "worker-#{i}" => {
            "remote_user" => "vagrant",
            "node_ip" => "192.168.56.#{i + 10}"
          }
        }
      end
    end
  end
  
end
