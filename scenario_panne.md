alias redis-cli='/export/product/redis/3.2.1/bin/redis-cli'

# find master
./redis_trib_wrapper.sh check $(hostname):50000 | grep M:

# find slave of master
./redis_trib_wrapper.sh check $(hostname):50000 | grep -B2 "replicates 1237847c8f51f14ce3361818f0acd0ad746907dc" grep "S:"


# Perte d'un slave
# Résultat attendu : rien, aucune consequence visible
redis-cli -h slave -p 50000 DEBUG SEGFAULT
./redis_trib_wrapper.sh check $(hostname):50000

# Perte de tous les slaves d'un shard
# Résultat attendu : un des shard va ceder un de ses slaves
redis-cli -h slave1 -p 50000 DEBUG SEGFAULT
redis-cli -h slave2 -p 50000 DEBUG SEGFAULT

# Perte d'un master
# Résultat attendu : un des slaves va devenir master
./redis_trib_wrapper.sh info $(hostname):50000
redis-cli -h master -p 50000 DEBUG SEGFAULT
./redis_trib_wrapper.sh info $(hostname):50000

# Perte d'un shard
# Résultat attendu : le cluster rest accessible, mais il manque un shard, il doit etre rebooter et ezventuellement reshardé
redis-cli -h 10.98.67.182 -p 50000 DEBUG SEGFAULT
redis-cli -h 10.98.68.16 -p 50000 DEBUG SEGFAULT
redis-cli -h 10.98.68.14 -p 50000 DEBUG SEGFAULT
