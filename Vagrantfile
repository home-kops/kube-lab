nodes = [
  { :hostname => "c1-cp", :ip => "172.16.94.10", :memory => "2048", :cpus => 2, disksize: "100GB" },
  #{ :hostname => "c1-node1", :ip => "172.16.94.11", :memory => "2048", :cpus => 2, disksize: "100GB" },
  #{ :hostname => "c1-node2", :ip => "172.16.94.12", :memory => "2048", :cpus => 2, disksize: "100GB" },
  #{ :hostname => "c1-node3", :ip => "172.16.94.13", :memory => "2048", :cpus => 2, disksize: "100GB" },
]

Vagrant.configure("2") do |config|
    required_plugins = %w( vagrant-disksize )
    _retry = false
    required_plugins.each do |plugin|
        unless Vagrant.has_plugin? plugin
            system "vagrant plugin install #{plugin}"
            _retry=true
        end
    end

    exec "vagrant " + ARGV.join(' ') if (_retry)

    config.vm.box = "ubuntu/jammy64"
    config.disksize.size = "100GB"

    nodes.each do |node_config|
        config.vm.define node_config[:hostname] do |node|
            node.vm.hostname = node_config[:hostname]
            node.vm.network :private_network, ip: node_config[:ip]
            node.disksize.size = node_config[:disksize]

            node.vm.provider "virtualbox" do |vb|
                vb.memory = node_config[:memory]
                vb.cpus = node_config[:cpus]
            end

            config.vm.provision "shell" do |s|
                ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_ed25519.pub").first.strip
                s.inline = <<-SHELL
                  echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
                  echo #{ssh_pub_key} >> /root/.ssh/authorized_keys
                SHELL
            end
        end
    end
end
