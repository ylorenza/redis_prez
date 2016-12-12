ssh -l adminapptest@chiwawa:SSH:yannick_lorenzati wallix.socrate.vsct.fr
ssh arola-bck
sudo su  - redpaoh1
alias redis-cli='/export/product/redis/3.2.1/bin/redis-cli'
cd PAO
./SHUT && ./BOOT
vi logs/redis_50000.log

redis-cli -h $(hostname -i) -p 50000 PING
redis-cli -h $(hostname -i) -p 50000
$ set test val1
$ set test2 val2
$ get test2
$ keys *
$ info
$ info cpu

