# Installation Multipass

```
snap install multipass --classic
```

# List of available images

```
multipass find

Image                       Aliases           Version          Description
snapcraft:core18                              20201111         Snapcraft builder for Core 18
snapcraft:core20                              20201111         Snapcraft builder for Core 20
snapcraft:core                                20210430         Snapcraft builder for Core 16
core                        core16            20200818         Ubuntu Core 16
core18                                        20200812         Ubuntu Core 18
18.04                       bionic            20210512         Ubuntu 18.04 LTS
20.04                       focal,lts         20210510         Ubuntu 20.04 LTS
20.10                       groovy            20210511.1       Ubuntu 20.10
21.04                       hirsute           20210513         Ubuntu 21.04
daily:21.10                 devel,impish      20210531         Ubuntu 21.10
appliance:adguard-home                        20200812         Ubuntu AdGuard Home Appliance
appliance:mosquitto                           20200812         Ubuntu Mosquitto Appliance
appliance:nextcloud                           20200812         Ubuntu Nextcloud Appliance
appliance:openhab                             20200812         Ubuntu openHAB Home Appliance
appliance:plexmediaserver                     20200812         Ubuntu Plex Media Server Appliance
```

# Create Nodes

````
multipass launch --name master-k8s   --cpus 2 --mem 2048M --disk 5G
multipass launch --name worker-1-k8s --cpus 2 --mem 2048M --disk 5G
multipass launch --name worker-2-k8s --cpus 2 --mem 2048M --disk 5G
````

Check to list if nodes are availabe

````
multipass list
````

# Install Master 

```
multipass exec master-k8s -- bash -c "curl -sfL https://get.k3s.io | sh -s - --no-deploy=traefik"
````

# Install Workers

```
export K3S_IP_MASTER="https://$(multipass info master-k8s | grep "IPv4" | awk -F' ' '{print $2}'):6443"
export K3S_TOKEN=$(multipass exec master-k8s -- /bin/bash -c "sudo cat /var/lib/rancher/k3s/server/node-token")

multipass exec worker-1-k8s -- /bin/bash -c "curl -sfL https://get.k3s.io | K3S_TOKEN=${K3S_TOKEN} K3S_URL=https://${K3S_IP_MASTER} sh -"
```

Verify if workers are bound to master

```
multipass exec master-k8s -- sudo kubectl get nodes
```
