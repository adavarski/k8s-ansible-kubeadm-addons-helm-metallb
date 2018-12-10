### Vagrant ENV:

Run `vagrant up`. This will automatically provision two VM with Kubernetes cluster installed.

```
k8s Environment Info:
Kubernetes version: 1.13 (or latest from repo)
CNI: Weave-Net
Default number of nodes: 2

root@k8s-m1:~# export KUBECONFIG=/etc/kubernetes/admin.conf
root@k8s-m1:~# kubectl get all --all-namespaces


root@k8s-m1:~# kubectl get pods -o wide --sort-by="{.spec.nodeName}" --all-namespaces


```
You can edit Vagrantfile and hack/setup-vms.sh for your needs

Example:
```
Vagrantfile: 
  master = 1
  node = 2

$ diff setup-vms.sh setup-vms.sh.NEW 
11d10
< 
13,14c12,13
< 192.16.35.11 k8s-m1
< 
---
> 192.16.35.11 k8s-n2
> 192.16.35.12 k8s-m1
38c37
<   HOSTS="192.16.35.10 192.16.35.11"
---
>   HOSTS="192.16.35.10 192.16.35.11 192.16.35.12"
```

## Manual Installation

### Kubeadm Ansible Playbook

Build a Kubernetes cluster using Ansible with kubeadm. The goal is easily install a Kubernetes cluster on machines running:

  - Ubuntu 16.04
  - CentOS 7
  - Debian 9

System requirements:

  - Deployment environment must have Ansible `2.4.0+`
  - Master and nodes must have passwordless SSH access

### Usage

Add the system information gathered above into a file called `hosts.ini`. For example:
```
[master]
192.16.35.12

[node]
192.16.35.[10:11]

[kube-cluster:children]
master
node
```

Before continuing, edit `group_vars/all.yml` to your specified configuration.

**Note:**  By default, `k8s-ansible-kubeadm` uses `eth1`. Your default interface may be `eth0`.

After going through the setup, run the `site.yaml` playbook:

```sh
$ ansible-playbook site.yaml
...
==> master1: TASK [addon : Create Kubernetes dashboard deployment] **************************
==> master1: changed: [192.16.35.12 -> 192.16.35.12]
==> master1:
==> master1: PLAY RECAP *********************************************************************
==> master1: 192.16.35.10               : ok=18   changed=14   unreachable=0    failed=0
==> master1: 192.16.35.11               : ok=18   changed=14   unreachable=0    failed=0
==> master1: 192.16.35.12               : ok=34   changed=29   unreachable=0    failed=0
```

Download the `admin.conf` from the master node:

```sh
$ scp k8s@k8s-master:/etc/kubernetes/admin.conf .
```

Verify cluster is fully running using kubectl:

```sh

$ export KUBECONFIG=~/admin.conf
$ kubectl get node
NAME      STATUS    AGE       VERSION
master1   Ready     22m       v1.6.3
node1     Ready     20m       v1.6.3
node2     Ready     20m       v1.6.3

$ kubectl get po -n kube-system
NAME                                    READY     STATUS    RESTARTS   AGE
etcd-master1                            1/1       Running   0          23m
...
```

=========================================================================

### K8s addons install via ansible role with MetalLB


This role installs the addons to Kubernetes cluster. Addons installed:

* Helm/Tillar
* Nginx Ingress (Internal Services)
* Nginx Ingress (External Services)
* Prometheus
* Grafana (includes a dashboard, using Prometheus as DataSource)
* MetalLB for LoadBalancer (Grafana, Prometheus nginx-external)

#### Requirements


The remote system should have Kubernetes masters/workers setup.

#### Role Variables


**ansible_templates_dir**: Directory where templates for the role exist.

**helm_service_account**: Service-account name for Helm/Tiller.
**kube_rbac_dir**: Directory where Kubernetes service-account manifests should be copied after templating.
**helm_certs_dir**: Directory where Helm certificates should be generated.
**helm_values_dir**: Directory containing templates for Helm-charts' config files.

**admin_namespace_full**: Full-name for the main namespace.
**admin_namespace_short**: Short-name for the main namespace.

**helm_namespaces**: An array with namespaces where helm should be installed. Should have short-name and full-name for namespace. For example:

```YAML
  - full_ns: development
    short_ns: dev
  - full_ns: staging
    short_ns: stag
  - full_ns: production
    short_ns: prod
```

#### Dependencies


* roles/docker
* roles/kubrnetes/master
* roles/kubernetes/node
* roles/cni


#### Example Playbook


