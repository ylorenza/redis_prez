class: center, middle

# Redis

---

# Agenda

Redis

Key feature

Other feature

Deep dive : Monothread
Deep dive : Memory
Deep dive : Persistance

Redis cli

Latency doctor

Redis sentinelles

Redis cluster

---

# C'est quoi redis ?
 
 * Key/Value Store
 
 * Toutes les données sont stoquées en mémoire
 
 * Toutes les clées peuvent avoir (ou non) une date d'expiration (TTL)
 
 * Rapide
 
 * Léger
 

![Redis logo](redis_logo.png)

---

# Mais aussi ...

 * La possibilité de persister les données sur disques
 
 * Des types de données avancées (Counter, List, Set, Sorted Set ...)
 
 * Un cache LRU out-of-the-box
 
 * Une fonctionnalitées de Souscription / Publication (Broker de message lite)
 
 * De la réplication (Redis Sentinel)

 * Du Sharding (Redis cluster)
 
 * Très rapide
 
 
---

# Et pour finir (Sur l'intro)

 * Open sources (BSD licensed)
 
 * Une documentation très fourni (https://redis.io/documentation)
 
 * Développé en C ANSI
 
 * Aucune dépendences externes

---

# Focus sur les perfs

 * Mono threadé
 
 * Tout doit etre in-memmory
 
 * Impact de l'activation de la persistance fort
  
 * Tunning Système fortement recommandé
    # TODO a détailler

---

# Focus sur la persistance

 * RDB mode : point-in-time snapshot
 
 * AOF mode : Chaque opération d'écriture est logguées
 
 * On peut activer l'un ou l'autre, les deux ou rien
 
 * Plus de détail ici : https://redis.io/topics/persistence

---

# Exploitation

 * redis_cli: Outils command line de redis
    
    + Autocomplétion et hostorisation
    
    + Interactif ou scripté
 
 * Logs redis : Emplacement paramétrable, pas mal d'information notament sur le parametrage system manquant
 
 * Commandes utiles
    
    + PING : réponse PONG
    
    + CLIENT * : Informations et actions sur les clients
    
    + CONFIG * : Informations et actions sur la configuration
    
    + Toutes les commandes ici : https://redis.io/commands

---

# Monitoring

La commande `INFO` fourni énormement de métriques : 

 * Généralitées redis (version, mode, uptime ...)
 
 * Utilisation CPU et mémoire
 
 * Statistique sur les clients
 
 * Information sur la persistances
 
 * Tous le détail ici : https://redis.io/commands/info


En mode cluster, il y à également la commande : `CLUSTER INFO` et `CLUSTER NODES`, qui donne des information sur létat d'un cluster Redis
Détail ici : https://redis.io/commands#cluster


---

# Redis Cluster

 * Complètement different du mode HA avec redis sentinel
 
 * Permet de Sharder les données entre differents noeuds du cluster (Pour de la performances et du scaling).
 
 * Permet également d'être résilient à un certain nombres de pannes
 
 * Attention : Redis cluster utilise un port tcp supplémentaire pour la communication inter node.
               Ce port n'est pas configurable (a moins de recompiler redis) et vaut le port redis standard + 10000
 
//TODO Schema d'un cluster

---

# Redis Cluster : La résilience

# Redis Cluster : redis_trib.rb

 * Script ruby qui permet de wrapper les commandes redis cluster pour l'exploitation
    
    + status
    
    + création de cluster
    
    + resharding
    
    + etc. (Voir redis_trib.rb help)

---

# Industrialisation VSCT : ENC

 * Minimaliste, juste les informations nécessaire pour trouver le YAML.

 ```
  vsct_redis::install:
   applications:
      pao:
        plateformes:
          PREP1:
            components:
              PAO:
                instances:
                  PAOAROH11RED: {}
          PREP2:
            components:
              PAO:
                instances:
                  PAOAROH21RED: {}
 ```
 
---

# Industrialisation VSCT : YAML

 ```
REDIS:
  - NAME: PAO
    USER: redpaoh1
    REDIS_VERSION: 3.2.1
    MODE: SHARD
    SHARD_OPTION:
      REPLICA_NUMBER: 2
    HOSTS:
      - NAME: AROLA
        IP: 10.98.67.180
        INSTANCES:
          - NAME: PAOAROH11RED
            PORT: 50000
      - NAME: BIZZARONE
        IP: 10.98.68.13
        INSTANCES:
          - NAME: PAOBIZH11RED
            PORT: 50000
 ```
 
---

# Industrialisation VSCT : Collectd

 * Plugin collectd remote repris en interne, basé sur les commandes `INFO` et `CLUSTER`
 
 ```
       PAO-PREP1:
         graphite_prefix: DT.PAO.remote.
         config:
           interval: 60
         modules:
           - redis
 ```
 
 * https://prep.appmetrics.vsct.fr/grafana/dashboard/db/tdc-pao-redis

---

# Sources
 
* https://redis.io

---