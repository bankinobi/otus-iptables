# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
:inetRouter => {
  :box_name => "centos/7",
        :net => [
                    {ip: '192.168.255.1', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "router-net"}
                ]
  },
  :inetRouter2 => {
        :box_name => "centos/7",
        :net => [
                    {ip: '192.168.250.1', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "router-net2"}
                ],
  },
  :centralRouter => {
        :box_name => "centos/7",
        :net => [
                    {ip: '192.168.255.2', adapter: 2, netmask: "255.255.255.252", gateway: "192.168.255.1", virtualbox__intnet: "router-net"},
                    {ip: '192.168.250.2', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "router-net2"},
                    {ip: '192.168.0.1', adapter: 4, netmask: "255.255.255.252", virtualbox__intnet: "central-net"},
                ]
  },
  :centralServer => {
        :box_name => "centos/7",
        :net => [
                    {ip: '192.168.0.2', adapter: 2, netmask: "255.255.255.252", gateway: "192.168.0.1", virtualbox__intnet: "central-net"}
                ]
  },
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

    config.vm.define boxname do |box|

        box.vm.box = boxconfig[:box_name]
        box.vm.host_name = boxname.to_s

        boxconfig[:net].each do |ipconf|
          box.vm.network "private_network", ipconf
        end

        if boxname.to_s == "inetRouter2"
          box.vm.network "forwarded_port", guest: 8080, guest_ip: "192.168.250.1", host: 9090, host_ip: "127.0.0.1",  protocol: "tcp"
        end

        box.vm.provision "shell", inline: <<-SHELL
          mkdir -p ~root/.ssh
                cp ~vagrant/.ssh/auth* ~root/.ssh
        SHELL

        box.vm.provision "ansible" do |ansible|
          ansible.become = true
          ansible.verbose = "v"
          ansible.playbook = "provision/provision.yaml"
        end 

      end

  end

end