```yaml
- hosts: master
  gather_facts: yes
  become: yes
  roles:
    - { role: addons, tags: addons }

```
```
root@k8s-m1:~# kubectl get ns

root@k8s-m1:~# kubectl get all --all-namespaces

root@k8s-m1:~# export PROD=/etc/kubernetes/pki/helm/prod/
root@k8s-m1:~# helm list --tls --tls-ca-cert ${PROD}/tiller-prod-ca.pem --tls-cert ${PROD}/helm-prod-cert.pem --tls-key ${PROD}/helm-prod-key.pem --tiller-namespace=production --namespace=kube-system
NAME            	REVISION	UPDATED                 	STATUS  	CHART              	APP VERSION	NAMESPACE  
bunking-squirrel	1       	Thu Dec  6 17:52:00 2018	DEPLOYED	grafana-1.20.0     	5.4.0      	kube-system
crazy-emu       	1       	Thu Dec  6 17:51:39 2018	DEPLOYED	nginx-ingress-1.0.1	0.21.0     	kube-system
prometheus      	1       	Thu Dec  6 17:51:50 2018	DEPLOYED	prometheus-8.1.0   	2.5.0      	kube-system
yellow-arachnid 	1       	Thu Dec  6 17:51:44 2018	DEPLOYED	nginx-ingress-1.0.1	0.21.0     	kube-system

```

### MetalLB for Vagrant ENV

https://metallb.universe.tf/tutorial/layer2/

Kubernetes does not offer an implementation of network load-balancers (Services of type LoadBalancer) for bare metal clusters. The implementations of Network LB that Kubernetes does ship with are all glue code that calls out to various IaaS platforms (GCP, AWS, Azure…). If you’re not running on a supported IaaS platform (GCP, AWS, Azure…), LoadBalancers will remain in the “pending” state indefinitely when created.

Bare metal cluster operators are left with two lesser tools to bring user traffic into their clusters, “NodePort” and “externalIPs” services. Both of these options have significant downsides for production use, which makes bare metal clusters second class citizens in the Kubernetes ecosystem.

MetalLB aims to redress this imbalance by offering a Network LB implementation that integrates with standard network equipment, so that external services on bare metal clusters also “just work” as much as possible.

To implement MetalLB in this Vagrant env:
```
change from nginx-internal to nginx-external

$ grep external prometheus.yml.j2 
      kubernetes.io/ingress.class: nginx-external
      kubernetes.io/ingress.class: nginx-external
      kubernetes.io/ingress.class: nginx-external
$ grep external grafana.yml.j2 
    kubernetes.io/ingress.class: nginx-external
    
We have a sample MetalLB configuration in example-layer2-config.yaml.j2. Let’s take a look at it:

apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: my-ip-space
      protocol: layer2
      addresses:
      - 192.16.35.240/28


where 

$ vagrant ssh k8s-m1 -c "ip a s"|grep 192
    inet 192.16.35.11/24 brd 192.16.35.255 scope global eth1

VagrantHost $ ip a s |grep "192.16.35"
    inet 192.16.35.1/24 brd 192.16.35.255 scope global vboxnet
    
$ sudo netstat -rn|grep "192.16.35"
192.16.35.0     0.0.0.0         255.255.255.0   U         0 0          0 vboxnet6
192.168.35.0    192.16.35.1     255.255.255.0   UG        0 0          0 vboxnet6

$ ipcalc 192.16.35.240/28
Address:   192.16.35.240        11000000.00010000.00100011.1111 0000
Netmask:   255.255.255.240 = 28 11111111.11111111.11111111.1111 0000
Wildcard:  0.0.0.15             00000000.00000000.00000000.0000 1111
=>
Network:   192.16.35.240/28     11000000.00010000.00100011.1111 0000
HostMin:   192.16.35.241        11000000.00010000.00100011.1111 0001
HostMax:   192.16.35.254        11000000.00010000.00100011.1111 1110
Broadcast: 192.16.35.255        11000000.00010000.00100011.1111 1111
Hosts/Net: 14      

$ sudo route add -net 192.168.35.0/24 gw 192.16.35.1 if we using 192.168.35.240/28 in example-layer2-config.yaml

$ kubectl get svc --all-namespaces|grep Load
kube-system   opinionated-eel-nginx-ingress-controller           LoadBalancer   10.101.216.228   192.16.35.240   80:32220/TCP,443:30614/TCP   24m

$ echo "192.16.35.240 grafana.local"|sudo tee -a "/etc/hosts"

$ curl 192.16.35.240
default backend - 404

 $ curl grafana.local
<a href="/login">Found</a>.

Browser: http://grafana.local --- admin:admin and we have monitoring cluster dashboard

```

### Resetting the environment

Finally, reset all kubeadm installed state using `reset-site.yaml` playbook:

```sh
$ ansible-playbook reset-site.yaml
```
