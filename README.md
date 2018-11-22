# aws_ec2_nginx

## Description:
Downloading the content of this repository gives you possibility to create your own EC2_nginx image(EC2 with nginx installed) with packer tool.
Note that instructions below are tested on ubuntu only.

## Requierments:
- You need packer tool installed on your machine.
- You need AWS account with `aws_access_key` and `aws_secret_access_key` configured.

## Files:
- `ec2_nginx.json` - file which **Packer** use in order to create our ec2_nginx image
- `Gemfile` - Specify the the ruby version, and all gems needed for **Kitchen** test
- `.kitchen.yml` - **Kitchen** configuration file
- `test/integration/default/check_pkg.rb` - Script needed to **Kitchen** in order to test whether nginx is installed on your EC2 image. 

## Instructions HOW to install `Packer`
- Download **Packer** from [here](https://www.packer.io/)

- Click on following link: [Install Packer](https://www.packer.io/intro/getting-started/install.html) 

## Instructions HOW to build your image with **Packer**

- Download the content of **berchev/aws_ec2_nginx** repository: 
```
git clone https://github.com/berchev/aws_ec2_nginx.git
```

- Change to downloaded **aws_ec2_nginx** directory: `cd aws_ec2_nginx`

- You need to export your **aws_access_key** and **aws_secret_access_key** as an env variable in linux:
  ```
  export AWS_ACCESS_KEY_ID="XXXXXXXXXXXXXX"
  export AWS_SECRET_ACCESS_KEY="XXXXXXXXXXXXXXXXXXXXXXXX"
  ```
  
- Note that current `ec2_nginx.json` configuration will create EC2_nginx image in **us-west-2** region. If you do not want that, you need to specify region by your choice. 

- Note that change of the region will led to change in the image (`source_ami`). The `source_ami` specified in ec2.json file could be not available in the region chosen by you.

That's why if you change the **region** you may need to change the **source_ami**, too.
- Run command: `packer build template.json` and wait the script to finish. You will receive notification similar to this one:
  ```
  ==> Builds finished. The artifacts of successful builds are:
  --> amazon-ebs: AMIs were created:
  us-west-2: ami-0470bba1269fef9f5
  ```
  
- Now your EC2_nginx image is created. You may go to your AWS account in order to verify this.
## Prepare your environment for **kitchen**
- Type: `sudo apt-get install rbenv ruby-dev ruby-bundler`
- add to your ~/.bash_profile: 
  ```
  eval "$(rbenv init -)"
  true
  export PATH="$HOME/.rbenv/bin:$PATH"
  ```
- do `. ~/.bash_profile` in order to apply the changes made in .bash_profile 

- Change to the directory with `Gemfile` and type: `bundle install` in order to install all needed gems for the test
## Instructions HOW to prepare your environment for **Kitchen** test
- First, you need to create a new file called `credentials` in **~/.aws** and set the rights to 600
- The credentials file needs to look like this:
  ```
  [default]
  aws_access_key_id="value here" <-- "This is the Access Key ID from IAM for the core user"
  aws_secret_access_key="value here" <-- "This is the secret ID from the CSV file that matches the access key"

  ```
- Then generate [EC2 ssh key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)
- Edit the content of `.kitchen.yml` file according to your needs.
  - change `region:` field with your region
  - change the `availability_zone:` with your availability_zone
  - change the `ssh_key:` with the location of your key
  - change `- name:` under `platform` with name of your machine
  - change `image_id:` with the image you chosen

## Execute the **Kitchen** test
- Type: `bundle exec kitchen list` to list the environment
- Type: `bundle exec kitchen converge` to build environment with kitchen
- Type: `bundle exec kitchen verify` to test the created kitchen environment
- Type: `bundle exec kitchen destroy` in order to destroy the created kitchen environment
- Type: `bundle exec kitchen test` in order to do steps from 3 to 5 in one command


## TODO

