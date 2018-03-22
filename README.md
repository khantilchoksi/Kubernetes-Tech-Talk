# Kubernetes-Tech-Talk
In this demo, we will see how we can use Kubernetes to deploy Jenkins server quickly.  

## What is MiniKube?
Minikube is a tool that makes it easy to run Kubernetes locally. Minikube runs a single-node Kubernetes cluster inside a VM on your laptop for users looking to try out Kubernetes or develop with it day-to-day.

## Installation
### **Windows**  
`
$ choco install minikube
`  
*OR*  

Download the [minikube-windows-amd64.exe](https://storage.googleapis.com/minikube/releases/latest/minikube-windows-amd64.exe) file, rename it to `minikube.exe` and add it to the path

*Note* - Remember to restart the command prompt after installation.

#### **Linux**
``` shell
$ curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
$ chmod +x minikube && sudo mv minikube /usr/local/bin/
```

#### **macOS**
```shell
$ brew cask install virtualbox
$ brew install kubectl
$ brew cask install minikube
```

To find out if **minikube** is installed properly and its version, type `minikube version` and `kubectl version`. 

--------------------------------------------------------------------------------------------------  

## Quick Start
To start minikube simply invoke  
```shell 
$ minikube start
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

--------------------------------------------------------------------  

### Deploying Jenkins server
We can define a simple deployment of the Jenkins as given below:
```shell
$ git clone <repo>
$ cd <repo>
```
We define the deployment of the server in a simple .yaml file - [Jenkins-deployment.yml](https://github.ncsu.edu/khchoksi/Kubernetes-Tech-Talk/blob/master/jenkins-deployment.yml)  
To create the deployment run the following command:
```shell
$ kubectl create -f jenkins-deployment.yaml
```

To validate that the deployment was successful, run: 
```shell
$ kubectl get deployments
```
We can see that a single pod has been created that has the Jenkins image by invoking:
```shell
$ kubectl get pods
```

----------------------------------------------------------  

## Accessing Jenkins
Now that we have a Jenkins instance deployed in a pod, we need to make it accessible from the outside the Kubernetes cluster. The Jenkins Pod has been assigned an IP which is internal to the Kubernetes cluster. We need to expose this Pod to using a service called *NodePort*. A *NodePort* service type exposes a service on a port on each node in the cluster.  
To create the service, run:
```shell
$ kubectl create -f jenkins-service.yml
```
To validate that the service was created successfully, run:
```shell
$ kubectl get services
```
It should give you an output like:
```shell
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
jenkins      NodePort    10.103.189.22   <none>        8080:30724/TCP   1h
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          3h
```
From this output we can see port 8080 has been exposed on the port 30724 for *jenkins* service.  
To get the IP of the VM, just run `minikube ip` .It will give you the IP of the running cluster. 
```shell
$ minikube ip
192.168.99.100
```

Now, we can access the jenkins server at http://192.168.99.100:30724/

----------------------------------------------------------------------  

## SSH into the Pod
First time login into Jenkins UI requires an administrator password, which is available at */var/jenkins_home/secrets/initialAdminPassword*  
To ssh into the Pod, find the name of the Pod:  
```shell
$ kubectl get pods
NAME                       READY     STATUS    RESTARTS   AGE
jenkins-6b9f5d55d9-5rbw9   1/1       Running   0          2h
```
You can ssh into the Pod, by using the following command: 
```shell
$ kubectl exec -it jenkins-6b9f5d55d9-5rbw9 -- /bin/bash
```
This will log you into the Pod and you can find the password from within:
```shell
jenkins@jenkins-6b9f5d55d9-5rbw9:/$ cat /var/jenkins_home/secrets/initialAdminPassword 
```
Using this, we can setup the Jenkins profile.

-----------------------------------------------  
## Quick Links  

* [Tool Report](too%20report.pdf)

* [Screencast & Demo]()  
-------------------------------------------  
## References
 * https://kubernetes.io/docs/tutorials/kubernetes-basics/
 * http://www.monkeylittle.com/blog/2017/02/07/deploying-jenkins-with-kubernetes.html   
