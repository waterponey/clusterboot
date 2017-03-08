# -*- mode: ruby -*-
# # vi: set ft=ruby :

require 'fileutils'

Vagrant.require_version ">= 1.6.0"

# Defaults for config options defined in CONFIG
$num_instances = 6
$instance_name_prefix = "centos"
$enable_serial_logging = false
$share_home = false
$vm_gui = false
$vm_memory = 3072
$vm_cpus = 2
$forwarded_ports = {5050=>5050, 50070=>50070, 18080=>18080}

# Attempt to apply the deprecated environment variable NUM_INSTANCES to
# $num_instances while allowing config.rb to override it
if ENV["NUM_INSTANCES"].to_i > 0 && ENV["NUM_INSTANCES"]
  $num_instances = ENV["NUM_INSTANCES"].to_i
end

# Use old vb_xxx config variables when set
def vm_gui
  $vb_gui.nil? ? $vm_gui : $vb_gui
end

def vm_memory
  $vb_memory.nil? ? $vm_memory : $vb_memory
end

def vm_cpus
  $vb_cpus.nil? ? $vm_cpus : $vb_cpus
end

Vagrant.configure("2") do |config|
  # always use Vagrants insecure key
  config.ssh.insert_key = false

  config.vm.box = "centos/7"

  # disable install guest additions since it's useless
  config.vbguest.auto_update = false

  # disable default syncing
  config.vm.synced_folder ".", "/vagrant", disabled: true

  # enable hostmanager
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  (1..$num_instances).each do |i|
    config.vm.define vm_name = "%s-%02d" % [$instance_name_prefix, i] do |node|
      node.vm.hostname = vm_name

      if $enable_serial_logging
        logdir = File.join(File.dirname(__FILE__), "log")
        FileUtils.mkdir_p(logdir)

        serialFile = File.join(logdir, "%s-serial.txt" % vm_name)
        FileUtils.touch(serialFile)

        node.vm.provider :virtualbox do |vb, override|
          vb.customize ["modifyvm", :id, "--uart1", "0x3F8", "4"]
          vb.customize ["modifyvm", :id, "--uartmode1", serialFile]
        end
      end

      node.vm.provider :virtualbox do |vb|
        vb.gui = vm_gui
        vb.memory = vm_memory
        vb.cpus = vm_cpus
      end

      $forwarded_ports.each do |guest, host|
        node.vm.network "forwarded_port", guest: guest, host: (host+i), auto_correct: true
      end

      node.vm.network :private_network, ip: "192.168.42.10#{i}"
      node.vm.provision :file, source: "~/.vagrant.d/insecure_private_key", destination:"~/.ssh/id_rsa"

      # configure parallel provisionning using ansible
      if i == $num_instances
        node.vm.provision "ansible" do |ansible|
          ansible.verbose = "v"
          ansible.limit="all"
          ansible.playbook = "playbook.yml"
          ansible.extra_vars= {
            bootstrap: ENV['BOOTSTRAP'] || "false"
          }
          ansible.host_vars = {
            "centos-01" => {"zoo_id" => 1},
            "centos-02" => {"zoo_id" => 2},
            "centos-03" => {"zoo_id" => 3}
          }
          ansible.groups = {
            "masters" => ["centos-0[1:3]"],
            "slaves"  => ["centos-0[4:6]"]
          }
        end
      end
    end
  end

  # configure provisionning using ansible
  #config.vm.provision "ansible" do |ansible|
  #  ansible.verbose = "v"
  #  ansible.playbook = "playbook.yml"
  #  ansible.host_vars = {
  #    "centos-01" => {"zoo_id" => 1},
  #    "centos-02" => {"zoo_id" => 2},
  #    "centos-03" => {"zoo_id" => 3}
  #  }
  #  ansible.groups = {
  #    "masters" => ["centos-0[1:3]"],
  #    "slaves"  => ["centos-0[4:6]"]
  #  }
  #end

end
