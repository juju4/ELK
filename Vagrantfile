# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"
ENV['VAGRANT_DEFAULT_PROVIDER'] = 'virtualbox'
#ENV['VAGRANT_DEFAULT_PROVIDER'] = 'lxc'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/trusty64"

  config.vm.hostname = "elk-vagrant"

  # Configure VM Ram usage: reco 2-4GB
  config.vm.provider "virtualbox" do |v|
    #v.memory = 2048
    v.memory = 4096
  end
#  config.vm.network :public_network
  config.vm.network "public_network", bridge: 'eth0'
  config.vm.network :forwarded_port, guest: 9200, host: 9200
  config.vm.network :forwarded_port, guest: 9300, host: 9300
  config.vm.network :forwarded_port, guest: 5601, host: 5601
  config.vm.network :forwarded_port, guest: 80, host: 6680

#  config.vm.provider :virtualbox do |vb|
#    ## If you want to enable GUI right away
#    #vb.gui = true
#  end

## https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-repositories.html
  config.vm.provision "shell", inline: 'wget -qO - https://packages.elasticsearch.org/GPG-KEY-elasticsearch | sudo apt-key add -'
  config.vm.provision "shell", inline: 'sudo add-apt-repository "deb http://packages.elasticsearch.org/elasticsearch/1.5/debian stable main"'
  config.vm.provision "shell", inline: 'sudo apt-get -y update && sudo env DEBIAN_FRONTEND=noninteractive apt-get -y upgrade'
  config.vm.provision "shell", inline: 'sudo apt-get -y purge rpcbind'
  config.vm.provision "shell", inline: 'sudo env DEBIAN_FRONTEND=noninteractive apt-get -y install screen vim rkhunter openntpd acct apparmor-profiles apparmor-utils acl locate sysstat chromium-browser libxml2-utils git'
  config.vm.provision "shell", inline: 'sudo cp /usr/share/zoneinfo/Canada/Eastern /etc/localtime'
  config.vm.provision "shell", inline: 'sudo perl -pi -e "s@^PermitRootLogin yes@PermitRootLogin no@;s@^PasswordAuthentication yes@PasswordAuthentication no@;" /etc/ssh/sshd_config'

  config.vm.provision "shell", inline: 'sudo apt-get -y install openjdk-7-jre-headless'
  config.vm.provision "shell", inline: 'sudo apt-get -y install elasticsearch'

  config.vm.provision "shell", inline: 'sudo /usr/share/elasticsearch/bin/plugin -install mobz/elasticsearch-head'
  config.vm.provision "shell", inline: 'sudo /usr/share/elasticsearch/bin/plugin -install lukas-vlcek/bigdesk'
  config.vm.provision "shell", inline: 'sudo /usr/share/elasticsearch/bin/plugin -install elasticsearch/marvel/latest'
  config.vm.provision "shell", inline: 'sudo update-rc.d elasticsearch defaults 95 10'
  config.vm.provision "shell", inline: 'echo "Monitor install on http://localhost:9200/_plugin/head/"'

  config.vm.provision "shell", inline: 'wget -q http://download.elasticsearch.org/logstash/logstash/packages/debian/logstash_1.5.0-1_all.deb && sudo dpkg -i logstash_1.5.0-1_all.deb'
  config.vm.provision "shell", inline: 'sudo setfacl -m u:logstash:r-x /var/log'
  config.vm.provision "shell", inline: 'sudo setfacl -m u:logstash:r-- /var/log/*log'
  config.vm.provision "shell", inline: 'wget -q https://download.elastic.co/kibana/kibana/kibana-4.0.2-linux-x64.tar.gz && tar xzf kibana-4.0.2-linux-x64.tar.gz && mv kibana-4.0.2-linux-x64 /opt/kibana4'
  config.vm.provision "shell", inline: 'sudo perl -pi -e "s@#network.host: .+\$@network.host: 127.0.0.1@" /etc/elasticsearch/elasticsearch.yml'
  config.vm.provision "shell", inline: 'echo "script.disable_dynamic: true" | sudo tee -a /etc/elasticsearch/elasticsearch.yml'

## http://www.elasticsearch.org/overview/shield?camp=homepage (commercial, 30d trial)
#  config.vm.provision "shell", inline: 'bin/plugin -i elasticsearch/license/latest'
#  config.vm.provision "shell", inline: 'bin/plugin -i elasticsearch/shield/latest'
  config.vm.provision "shell", inline: 'sudo service elasticsearch restart && sleep 10'
#  config.vm.provision "shell", inline: 'bin/shield/esusers useradd es_admin -r admin'
#  config.vm.provision "shell", inline: 'curl -u es_admin -s -X GET \'http://localhost:9200\''

  config.vm.provision "shell", inline: 'curl -s -X GET "http://localhost:9200"'

  config.vm.provision "shell", inline: 'sudo install -d -o vagrant -m 755 /home/vagrant/logs'
#  config.vm.provision "shell", inline: 'sudo install -m 644 /vagrant/logstash-local-syslog.conf /etc/logstash/conf.d/'
#  config.vm.provision "shell", inline: 'sudo install -m 644 /vagrant/logstash-sinkhole.conf /etc/logstash/conf.d/'
  config.vm.provision "shell", inline: 'sudo install -m 644 /vagrant/logstash-arbornetworks.conf /etc/logstash/conf.d/'
  config.vm.provision "shell", inline: 'sudo install -m 644 /challenges/b/Arbor-Alerts.xml /home/vagrant/logs/'
  #config.vm.provision "shell", inline: 'wget -q https://raw.githubusercontent.com/mcnewton/elk/master/logstash-filters/sanitize_mac.rb && sudo install -m 644 sanitize_mac.rb ...'

  config.vm.provision "shell", inline: 'sudo update-rc.d logstash defaults 99 10'
  config.vm.provision "shell", inline: 'sudo service logstash restart && sleep 10'

  config.vm.provision "shell", inline: 'wget -q http://geolite.maxmind.com/download/geoip/database/GeoLiteCountry/GeoIP.dat.gz http://geolite.maxmind.com/download/geoip/database/GeoIPv6.dat.gz http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz http://download.maxmind.com/download/geoip/database/asnum/GeoIPASNum.dat.gz && gunzip *.gz'

## chromium-browser {http://localhost:9200/_plugin/head,http://localhost:9200/_plugin/bigdesk,http://localhost:9200/_plugin/marvel,http://127.0.0.1:9200/_aliases?pretty,http://localhost:5601}&
## (nohup /opt/kibana4/bin/kibana &) ; sleep 10; curl -s -X GET "http://localhost:5601"
## tail /var/log/logstash/logstash.*
## End configuration by adding index pattern for sinkhole-*, linux-syslog-*, ...

  #config.ssh.forward_x11: true
  config.vm.synced_folder ".", "/vagrant", disabled: false
  config.vm.synced_folder "/opt/tmp/challenges", "/challenges"

end

