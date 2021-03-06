# do NOT just "vagrant up" this whole cluster. not recommended.
# currently, need to do startup.sh and then vagrant up the machines one by on in this order
#  cerebro, brazos, espina, hombros, **manually setup artifactory**, manos, cara

#Berksfile tweak needed per https://github.com/berkshelf/vagrant-berkshelf/issues/237  **/.git

# dependencies:
  # manos => cerebro
  # manos => hombros
  # hombros => brazos
  # hombros => espina
  # hombros => cerebro
  # cara => espina

Vagrant.configure(2) do |config|
  if ARGV[1]=='base'
    config.vm.box = "http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_ubuntu-14.04_chef-provisionerless.box"
  else
    config.vm.box = "opscode-ubuntu-14.04a"  # this box will not be on your machine to start
  end
  config.berkshelf.enabled = true

  # how to boost capacity
      #config.vm.provider :virtualbox do |virtualbox|
      #virtualbox.customize ["modifyvm", :id, "--memory", "1024"]   # e.g. for Chef Server
      #virtualbox.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
    #end

###############################################################################
###################################    base   #################################
###############################################################################

# Called by startup.sh. You should not "vagrant up base" directly, as it also needs
# to be repackaged
#
# purpose of doing this is to save time
# eliminates repeated chef, virtualbox utils & java downloads, also apt-get updates & installs tree & curl)
# also configures ssh & hosts

  config.vm.define "base" do | base |
    base.vm.host_name              ="base.calavera.biz"
    base.vm.network                 "private_network", ip: "192.168.33.29"
    base.vm.network                 "forwarded_port", guest: 22, host: 2229, auto_correct: true
    base.vm.network                 "forwarded_port", guest: 80, host: 8029
    base.vm.network                 "forwarded_port", guest: 8080, host: 8129

    base.vm.synced_folder           ".",         "/home/base"
    base.vm.synced_folder           "./shared", "/mnt/shared"

    #base.vm.provision             :shell, path: "./shell/base.sh"

    base.vm.provision :chef_zero do |chef|
      chef.cookbooks_path           = ["./cookbooks/"]
      chef.add_recipe               "base::default"
      chef.add_recipe               "shared::default"
    end

  end

###############################################################################
###################################    cerebro   ##############################
###############################################################################

  config.vm.define "cerebro" do | cerebro |
    cerebro.vm.host_name           ="cerebro.calavera.biz"
    cerebro.vm.network             "private_network", ip: "192.168.33.30"
    cerebro.vm.network            "forwarded_port", guest: 22, host: 2230, auto_correct: true
    cerebro.vm.network            "forwarded_port", guest: 80, host: 8030
    cerebro.vm.network             "forwarded_port", guest: 8080, host: 8130

    cerebro.vm.synced_folder      ".",         "/home/cerebro"
    cerebro.vm.synced_folder      "./shared", "/mnt/shared"

    #cerebro.vm.provision       :shell, path: "./shell/cerebro.sh"

    cerebro.vm.provision :chef_zero do |chef|
      chef.cookbooks_path         = ["./cookbooks/"]
      chef.add_recipe             "git::default"
      chef.add_recipe             "cerebro::default"
    end
  end

# need a git test

###############################################################################
###################################    brazos     ##############################
###############################################################################

  config.vm.define "brazos" do | brazos |
    brazos.vm.host_name            ="brazos.calavera.biz"
    brazos.vm.network               "private_network", ip: "192.168.33.31"
    brazos.vm.network               "forwarded_port", guest: 22, host: 2231, auto_correct: true
    brazos.vm.network               "forwarded_port", guest: 80, host: 8031
    brazos.vm.network              "forwarded_port", guest: 8080, host: 8131

    brazos.vm.synced_folder        ".",         "/home/brazos"
    brazos.vm.synced_folder        "./shared", "/mnt/shared"

    brazos.vm.provision :chef_zero do |chef|
      chef.cookbooks_path         = ["./cookbooks/"]
      chef.add_recipe             "git::default"
      chef.add_recipe             "localAnt::default"
      chef.add_recipe             "shared::_junit"
      chef.add_recipe             "java7::default"
      chef.add_recipe             "tomcat::default"
      chef.add_recipe             "brazos::default"
    end
  end

###############################################################################
###################################    espina     ##############################
###############################################################################

  config.vm.define "espina" do | espina |
    espina.vm.host_name            ="espina.calavera.biz"
    espina.vm.network               "private_network", ip: "192.168.33.32"
    espina.vm.network               "forwarded_port", guest: 22, host: 2232, auto_correct: true
    espina.vm.network               "forwarded_port", guest: 80, host: 8032
    espina.vm.network              "forwarded_port", guest: 8080, host: 8132

    espina.vm.synced_folder        ".",         "/home/espina"
    espina.vm.synced_folder        "./shared", "/mnt/shared"
    #espina.vm.provision       :shell, path: "./shell/espina.sh"

    espina.vm.provision :chef_zero do |chef|
      chef.cookbooks_path         = ["./cookbooks/"]
      chef.add_recipe             "espina::default"
    end
  end

  # test: http://192.168.33.32:8081, admin/password
  # currently need to manually configure Artifactory url and login/pw in Jenkins main setup
  # select "target repository" in hijoInit setup (defaults to ext-release-local) - this probably will show up in xml export

