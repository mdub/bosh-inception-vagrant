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

# Ubuntu 12.04 "precise" EBS-backed i386 AMIs
ami_by_region = {
  "ap-northeast-1" => "ami-b95ad2b8",
  "ap-southeast-1" => "ami-b98bc5eb",
  "ap-southeast-2" => "ami-ebe675d1",
  "eu-west-1" => "ami-f5998981",
  "sa-east-1" => "ami-86d97c9b",
  "us-east-1" => "ami-e5582d8c",
  "us-west-1" => "ami-17e6c852",
  "us-west-2" => "ami-1b6ffe2b",
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

    aws.user_data = <<-'EOT'.gsub(/^\s+/, '')
      #!/bin/sh
      sed -i -e 's/^\(Defaults.*requiretty\)/#\1/' /etc/sudoers
    EOT

    aws.keypair_name = keypair_name
    override.ssh.username = "ubuntu"
    override.ssh.private_key_path = keypair_private_key_path

  end

  config.berkshelf.enabled = true

  config.vm.provision :shell, :inline => <<-BASH
    chef --version || curl https://www.opscode.com/chef/install.sh | bash
  BASH

  config.vm.provision :chef_solo do |chef|
    chef.add_recipe 'apt::default'
    chef.add_recipe 'git'
    chef.add_recipe 'chruby'
    chef.json = {
     "chruby" => {
        "rubies" => {
          "1.9.3-p429" => true
        },
        "default" => "1.9.3-p429"
      }
    }
  end

end
