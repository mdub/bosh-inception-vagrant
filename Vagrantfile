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

ami_by_region = {
  "ap-northeast-1" => "ami-6b26ab6a",
  "ap-southeast-1" => "ami-2b511e79",
  "ap-southeast-2" => "ami-84a333be",
  "eu-west-1" => "ami-3d160149",
  "sa-east-1" => "ami-28e43e35",
  "us-east-1" => "ami-c30360aa",
  "us-west-1" => "ami-d383af96",
  "us-west-2" => "ami-bf1d8a8f"
}

Vagrant.configure("2") do |config|

  config.vm.box = "aws-dummy"
  config.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"

  config.vm.provider :aws do |aws, override|

    aws.access_key_id = access_key
    aws.secret_access_key = secret_key

    aws.region = region
    aws.ami = ami_by_region[region]

    aws.tags = { "Name" => "bosh-inception" }

    aws.user_data = <<-'EOT'.gsub(/^\s+/, '')
      #!/bin/sh
      sed -i -e 's/^\(Defaults.*requiretty\)/#\1/' /etc/sudoers
    EOT

    aws.keypair_name = keypair_name
    override.ssh.username = "ubuntu"
    override.ssh.private_key_path = keypair_private_key_path

  end
end
