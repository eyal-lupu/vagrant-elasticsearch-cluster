# -*- mode: ruby -*-
# vi: set ft=ruby :
#

# Set below the number of nodes in your elasticsearch cluster
NUM_OF_NODES=2
HEAP_SIZE="512m"


@initManager = <<EOD
    echo Arguments: 
    echo NUM_OF_NODES = $1
    echo HEAP_SIZE = $2
    echo THIS_NODE = es-$3

    sudo add-apt-repository -y ppa:openjdk-r/ppa
    wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
    echo "deb http://packages.elastic.co/elasticsearch/5.x/debian stable main" | sudo tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
    sudo apt-get update
    sudo apt-get -y --force-yes install openjdk-8-jre
    sudo update-alternatives --config java
    sudo apt-get -y install elasticsearch
    sudo echo 'network.host: [_eth1_]' >> /etc/elasticsearch/elasticsearch.yml
    sudo echo 'cluster.name: es-cluster' >> /etc/elasticsearch/elasticsearch.yml

    sudo echo "node.name: ${HOSTNAME}" >> /etc/elasticsearch/elasticsearch.yml
    #  The below will not hold for production system!
    sudo echo "discovery.zen.minimum_master_nodes: 1" >> /etc/elasticsearch/elasticsearch.yml
    HOSTS=""
    for X in `seq 1 $1` ; do
        let Z="99 + $X"
        NEXT_HOST="192.168.50.$Z"
        HOSTS="$HOSTS, \"$NEXT_HOST\""

        echo "$NEXT_HOST    es-$X" >> /etc/hosts
    done
    HOSTS=${HOSTS:1}
    sudo echo "discovery.zen.ping.unicast.hosts: [${HOSTS}]" >> /etc/elasticsearch/elasticsearch.yml
    
    sudo sed -i "s/^-Xmx[0-9].*/-Xmx$2/g" /etc/elasticsearch/jvm.options
    sudo sed -i "s/^-Xms[0-9].*/-Xms$2/g" /etc/elasticsearch/jvm.options

    sudo update-rc.d elasticsearch defaults 95 10
    sudo /etc/init.d/elasticsearch start

EOD

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.provider "virtualbox" do |v| 
        v.memory = 2048
  end 

  (1..NUM_OF_NODES).each do |nodeNumber|
    config.vm.define "es-#{nodeNumber}" do |node|
      node.vm.hostname = "es-#{nodeNumber}"
      node.vm.network "private_network", ip: "192.168.50.#{99+nodeNumber}"
      node.vm.network "forwarded_port", guest: 9200, host: 9200, auto_correct: true
      node.vm.provision "shell", inline: @initManager, args: [NUM_OF_NODES, HEAP_SIZE, nodeNumber]
    end
  end
end
