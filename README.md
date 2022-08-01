# WHAT? #

Install Vagrant. Install VirtualBox or Parallels Desktop.

Install vagrant plugin dependencies:

* vagrant plugin install vagrant-bindfs
* vagrant plugin install vagrant-hostmanager
* vagrant plugin install vagrant-puppet-install
* vagrant plugin install vagrant-parallels (if using Parallels for emulation)
* vagrant plugin install vagrant-winnfsd (if using Windows)
* sudo apt-get install nfs-kernel-server nfs-common portmap (if using Linux)

# Configuration #

## DNS Name / Boot ##

To boot the machine, specify an environment variable on the command line that sets the DNS name by which you'll access the VM. Use a unique name for each application.

SET_HOSTNAME=xxx.dev.northwestern.edu vagrant up

It's important to set a DNS name that ends in northwestern.edu here so that the browser's contract with the server over cookies allows single sign-on tokens to be passed to the development server. If you do not set a name ending in northwestern.edu, single sign on testing may not work.

## Database ##

The Virtual Machine scripts provision both MySQL and PostgreSQL. Each has two databases with connection details as follows. These should be used in your application's database connection configuration.

database: development
username: devuser
password: devpassword

database: test
username: testuser
password: testpassword

Each also has a superuser account with credentials root/123.

I recommend using GUI tool like PGAdmin on your computer to inspect the state of the database as needed.

## Mail ##

The provisioner creates a local mail server for testing purposes. You should send mail on port 1025. You can retrieve mail on HTTP port 1080 (e.g. http://dev.northwestern.edu:1080).

## Deploying an Application ##

Use the folder called shared to send files into the Virtual Machine. Anything written to shared will appear at /shared inside the virtual machine.

Obviously, review the readme for the application you are deploying to see if there are any special steps needed.

In general, you should expect to be able to bundle install, bundle exec rake db:migrate, bundle exec rake db:seed, and then bundle exec rails s -b 0.0.0.0 to start the Rails server.

Due to some NFS issues with file system permissions, bundle gems that require binary compilation using /vagrant/shared rather than /shared.

# Other Tasks / Miscellany #

If you absolutely need to fix a botched database, you can first try to use the artisan migrator to roll back all of the migrations.

If that doesn't work and you want to revert to fresh, you have two options:

You can drop the database and recreate it:
* sudo -s
* su postgres
* psql
* DROP DATABASE development; CREATE DATABASE development OWNER devuser;
* exit
* exit
* exit

Or, you can destroy the machine entirely and re-provision it with vagrant destroy followed by vagrant up.