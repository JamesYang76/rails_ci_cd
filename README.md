# README

### EC2
```bash
ubuntu$ sudo apt-get update && sudo apt-get -y upgrade
ubuntu$ sudo su
root$ adduser deploy
root$ id deploy
root$ sudo vim /etc/sudoers
# Add below in /etc/sudoers
deploy  ALL=(ALL:ALL) ALL 
#


# or gpasswd -a deployer sudo instead of vim sudo /etc/sudoers

root$ su - deploy

#key
deploy$ ssh-keygen
deploy$ ls -la
deploy$ cd .ssh
deploy$ cat id_rsa.pub
# copy and paste id_rsa.pub into github

deploy$ ssh -T git@github.com
deploy$ vim authorized_keys
# copy and paste ssh(id_rsa.pub) from local machine and codeship ssh

deploy$ cd ..
# rvm
deploy$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
deploy$ \curl -sSL https://get.rvm.io | bash -s stable
deploy$ source /home/deploy/.rvm/scripts/rvm
deploy$ rvm install 2.4.4 && rvm use --default 2.4.4
deploy$ rvm list
deploy$ gem install bundler --no-ri --no-rdoc

# nodejs
deploy$ sudo apt-get install -y nodejs

#postgre
deploy$ sudo apt-get install postgresql postgresql-contrib libpq-dev
deploy$ sudo -u postgres createuser -s deploy
deploy$ sudo -u postgres psql
postgres=# \password deploy 
postgres-# \q
deploy$ sudo -u postgres createdb -O deploy rails_ci_cd_production

deploy$ sudo apt-get install nginx
deploy$ sudo rm /etc/nginx/sites-available/default
deploy$ wget https://raw.githubusercontent.com/puma/puma/master/tools/jungle/upstart/puma-manager.conf
deploy$ wget https://raw.githubusercontent.com/puma/puma/master/tools/jungle/upstart/puma.conf

deploy$ vim puma.conf
# Set below
setuid deploy
setgid deploy
# 

deploy$ sudo cp puma.conf puma-manager.conf /etc/init
deploy$ sudo vim /etc/puma.conf
# copy and paste
/home/deploy/rails_ci_cd
#

deploy$ sudo vim  /etc/environment
# copy and paste below
export SECRET_KEY_BASE=fd0cc2dd5f8e86957c5797b0d1c144a35ed1897455b6ec66a80ddcdf32c0ce253a348a19950e00b925d0c327093fa58f3040271103f9d28f488b7fcfe5fa7fa8
ruby -e 'p ENV["SECRET_KEY_BASE"]'

export RAILS_CI_CD_DATABASE_PASSWORD=deploy
ruby -e 'p ENV["RAILS_CI_CD_DATABASE_PASSWORD"]'
#
$ exit
$ su - deploy
$ echo $SECRET_KEY_BASE
$ echo $RAILS_CI_CD_DATABASE_PASSWORD
$ sudo service nginx restart



```
### local 
```
$ rails new rails_ci_cd -d postgresql
$ cd rails_ci_cd
$ bundle
$ cap install STAGES=production
$ touch ./config/secrets.yml
$ cp config/database.yml config/database.yml.example
$ cp config/secrets.yml config/secrets.yml.example
$ cap production config:init
# EDIT WITH YOUR PARAMETERS:
/config/database.production.yml
/config/secrets.production.yml
#

$ rails g capistrano:nginx_puma:config
# EDIT OR LEAVE AS IS:
config/deploy/templates/nginx_conf.erb
config/deploy/templates/puma.rb.erb
#

$ cap production puma:nginx_config
$ cp ./config/puma.rb ./config/puma/production.rb

# git commit update to git hub
```

### source
```
# gem file
group :development do
 
  gem 'capistrano'
  gem 'capistrano-rvm'
  gem 'capistrano-nginx'
  gem 'capistrano3-puma'
  gem 'capistrano-rails'
  gem 'capistrano-rails-db'
  gem 'capistrano-rails-console'
  gem 'capistrano-upload-config'
  gem 'sshkit-sudo'
end

# /config/secrets.production.yml
production:
  secret_key_base: <%= ENV["SECRET_KEY_BASE"] %>  
```

refer to 
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-rails-app-with-puma-and-nginx-on-ubuntu-14-04
