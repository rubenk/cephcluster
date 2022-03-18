# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box_url = 'https://cloud.centos.org/centos/8-stream/x86_64/images/CentOS-Stream-Vagrant-8-20220125.1.x86_64.vagrant-libvirt.box'
  config.vm.box = 'centos/stream8'
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.ssh.forward_agent = true

  config.vm.provider :libvirt do |v|
    v.cpu_mode = 'host-passthrough'
    v.cpus = 2
    v.memory = 2048
    v.video_type = 'vga'
    v.machine_type = 'q35'
    v.random :model => 'random'
    v.memballoon_enabled = false
    v.qemu_use_agent = true
    v.channel :type => 'unix', :target_name => 'org.qemu.guest_agent.0', :target_type => 'virtio'
    v.storage :file
    v.storage :file
  end

  ansible_provision = proc do |ansible|
    ansible.playbook = "playbook.yml"
    ansible.limit = "all"
    ansible.verbose = true
  end

  %w(alpha bravo charlie delta echo).each_with_index do |name, i|
    config.vm.define name do |node|
      node.vm.hostname = name

      node.vm.network :private_network,
        :ip => "192.168.56.#{i+10}",
        :libvirt__dhcp_enabled => false,
        :libvirt__forward_mode => "veryisolated",
        :libvirt__network_name => "ceph",
        :libvirt__mtu => "9000"

      if i == 4
        node.vm.provision :ansible, &ansible_provision
      end
    end
  end
end
