# vagrant-elasticsearch-cluster
Vargrant file to kick an elasticsearch cluster

## Usage
Just start your cluster with ```vagrant up``` to kick in with the default setup. To modify the number of nodes in the cluster change NUM_OF_NODES at the top. 

## Notice
I am using it for testing - so the default cluster size is very small (1 nodes) and 'discovery.zen.minimum_master_nodes' is default to 1. Be aware of split-brain ....