###############################################################################
###################################    hombros     ##############################
###############################################################################

  config.vm.define "hombros" do | hombros |
    hombros.vm.host_name          ="hombros.calavera.biz"
    hombros.vm.network             "private_network", ip: "192.168.33.33"
    hombros.vm.network            "forwarded_port", guest: 22, host: 2233, auto_correct: true
    hombros.vm.network            "forwarded_port", guest: 80, host: 8033
    hombros.vm.network            "forwarded_port", guest: 8080, host: 8133

    hombros.vm.synced_folder        ".",         "/home/espina"
    hombros.vm.synced_folder        "./shared", "/mnt/shared"

    #hombros.vm.provision          :shell, path: "./shell/hombros.sh"

    hombros.vm.provision :chef_zero do |chef|
      chef.cookbooks_path           = ["./cookbooks/"]
      chef.add_recipe               "git::default"
      chef.add_recipe               "jenkins::master"
      chef.add_recipe               "hombros::default"
    end

  end

  # Jenkins should appear at http://192.168.33.33:8080

###############################################################################
###################################    manos     ##############################
###############################################################################

  config.vm.define "manos" do | manos |
    manos.vm.host_name            ="manos.calavera.biz"
    manos.vm.network              "private_network", ip: "192.168.33.34"
    manos.vm.network              "forwarded_port", guest: 22, host: 2234, auto_correct: true
    manos.vm.network              "forwarded_port", guest: 80, host: 8034
    manos.vm.network              "forwarded_port", guest: 8080, host: 8134

    manos.vm.synced_folder        ".",         "/home/manos"
    manos.vm.synced_folder        "./shared", "/mnt/shared"
    #manos.vm.provision         :shell, path: "./shell/manos.sh"

    manos.vm.provision :chef_zero do |chef|
      chef.cookbooks_path         = ["./cookbooks/"]
      chef.add_recipe             "git::default"
      chef.add_recipe             "localAnt::default"
      chef.add_recipe             "java7::default"   # for some reason the Java recipe must be re-run to install Tomcat
      chef.add_recipe             "tomcat::default"
      chef.add_recipe             "shared::_junit"
      chef.add_recipe             "manos::default"
    end

  end

  # test: http://192.168.33.34:8080/MainServlet
  # if cerebro is configured:
  # git add .
  # git commit -m "some message"
  # git push origin master

###############################################################################
###################################    cara     ##############################
###############################################################################
""
  config.vm.define "cara" do | cara |
    cara.vm.host_name              ="cara.calavera.biz"
    cara.vm.network                 "private_network", ip: "192.168.33.35"
    cara.vm.network                 "forwarded_port", guest: 22, host: 2235, auto_correct: true
    cara.vm.network                 "forwarded_port", guest: 80, host: 8035
    cara.vm.network                "forwarded_port", guest: 8080, host: 8135

    cara.vm.synced_folder          ".",         "/home/cara"
    cara.vm.synced_folder          "./shared", "/mnt/shared"
    #cara.vm.provision       :shell, path: "./shell/cara.sh"]

    cara.vm.provision :chef_zero do |chef|
      chef.cookbooks_path         = ["./cookbooks/"]
      chef.add_recipe             "java7::default"
      chef.add_recipe             "tomcat::default"
      chef.add_recipe             "cara::default"
    end
  end
  # test: http://192.168.33.35:8080/MainServlet


###############################################################################
###################################    test     ##############################
###############################################################################
# a test machine to try out new things
# not part of the pipeline

  config.vm.define "test" do | test |
    test.vm.host_name              ="test.calavera.biz"
    test.vm.network                 "private_network", ip: "192.168.33.99"
    test.vm.network                 "forwarded_port", guest: 22, host: 2299, auto_correct: true
    test.vm.network                 "forwarded_port", guest: 80, host: 8099
    test.vm.network                "forwarded_port", guest: 8080, host: 8199

      test.vm.synced_folder         ".", "/home/test"
      test.vm.synced_folder         "./shared", "/mnt/shared"
    #test.vm.provision       :shell, path: "./shell/test.sh"

    test.vm.provision :chef_zero do |chef|
      chef.cookbooks_path =       ["./cookbooks/"]
      chef.add_recipe             "test::default"
    end
  end
###############################################################################

end
