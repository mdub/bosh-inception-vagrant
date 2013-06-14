require 'yaml'

inception_config = YAML.load_file("config.yml")

access_key = inception_config.fetch("aws_access_key_id")
secret_key = inception_config.fetch("aws_secret_access_key")
region = inception_config.fetch("aws_region", "ap-southeast-2")

keypair_name = inception_config.fetch("aws_keypair_name", "inception")
keypair_private_key_path = inception_config.fetch("aws_keypair_private_key_path") do
  File.expand_path("../#{keypair_name}.id_rsa", __FILE__)
end

unless File.exist?(keypair_private_key_path)
  raise "missing private key: #{keypair_private_key_path}"
end

# Ubuntu 13.04 "raring" EBS-backed i386 AMIs
ami_by_region = {
  "ap-northeast-1" => "ami-39961e38",
  "ap-southeast-1" => "ami-65763837",
  "ap-southeast-2" => "ami-dbf86be1",
  "eu-west-1" =>"ami-61cede15",
  "sa-east-1" =>"ami-aed174b3",
  "us-east-1" =>"ami-ffd1bb96",
  "us-west-1" =>"ami-851836c0",
  "us-west-2" =>"ami-9179e8a1",
}

Vagrant.configure("2") do |config|

  config.vm.box = "aws-dummy"
  config.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"

  config.vm.provider :aws do |aws, override|

    aws.access_key_id = access_key
    aws.secret_access_key = secret_key

    aws.region = region
    aws.ami = ami_by_region[region]
    aws.security_groups = ["ssh"]

    aws.tags = { "Name" => "bosh-inception" }

    # aws.user_data = <<-'EOT'.gsub(/^\s+/, '')
    #   #!/bin/sh
    #   sed -i -e 's/^\(Defaults.*requiretty\)/#\1/' /etc/sudoers
    # EOT

    aws.keypair_name = keypair_name
    override.ssh.username = "ubuntu"
    override.ssh.private_key_path = keypair_private_key_path

  end

  config.berkshelf.enabled = true

  config.vm.provision :chef_solo do |chef|
    chef.add_recipe 'apt::default'
    chef.add_recipe 'git'
  end

end
