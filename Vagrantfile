
Vagrant.configure("2") do |config|
    config.berkshelf.enabled = true
  # The path to the Berksfile to use with Vagrant Berkshelf
    config.berkshelf.berksfile_path = "./Berksfile"

  # An array of symbols representing groups of cookbook described in the Vagrantfile
  # to skip installing and copying to Vagrant's shelf.
  # config.berkshelf.only = []

  # An array of symbols representing groups of cookbook described in the Vagrantfile
  # to skip installing and copying to Vagrant's shelf.
  # config.berkshelf.except = []

  config.vm.box = "squeeze"
  config.vm.box_url = "https://s3.amazonaws.com/wa.milton.aws.bucket01/sqeeze.box"

  config.vm.network :private_network, ip: "10.33.10.16"

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", 2048]
  end

  config.ssh.max_tries = 40
  config.ssh.timeout   = 120

  # Forward a port from the guest to the host, which allows for outside
  # computers to access the VM, whereas host only networking does not.
  # config.vm.network :forwared_port, guest: 80, host: 8080,
  #   auto_correct: true
  # config.vm.network :forwared_port, guest: 22, host: 2201,
  #   auto_correct: true

  project = 'proto2'

  config.vm.hostname = "mrm-#{project}"

  config.vm.synced_folder ".", "/var/drupals/#{project}", :nfs => true

  config.vm.provision :chef_solo do |chef|
    chef.json = {
      :mysql => {
        :server_root_password => 'rootpass',
        :server_debian_password => 'debpass',
        :server_repl_password => 'replpass'
      },
      :drupal => {
        :projects => {
          project => {
            :sites => {
              project => {
                :uri => "#{project}.dev",
                :build => TRUE
              }
            },
          }
        }
      },
      :apache => {
        :prefork => {
          :startservers => 5,
          :minspareservers => 5,
          :maxspareservers => 5,
          :serverlimit => 10,
          :maxclients => 10
        }
      }
    }

    chef.add_recipe "solo-helper"
    chef.add_recipe "drupal_projects::node_sites"
  end
end
