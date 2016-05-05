# vagrant-wpskeleton
A Vagrant skeleton for setting up WordPress client sites. Uses Ansible for provisioning.

## Usage

    wget https://github.com/eriktorsner/vagrant-wpskeleton/archive/master.zip
    unzip master.zip
    mv vagrant-wpskeleton-master/* .
    rm -rf vagrant-wpskeleton-master master.zip

edit Vagrantfile to suit project name and IP requirements, then

    vagrant up
  
Good luck!
