# Kubernetes-Tech-Talk

## What is MiniKube?

## Installation
### **Windows**  
`
choco install minikube
`  

*OR*  

Download the [minikube-windows-amd64.exe](https://storage.googleapis.com/minikube/releases/latest/minikube-windows-amd64.exe) file, rename it to `minikube.exe` and add it to the path

*Note* - Remember to restart the command prompt after installation.

### **Linux**
``` shell
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube && sudo mv minikube /usr/local/bin/
```

### **macOS**
```shell
brew cask install virtualbox
brew install kubectl
brew cask install minikube
```


To find out if **minikube** is installed properly and its version, type `minikube version` and `kubectl version`. 


## Quick Start
To start minikube simply invoke  
```shell 
minikube start
```  
If you want to change the VM driver add appropriate `--vm-driver=xxx` flag which supports the following list of drivers:
* [virtualbox](https://www.virtualbox.org/wiki/Downloads) (Windows)
* [hyperv](https://github.com/kubernetes/minikube/blob/master/docs/drivers.md#hyperV-driver) (Windows)
* [vmwarefusion](https://www.vmware.com/products/fusion) (macOS)
* [hyperkit](https://github.com/kubernetes/minikube/blob/master/docs/drivers.md#hyperkit-driver) (macOS)
* [xhyve](https://github.com/kubernetes/minikube/blob/master/docs/drivers.md#xhyve-driver) (macOS)
* [KVM2](https://github.com/kubernetes/minikube/blob/master/docs/drivers.md#kvm2-driver) (Linux)
* none (**Linux-only**) - this driver can be used to run the Kubernetes cluster components on the host instead of in a VM. This can be useful for CI workloads which do not support nested virtualization.  


At this point a single node Kubernetes environment is running. To verify you can run `minikube status` and you should something similar to following output:
```shell
minikube: Running
cluster: Running
kubectl: Correctly Configured: pointing to minikube-vm at 192.168.99.100
```
From here onwards, we will be interacting with Kubernetes environment using `kubectl` command.  
To get info about the nodes running in the environment you can type `kubectl get nodes` and you can see all the nodes running:  
```shell
NAME       STATUS    ROLES     AGE       VERSION
minikube   Ready     <none>    2h        v1.9.0
```
