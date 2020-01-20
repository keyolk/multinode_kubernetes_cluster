box_image = "peru/ubuntu-16.04-server-amd64"
node_count = 3
ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip


Vagrant.configure(2) do |config|
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = false
  config.vm.synced_folder ".", "/vagrant", :disabled => true
  config.vm.box = box_image
  # Prevent TTY Errors (copied from laravel/homestead: "homestead.rb" file)... By default this is "bash -l".
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

  config.vm.provider :libvirt do |domain|
    domain.cpus = 1
    domain.memory = 3072
    domain.driver = "kvm"
    domain.management_network_address = "172.19.0.0/16"
  end

  (1..node_count).each do |i|
    config.vm.define "node#{i}" do |config|
      config.vm.hostname = "node#{i}"
      config.hostmanager.ignore_private_ip = true
      config.vm.network :private_network,
        :ip => "172.20.0.#{i+10}",
        :mac => "52:54:00:00:25:#{i+10}",
        :libvirt__netmask => "255.255.0.0",
        :libvirt__network_name => "kubernetes",
        :libvirt__dhcp_enabled => false,
        :libvirt__forward_mode => "route"
    end
  end

  config.vm.provision 'shell', inline: "install -m 0700 -d /root/.ssh/; echo #{ssh_pub_key} >> /root/.ssh/authorized_keys; chmod 0600 /root/.ssh/authorized_keys"
  config.vm.provision 'shell', inline: "echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys", privileged: false
end
