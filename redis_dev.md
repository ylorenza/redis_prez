class: center, middle

# Redis

---

# Agenda

* C'est quoi Redis

* Les data types de Redis

* Redis Cluster

* Exploitation

* Monitoring

---

# C'est quoi redis ?
 
 * Un Key/Value Store
 
 * Tout est stoqué en mémoire
 
 * Toutes les clées peuvent avoir (ou non) une date d'expiration (TTL)
 
 * C'est Rapide (sur une vm vsct avec 2 coeur et 12go de ram 80000 get/s et 100000 set/s)
 
 * C'est Léger ( Un redis vide occupe 1MB de mémoire)
 

![Redis logo](images/redis_logo.png)

---

# Mais aussi ...

 * La possibilité de persister les données sur disques
 
 * Des types de données avancées (Counter, List, Set, Sorted Set ...)
 
 * Un cache LRU out-of-the-box
 
 * Une fonctionnalité de Souscription / Publication (Broker de message léger)
 
 * De la réplication (Redis Sentinel)

 * Du Sharding (Redis cluster)
 
---

# Et pour finir (Sur l'intro)

 * C'est Open sources (BSD licensed)
 
 * Le est Code claire
 
 * Une documentation très fournie (https://redis.io/documentation)
 
 * Développé en C ANSI
 
 * Pas de dépendances externes

---

# Focus sur les perfs

 * Mono threadé
 
 * Tout doit être in-memory (Pas de swapping)
 
 * Impact de l'activation de la persistance fort
 
 TODO plus de chiffre ici

---

# Focus sur la persistance

 * RDB(Redis Database File) mode : snapshot régulier
 
 * AOF(Append Only File) mode : Chaque opération d'écriture est logguée
 
 * On peut activer l'un ou l'autre, les deux ou rien
 
 * Plus de détails ici : https://redis.io/topics/persistence

---
# Les Datatypes Redis

TODO

---

# Exploitation
 
 * redis_cli: Outils command line de redis
    
    + Autocomplétion et historisation des commandes
    
    + Mode interactif ou scripté
    
 * Commandes utiles
    
    + PING : réponse PONG
    
    + CONFIG * : Informations et actions sur la configuration
    
    + Toutes les commandes ici : https://redis.io/commands

 * Astuce : un simple nc ou telnet peut faire office de redis_cli
 
---

# Monitoring

La commande `INFO` fournit énormement de métriques : 

 * Généralitées sur redis (version, mode, uptime ...)
 
 * Utilisation CPU et mémoire
 
 * Statistique sur les clients
 
 * Information sur la persistances
 
 * Tous le détail ici : https://redis.io/commands/info


En mode cluster, il y a également les commandes : 
 
 * `CLUSTER INFO` 

 * `CLUSTER NODES`
 
 * Détail ici : https://redis.io/commands#cluster


---

# Redis Cluster

 * Complètement différent du mode HA avec redis sentinel
 
 * Permet de Sharder les données entre les differents noeuds du cluster (Pour la performance et du scalabilité)
 
 * Permet également d'être résilient à un certain nombres de pannes
 
 * Attention : Redis cluster utilise un port tcp supplémentaire pour la communication inter node. 
 Ce port n'est pas configurable (à moins de recompiler redis) et vaut le port redis standard + 10000
 

---
# Redis Cluster

![Redis cluster](images/cluster_base.svg)

---

# Redis Cluster : La résilience

 * Il faut toujours une majorité de master UP, ainsi qu'un slave par master DOWN sinon la totalité du cluster sera DOWN.
 Ceci afin d'éviter un "Split brain"
 
 * Si nous avons la majorité, lorsqu'un noeud master est DOWN, il sera automatiquement remplacé par un de ses réplicas.
 L'operation prend la valeur du paramètre `cluster-node-timeout` plus quelques secondes.

 * Si un master n'a plus de slave, redis cluster va automatiquement lui en assigner un si des masters disposent de plus d'un slave

 * Attention : Si la persistance est désactivée et qu'un master est DOWN,
 mais revient (ex : supervision monit) avant une réélection, les données de ce noeud seront perdues.

---

# Redis Cluster : La résilience - Perte d'un master

![Redis one master down 1](images/redis_perte_master_1.svg)

---

# Redis Cluster : La résilience - Perte d'un master

![Redis one master down 2](images/redis_perte_master_2.svg)

---

# Redis Cluster : La résilience - Perte de tous les slaves d'un master

![Redis all slaves down 1](images/redis_perte_all_slaves_1.svg)

---

# Redis Cluster : La résilience - Perte de tous les slaves d'un master

![Redis all slaves down 2](images/redis_perte_all_slaves_2.svg)

---

# Redis Cluster : La résilience - Perte de la majorité des masters

![Redis majority of master down](images/redis_2_master.svg)

---

# Redis Cluster : redis_trib.rb

 * Script ruby qui permet de wrapper les commandes redis cluster pour l'exploitation
    
    + status
    
    + création de cluster
    
    + resharding
    
    + rebalancing
    
    + etc. (Voir redis_trib.rb help)
