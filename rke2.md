# Presentation

How is this different from RKE or K3s?¶
RKE2 combines the best-of-both-worlds from the 1.x version of RKE (hereafter referred to as RKE1) and K3s.

From K3s, it inherits the usability, ease-of-operations, and deployment model.

From RKE1, it inherits close alignment with upstream Kubernetes. In places K3s has diverged from upstream Kubernetes in order to optimize for edge deployments, but RKE1 and RKE2 can stay closely aligned with upstream.

Importantly, RKE2 does not rely on Docker as RKE1 does. RKE1 leveraged Docker for deploying and managing the control plane components as well as the container runtime for Kubernetes. RKE2 launches control plane components as static pods, managed by the kubelet. The embedded container runtime is containerd.

# Installation

multipass launch --name master --cpus 2 --mem 2048M --disk 5G
multipass launch --name worker1 --cpus 2 --mem 2048M --disk 5G
multipass launch --name worker2 --cpus 2 --mem 2048M --disk 5G

## Master

multipass exec master -- /bin/bash -c "curl -sfL https://get.rke2.io | sudo sh -"
multipass exec master -- sudo /bin/bash -c "systemctl enable rke2-server.service"
multipass exec master -- sudo /bin/bash -c "systemctl start rke2-server.service"
multipass exec master -- /bin/bash -c "journalctl -u rke2-server -f"

# Worker


