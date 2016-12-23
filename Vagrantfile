# vi: set ft=ruby :
MASTER_IP = '192.168.56.174'
# NOTE - building from git can take a lot of time and contain bugs
SALT = 'stable' # stable|git|daily|testing
# version to check out if using git
SALT_VERSION = "v2016.11.2"

boxes = [
  {
    :name       => "es-master-0",
    :mem        => "1024",
    :cpu        => "2",
    :ip         => "192.168.56.170",
    :image      => 'debian/jessie64',
    :saltmaster => false
  },
  {
    :name       => "es-data-0",
    :mem        => "2048",
    :cpu        => "2",
    :ip         => "192.168.56.171",
    :image      => "ubuntu/xenial64",
    :saltmaster => false
  },
  {
    :name       => "es-gw-0",
    :mem        => "2048",
    :cpu        => "2",
    :ip         => "192.168.56.172",
    :image      => "ubuntu/trusty64",
    :saltmaster => false
  },
  {
    :name       => "es-proxy-0",
    :mem        => "2048",
    :cpu        => "2",
    :ip         => "192.168.56.173",
    :image      => "ubuntu/xenial64",
    :saltmaster => false
  },
  {
    :name       => "saltmaster",
    :mem        => "512",
    :cpu        => "2",
    :ip         => MASTER_IP,
    :image      => "ubuntu/trusty64",
    :saltmaster => true
  }
]

Vagrant.configure(2) do |config|
  boxes.each do |opts|
    config.vm.define opts[:name] do |config|
      config.vm.box = opts[:image]
      config.vm.hostname = opts[:name]
      config.vm.network 'private_network',
        ip: opts[:ip]
      config.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--memory", opts[:mem]]
        v.customize ["modifyvm", :id, "--cpus", opts[:cpu]]
      end
      config.vm.provision "shell",
        inline: "grep salt /etc/hosts || sudo echo \"#{MASTER_IP}\"  salt >> /etc/hosts"
      config.vm.provision :salt do |salt|
        salt.minion_config = "vagrant/config/minion"
        salt.masterless = false
        salt.run_highstate = false
        salt.install_type = SALT
        salt.install_master = opts[:saltmaster]
        if opts[:saltmaster] == true
          salt.master_config = "vagrant/config/master"
        end
      end
    end
  end
end
