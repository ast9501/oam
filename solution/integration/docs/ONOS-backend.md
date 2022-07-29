# Use ONOS as SDN-C

# System Architecture
TBD

# Deploy
## Deploy common components (DMaaP, Keycloak, etc)
```
cd oam/solution/integration
sudo docker-compose -f smo/common/docker-compose.yml up -d
python3 smo/common/identity/config.py
```
## Deploy oam components
```
sudo docker-compose -f smo/oam/docker-compose.yml up -d
```

* Enable ONOS netconf apps
```
# Connect to onos (password: karaf)
ssh -p 8101 -o StrictHostKeyChecking=no karaf@localhost

# onos cli
karaf@root > app activate org.onosproject.drivers.netconf
```

* Enable apps for support ODLUX
```
# copy apps into container
cd oam/app
sudo docker cp Netconf-logger/target/netconf-logger-1.0-SNAPSHOT.oar onos:/
sudo docker cp ONOS-DMaaP-agent/o1-netconf-agent-app/target/o1-netconf-agent-app-1.0-SNAPSHOT.oar onos:/ 

docker exec -ti onos bash
apt update && apt install curl python -y
cd ~/onos/bin

# install apps
./onos-app localhost install! /netconf-logger-1.0-SNAPSHOT.oar
./onos-app localhost install! /o1-netconf-agent-app-1.0-SNAPSHOT.oar
```

onos logs (apps activated)
```
03:14:22.479 INFO  [ApplicationManager] Application winlab.nctu.netconfLogger has been installed
03:14:22.482 INFO  [FeaturesServiceImpl] Adding features: netconf-logger/[1.0.0.SNAPSHOT,1.0.0.SNAPSHOT]
03:14:22.746 INFO  [FeaturesServiceImpl] Changes to perform:
03:14:22.747 INFO  [FeaturesServiceImpl]   Region: root
03:14:22.747 INFO  [FeaturesServiceImpl]     Bundles to install:
03:14:22.747 INFO  [FeaturesServiceImpl]       mvn:nctu.winlab/netconf-logger/1.0-SNAPSHOT
03:14:22.748 INFO  [FeaturesServiceImpl] Installing bundles:
03:14:22.748 INFO  [FeaturesServiceImpl]   mvn:nctu.winlab/netconf-logger/1.0-SNAPSHOT
03:14:22.757 INFO  [FeaturesServiceImpl] Starting bundles:
03:14:22.758 INFO  [FeaturesServiceImpl]   nctu.winlab.netconf-logger/1.0.0.SNAPSHOT
03:14:22.766 INFO  [AppComponent] Started
03:14:22.766 INFO  [AppComponent] ODLUX ONOS agent IP: 172.26.0.3 with service port 8000.
03:14:22.767 INFO  [FeaturesServiceImpl] Done.
03:14:22.768 INFO  [ApplicationManager] Application winlab.nctu.netconfLogger has been activated
03:14:23.017 INFO  [AppComponent] Reconfigured
03:15:05.976 INFO  [ApplicationManager] Application nctu.winlab.app has been installed
03:15:05.981 INFO  [FeaturesServiceImpl] Adding features: o1-netconf-agent-app/[1.0.0.SNAPSHOT,1.0.0.SNAPSHOT]
03:15:06.201 INFO  [FeaturesServiceImpl] Changes to perform:
03:15:06.202 INFO  [FeaturesServiceImpl]   Region: root
03:15:06.202 INFO  [FeaturesServiceImpl]     Bundles to install:
03:15:06.202 INFO  [FeaturesServiceImpl]       mvn:nctu.winlab/o1-netconf-agent-app/1.0-SNAPSHOT
03:15:06.203 INFO  [FeaturesServiceImpl] Installing bundles:
03:15:06.203 INFO  [FeaturesServiceImpl]   mvn:nctu.winlab/o1-netconf-agent-app/1.0-SNAPSHOT
03:15:06.209 INFO  [FeaturesServiceImpl] Starting bundles:
03:15:06.209 INFO  [FeaturesServiceImpl]   nctu.winlab.o1-netconf-agent-app/1.0.0.SNAPSHOT
03:15:06.214 INFO  [AppComponent] Started
```

## Start up devices

```
cd oam/solution/integration
sudo docker-compose -f network/docker-compose.yml up -d
```

## Setup RU callhome (manually)
* Get RU ssh-key
``` 
ssh-keyscan -p 830 <RU ip>
```

* Modify ru.json
the example of ru.json
1. netconf:0.0.0.5:830
the name of netconf device
2. server-key
replace it with key from ssh-keyscan

```
{
  "devices": {
    "netconf:0.0.0.5:830": {
      "netconf-ch": {
        "server-key": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCu9TNBaCch3kBsMZqebFof/GZ7eCLzsa1w4ggviypQebfoGEAUtPKv0zp0JlU3DvDcM6LgA0Zch5dgpJn0uZ2nPZ0NpAf8gymCjattruI8ZLBSgkHC6gWeoHEKAhGyxSfe3paM9VfvcvHSBqUImfxArYtxT5ly0ly796BCnHmbwT6XLmLYb1Hb1VPCJCfO7BtXyDBOk1BmW3tq1NUwKLKcomTDxJf4DtVVQh2ChLsdt54UzyJtcWTiYt00OVYrOoOpaD8XkjXe3JK7CBjHSQMaNOr74bvyI/yUNV0+M8hGGJmAECEthDWaeS7OXGF0Tb+GonjAJ/HuVNrdLeNNOTTgwst7EBI/xMajC9SU6WOHfyaD0GHqo7Lmgnk5eFd5PU5Og5iLb/zOU220aXfxAPcfbOyetIjRKqOmXZW8uCoUnyKwU36zSj+k140YWxb++O/9yzCGnSmXK/N21R7VH7s6HTgaG7f8gvXBDM/rl+kVzE1H/4BxXiIy5IdI0YG6EsE=",
        "username": "netconf",
        "password": "netconf!"
      },
      "basic": {
        "driver": "netconf"
      }
    }
  }
}
```

copy to onos container
```
sudo docker cp ru.json onos:/
```

* Upload ru.json to ONOS
```
docker exec -ti onos bash

# in onos container
cd bin/
./onos-netcfg localhost /ru.json
```
