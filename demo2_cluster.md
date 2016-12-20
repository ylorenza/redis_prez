ssh -l adminapptest@chiwawa:SSH:yannick_lorenzati wallix.socrate.vsct.fr
ssh arola-bck
sudo su  - redpaoh1
alias redis-cli='/export/product/redis/3.2.1/bin/redis-cli'
ll
cd PAO
ll data/PAOAROH11RED/
ll logs
vi logs/redis_50000.log
ll config
vi config/redis_50000.conf
cd script/cluster_shard_exploit

# creation du cluster
./init_cluster.sh

redis-cli -h $(hostname -i) -p 50000
cluster info
cluster nodes
#fail
set test val1

redis-cli -h $(hostname -i) -p 50000 -c
# ok on est en mode cluster
set test val1

./redis_trib_wrapper.sh
./redis_trib_wrapper.sh info $(hostname -i):50000
./redis_trib_wrapper.sh check $(hostname -i):50000