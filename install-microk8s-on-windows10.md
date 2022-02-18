# Install microk8s with Ingress Controller and MetalLB on WIndows 10

Article based on https://microk8s.io/docs/install-alternatives#heading--windows

```
Note: We recommend Windows 10 Professional or Windows 10 Enterprise. MicroK8s will also require at least 4GB of available RAM and 40GB of storage.
```

# Download the MicroK8s Installer for Windows
https://microk8s.io/microk8s-installer.exe

# **Run the installer**

The installer checks if Hyper-V is available and switched on. If you don’t have Hyper-v (e.g. on Windows 10 Home edition) it is possible to use VirtualBox as an alternative.

# Installer multipass

Use Hyper-V on Windows 10 when install multipass

# **Configure MicroK8s**

You can now configure MicroK8s - the minimum recommendations are already provided.
For information on changing the ‘Snap Track’, see [this page](https://microk8s.io/docs/setting-snap-channel).
You can change this configuration at a later date by re-running the installer. Note that the Memory and Disk limits are initially set at the **minimum** values. If you are planning on running large workloads (e.g. `kubeflow` will require around 12GB RAM) you will want to set these higher.

Set 1.22 version kubernetes

# **Open a command line CMD/Powershell**

## Get external ip of microk8s

```
multipass list
```

If output have a few IP addresses, then check part of the IP in your windows

![](https://habrastorage.org/webt/er/id/b-/eridb-fs5dnqtziawz1afjkgosq.png)

```
ipconfig |findstr /i "xx.xx.xx"
```



## **Check the status while Kubernetes starts**

```
microk8s status --wait-ready
```

## **Turn on the DNS service** 

```
microk8s enable dns
```

## **Turn on the ingress storage metallb services** 

```
microk8s enable ingress storage metallb:external_ip_of_microk8s-external_ip_of_microk8s
```

Text external_ip_of_microk8s need change to external ip of microk8s

# Install helm and kubectl inside microk8s

## Enter to microk8s

```
multipass shell microk8s-vm
```

## Install helm

```
sudo snap install helm --classic
```

## Install kubectl

```
sudo snap install kubectl --classic
```

## Change permission for directory Kubernetes

```
sudo chown ubuntu:ubuntu /home/ubuntu/.kube
```

## Export microk8s kubectl config to general kubectl config

```
microk8s kubectl config view --raw > $HOME/.kube/config
export  KUBECONFIG=$HOME/.kube/config
export  KUBECONFIG=$KUBECONFIG:$HOME/.kube/config
sudo usermod -aG microk8s ubuntu
sudo chown -f -R ubuntu ~/.kube
```

