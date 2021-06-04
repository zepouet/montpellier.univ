# Multipass

K3s is an ultra-light version of Kubernetes developed by Rancher. It only requires 40MB of disk space and 512MB of RAM to start up because it is primarily intended for light equipment such as IoT, edge transport servers, RaspberryPi among others.
It is a very efficient and lightweight fully compliant Kubernetes distribution. k3d is a utility designed to easily run k3s in Docker, it provides a simple CLI to create, run, delete a fully compliance Kubernetes cluster with 1 to n nodes.

What does this distribution contain?
All Kubernetes components (kube-apiserver, kube-controller-manager, kube-scheduler, kubelet, kube-proxy
Docker is replaced by containerd.
On the other hand, the plugins of the cloud providers and of storage have been removed.
Sqlite3 replaces etcd.

K3s includes:
* Flannel: a very simple L2 overlay network that satisfies the Kubernetes requirements. This is a CNI plugin (Container Network Interface), such as Calico, Romana, Weave-net Flannel doesn’t support Kubernetes Network Policy, but it can be replaced by Calico (see next sections).
* CoreDNS: a flexible, extensible DNS server that can serve as the Kubernetes cluster DNS
* Traefik is a modern HTTP reverse proxy and load balancer. 
* Klipper Load Balancer : Service load balancer that uses available host ports.
* SQLite3: The storage backend used by default (also support MySQL, Postgres, and etcd3)
* Containerd is a runtime container like Docker without the image build part

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
export K3S_IP_MASTER="$(multipass info master-k8s | grep "IPv4" | awk -F' ' '{print $2}'):6443"
export K3S_TOKEN=$(multipass exec master-k8s -- /bin/bash -c "sudo cat /var/lib/rancher/k3s/server/node-token")

multipass exec worker-1-k8s -- /bin/bash -c "curl -sfL https://get.k3s.io | K3S_TOKEN=${K3S_TOKEN} K3S_URL=https://${K3S_IP_MASTER} sh -"
multipass exec worker-2-k8s -- /bin/bash -c "curl -sfL https://get.k3s.io | K3S_TOKEN=${K3S_TOKEN} K3S_URL=https://${K3S_IP_MASTER} sh -"
```

Verify if workers are bound to master

```
multipass exec master-k8s -- sudo kubectl get nodes
```

# Additional Steps

1. Deploy your first pod and check to make sure you can actually deploy something in this cluster
2. Expose the service

```
# wait for 2 x minutes or so and check if it was successful
kubectl top nodes 
kubectl top po --all-namespaces

# run nginx pod in your cluster
kubectl run nginx --image=nginx --restart=Never

# check to see if your pod is running
kubectl get po
```

Create a file and deploy it.
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
  type: LoadBalancer
```

# How to access to Nginx

At this moment, no way to access to application

```
multipass exec master-k8s bash
sudo kubectl get svc
curl localhost
```

Questions :
* Solve the problem between pod and service
* by what black magic can you use an on-prem load balancer?
  If so, what is the product used?

# Get the config file

```
mkdir $HOME/.k3s
multipass mount $HOME master-k8s
multipass exec master-k8s -- /bin/bash -c "sudo cp /etc/rancher/k3s/k3s.yaml /home/$USER/.k3s"
multipass umount master-k8s
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x kubectl
```

Change the IP from 127.0.0.1 to the right value (K3S_IP_MASTER)

```
export KUBECONFIG=${HOME}/.k3s/k3s.yaml
```

You could access to the cluster
