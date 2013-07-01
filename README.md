Inception
=========

Create a "config.yml" file in this directory, using the following template:

    aws_access_key_id: YOURKEY
    aws_secret_access_key: YOURSECRET
    aws_region: AWS_REGION
    aws_keypair_name: YOURKEYPAIR
    aws_keypair_private_key_path: YOURKEYPAIR.pem

Install [Vagrant](http://www.vagrantup.com).

Install the [vagrant-aws](https://github.com/mitchellh/vagrant-aws) plugin.

    $ vagrant plugin install vagrant-aws

Then:

    $ vagrant up --provider aws
    $ vagrant ssh
