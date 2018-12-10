### Vagrant ENV:

Run `vagrant up`. This will automatically provision two VM with Kubernetes cluster installed.

```
k8s Environment Info:
Kubernetes version: 1.13 (or latest from repo)
CNI: Weave-Net
Default number of nodes: 2

root@k8s-m1:~# export KUBECONFIG=/etc/kubernetes/admin.conf
root@k8s-m1:~# kubectl get all --all-namespaces
NAMESPACE     NAME                                        READY   STATUS    RESTARTS   AGE
kube-system   pod/coredns-86c58d9df4-4tg5l                1/1     Running   0          36m
kube-system   pod/coredns-86c58d9df4-jjdjr                1/1     Running   0          36m
kube-system   pod/etcd-k8s-m1                             1/1     Running   0          52m
kube-system   pod/kube-apiserver-k8s-m1                   1/1     Running   0          52m
kube-system   pod/kube-controller-manager-k8s-m1          1/1     Running   3          52m
kube-system   pod/kube-proxy-cbw8d                        1/1     Running   0          52m
kube-system   pod/kube-proxy-cncbd                        1/1     Running   0          44m
kube-system   pod/kube-scheduler-k8s-m1                   1/1     Running   2          52m
kube-system   pod/kubernetes-dashboard-79ff88449c-pqgx8   1/1     Running   0          52m
kube-system   pod/weave-net-ktbvl                         2/2     Running   0          51m
kube-system   pod/weave-net-qzfcx                         2/2     Running   3          44m

NAMESPACE     NAME                           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)         AGE
default       service/kubernetes             ClusterIP   10.96.0.1        <none>        443/TCP         52m
kube-system   service/kube-dns               ClusterIP   10.96.0.10       <none>        53/UDP,53/TCP   52m
kube-system   service/kubernetes-dashboard   ClusterIP   10.111.210.147   <none>        443/TCP         52m

NAMESPACE     NAME                        DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
kube-system   daemonset.apps/kube-proxy   2         2         2       2            2           <none>          52m
kube-system   daemonset.apps/weave-net    2         2         2       2            2           <none>          51m

NAMESPACE     NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/coredns                2/2     2            2           52m
kube-system   deployment.apps/kubernetes-dashboard   1/1     1            1           52m

NAMESPACE     NAME                                              DESIRED   CURRENT   READY   AGE
kube-system   replicaset.apps/coredns-86c58d9df4                2         2         2       52m
kube-system   replicaset.apps/kubernetes-dashboard-79ff88449c   1         1         1       52m


root@k8s-m1:~# kubectl get pods -o wide --sort-by="{.spec.nodeName}" --all-namespaces
NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE   IP             NODE     NOMINATED NODE   READINESS GATES
kube-system   coredns-86c58d9df4-jjdjr                1/1     Running   0          38m   10.32.0.3      k8s-m1   <none>           <none>
kube-system   coredns-86c58d9df4-4tg5l                1/1     Running   0          38m   10.36.0.0      k8s-n1   <none>           <none>
kube-system   etcd-k8s-m1                             1/1     Running   0          53m   192.16.35.11   k8s-m1   <none>           <none>
kube-system   kube-apiserver-k8s-m1                   1/1     Running   0          53m   192.16.35.11   k8s-m1   <none>           <none>
kube-system   kube-controller-manager-k8s-m1          1/1     Running   3          53m   192.16.35.11   k8s-m1   <none>           <none>
kube-system   kube-proxy-cbw8d                        1/1     Running   0          53m   192.16.35.11   k8s-m1   <none>           <none>
kube-system   kube-scheduler-k8s-m1                   1/1     Running   2          53m   192.16.35.11   k8s-m1   <none>           <none>
kube-system   kube-proxy-cncbd                        1/1     Running   0          45m   192.16.35.10   k8s-n1   <none>           <none>
kube-system   kubernetes-dashboard-79ff88449c-pqgx8   1/1     Running   0          53m   10.32.0.2      k8s-m1   <none>           <none>
kube-system   weave-net-ktbvl                         2/2     Running   0          53m   192.16.35.11   k8s-m1   <none>           <none>
kube-system   weave-net-qzfcx                         2/2     Running   3          45m   192.16.35.10   k8s-n1   <none>           <none>
root@k8s-m1:~# 


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

### K8s addons install via ansible role


This role installs the addons to Kubernetes cluster. Addons installed:

* Helm/Tillar
* Nginx Ingress (Internal Services)
* Nginx Ingress (External Services)
* Prometheus
* Grafana (includes a dashboard, using Prometheus as DataSource)
* MetalLB for LoadBalancer provisioning

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
NAME          STATUS   AGE
default       Active   10m
development   Active   7m31s
kube-public   Active   10m
kube-system   Active   10m
production    Active   7m31s
staging       Active   7m31s
root@k8s-m1:~# kubectl get all --all-namespaces
NAMESPACE     NAME                                                                 READY   STATUS    RESTARTS   AGE
development   pod/tiller-deploy-8d8676d44-bvwcm                                    1/1     Running   0          6m33s
kube-system   pod/bunking-squirrel-grafana-7bbbd7cdd9-85dbw                        1/1     Running   0          4m29s
kube-system   pod/coredns-86c58d9df4-srv77                                         1/1     Running   0          8m12s
kube-system   pod/coredns-86c58d9df4-xffhr                                         1/1     Running   0          8m12s
kube-system   pod/crazy-emu-nginx-ingress-controller-7f8dc9b448-6k2tr              1/1     Running   0          4m50s
kube-system   pod/crazy-emu-nginx-ingress-default-backend-699bbcd79f-bfg9m         1/1     Running   0          4m50s
kube-system   pod/etcd-k8s-m1                                                      1/1     Running   0          9m37s
kube-system   pod/kube-apiserver-k8s-m1                                            1/1     Running   0          9m53s
kube-system   pod/kube-controller-manager-k8s-m1                                   1/1     Running   0          9m43s
kube-system   pod/kube-proxy-29d2b                                                 1/1     Running   0          8m25s
kube-system   pod/kube-proxy-56zbw                                                 1/1     Running   0          10m
kube-system   pod/kube-scheduler-k8s-m1                                            1/1     Running   0          9m48s
kube-system   pod/kubernetes-dashboard-79ff88449c-4b5s7                            1/1     Running   0          10m
kube-system   pod/prometheus-alertmanager-5bbd595756-k6bzd                         2/2     Running   0          4m39s
kube-system   pod/prometheus-kube-state-metrics-b4d7c746-b9rtj                     1/1     Running   0          4m39s
kube-system   pod/prometheus-node-exporter-k27dq                                   1/1     Running   0          4m40s
kube-system   pod/prometheus-pushgateway-695c7c958b-6twbt                          1/1     Running   0          4m39s
kube-system   pod/prometheus-server-7f59447d54-2jrsp                               1/2     Running   0          4m39s
kube-system   pod/weave-net-j8q4t                                                  2/2     Running   0          10m
kube-system   pod/weave-net-z7dvl                                                  2/2     Running   0          8m25s
kube-system   pod/yellow-arachnid-nginx-ingress-controller-77cd6f49d5-tcl2r        1/1     Running   0          4m46s
kube-system   pod/yellow-arachnid-nginx-ingress-default-backend-7c9fbf88d9-4wkws   1/1     Running   0          4m46s
production    pod/tiller-deploy-77d75bcbf6-nhs4x                                   1/1     Running   0          6m31s
staging       pod/tiller-deploy-57b8499c56-s4jr9                                   1/1     Running   0          6m32s

NAMESPACE     NAME                                                       TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
default       service/kubernetes                                         ClusterIP      10.96.0.1        <none>        443/TCP                      10m
development   service/tiller-deploy                                      ClusterIP      10.106.197.114   <none>        44134/TCP                    6m34s
kube-system   service/bunking-squirrel-grafana                           ClusterIP      10.109.52.53     <none>        80/TCP                       4m29s
kube-system   service/crazy-emu-nginx-ingress-controller                 LoadBalancer   10.96.145.192    <pending>     80:30196/TCP,443:32531/TCP   4m51s
kube-system   service/crazy-emu-nginx-ingress-controller-metrics         ClusterIP      10.100.105.224   <none>        9913/TCP                     4m51s
kube-system   service/crazy-emu-nginx-ingress-controller-stats           ClusterIP      10.110.197.56    <none>        18080/TCP                    4m51s
kube-system   service/crazy-emu-nginx-ingress-default-backend            ClusterIP      10.97.94.156     <none>        80/TCP                       4m50s
kube-system   service/kube-dns                                           ClusterIP      10.96.0.10       <none>        53/UDP,53/TCP                10m
kube-system   service/kubernetes-dashboard                               ClusterIP      10.108.81.188    <none>        443/TCP                      10m
kube-system   service/prometheus-alertmanager                            ClusterIP      10.102.212.64    <none>        80/TCP                       4m41s
kube-system   service/prometheus-kube-state-metrics                      ClusterIP      None             <none>        80/TCP                       4m41s
kube-system   service/prometheus-node-exporter                           ClusterIP      None             <none>        9100/TCP                     4m41s
kube-system   service/prometheus-pushgateway                             ClusterIP      10.105.163.68    <none>        9091/TCP                     4m40s
kube-system   service/prometheus-server                                  ClusterIP      10.109.198.42    <none>        80/TCP                       4m40s
kube-system   service/yellow-arachnid-nginx-ingress-controller           NodePort       10.102.104.245   <none>        80:30080/TCP,443:30443/TCP   4m46s
kube-system   service/yellow-arachnid-nginx-ingress-controller-metrics   ClusterIP      10.103.251.124   <none>        9913/TCP                     4m46s
kube-system   service/yellow-arachnid-nginx-ingress-controller-stats     ClusterIP      10.107.195.220   <none>        18080/TCP                    4m46s
kube-system   service/yellow-arachnid-nginx-ingress-default-backend      ClusterIP      10.109.169.84    <none>        80/TCP                       4m46s
production    service/tiller-deploy                                      ClusterIP      10.108.62.121    <none>        44134/TCP                    6m31s
staging       service/tiller-deploy                                      ClusterIP      10.107.144.60    <none>        44134/TCP                    6m32s

NAMESPACE     NAME                                      DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
kube-system   daemonset.apps/kube-proxy                 2         2         2       2            2           <none>          10m
kube-system   daemonset.apps/prometheus-node-exporter   1         1         1       1            1           <none>          4m40s
kube-system   daemonset.apps/weave-net                  2         2         2       2            2           <none>          10m

NAMESPACE     NAME                                                            READY   UP-TO-DATE   AVAILABLE   AGE
development   deployment.apps/tiller-deploy                                   1/1     1            1           6m34s
kube-system   deployment.apps/bunking-squirrel-grafana                        1/1     1            1           4m29s
kube-system   deployment.apps/coredns                                         2/2     2            2           10m
kube-system   deployment.apps/crazy-emu-nginx-ingress-controller              1/1     1            1           4m50s
kube-system   deployment.apps/crazy-emu-nginx-ingress-default-backend         1/1     1            1           4m50s
kube-system   deployment.apps/kubernetes-dashboard                            1/1     1            1           10m
kube-system   deployment.apps/prometheus-alertmanager                         1/1     1            1           4m40s
kube-system   deployment.apps/prometheus-kube-state-metrics                   1/1     1            1           4m40s
kube-system   deployment.apps/prometheus-pushgateway                          1/1     1            1           4m40s
kube-system   deployment.apps/prometheus-server                               0/1     1            0           4m40s
kube-system   deployment.apps/yellow-arachnid-nginx-ingress-controller        1/1     1            1           4m46s
kube-system   deployment.apps/yellow-arachnid-nginx-ingress-default-backend   1/1     1            1           4m46s
production    deployment.apps/tiller-deploy                                   1/1     1            1           6m31s
staging       deployment.apps/tiller-deploy                                   1/1     1            1           6m32s

NAMESPACE     NAME                                                                       DESIRED   CURRENT   READY   AGE
development   replicaset.apps/tiller-deploy-8d8676d44                                    1         1         1       6m34s
kube-system   replicaset.apps/bunking-squirrel-grafana-7bbbd7cdd9                        1         1         1       4m29s
kube-system   replicaset.apps/coredns-86c58d9df4                                         2         2         2       10m
kube-system   replicaset.apps/crazy-emu-nginx-ingress-controller-7f8dc9b448              1         1         1       4m50s
kube-system   replicaset.apps/crazy-emu-nginx-ingress-default-backend-699bbcd79f         1         1         1       4m50s
kube-system   replicaset.apps/kubernetes-dashboard-79ff88449c                            1         1         1       10m
kube-system   replicaset.apps/prometheus-alertmanager-5bbd595756                         1         1         1       4m40s
kube-system   replicaset.apps/prometheus-kube-state-metrics-b4d7c746                     1         1         1       4m40s
kube-system   replicaset.apps/prometheus-pushgateway-695c7c958b                          1         1         1       4m40s
kube-system   replicaset.apps/prometheus-server-7f59447d54                               1         1         0       4m40s
kube-system   replicaset.apps/yellow-arachnid-nginx-ingress-controller-77cd6f49d5        1         1         1       4m46s
kube-system   replicaset.apps/yellow-arachnid-nginx-ingress-default-backend-7c9fbf88d9   1         1         1       4m46s
production    replicaset.apps/tiller-deploy-77d75bcbf6                                   1         1         1       6m31s
staging       replicaset.apps/tiller-deploy-57b8499c56                                   1         1         1       6m32s


root@k8s-m1:~# export PROD=/etc/kubernetes/pki/helm/prod/
root@k8s-m1:~# helm list --tls --tls-ca-cert ${PROD}/tiller-prod-ca.pem --tls-cert ${PROD}/helm-prod-cert.pem --tls-key ${PROD}/helm-prod-key.pem --tiller-namespace=production --namespace=kube-system
NAME            	REVISION	UPDATED                 	STATUS  	CHART              	APP VERSION	NAMESPACE  
bunking-squirrel	1       	Thu Dec  6 17:52:00 2018	DEPLOYED	grafana-1.20.0     	5.4.0      	kube-system
crazy-emu       	1       	Thu Dec  6 17:51:39 2018	DEPLOYED	nginx-ingress-1.0.1	0.21.0     	kube-system
prometheus      	1       	Thu Dec  6 17:51:50 2018	DEPLOYED	prometheus-8.1.0   	2.5.0      	kube-system
yellow-arachnid 	1       	Thu Dec  6 17:51:44 2018	DEPLOYED	nginx-ingress-1.0.1	0.21.0     	kube-system

kubectl get pods --namespace kube-system -l "app=grafana" -o jsonpath="{.items[0].metadata.name}"
bunking-squirrel-grafana-7bbbd7cdd9-85dbw

kubectl --namespace kube-system port-forward bunking-squirrel-grafana-7bbbd7cdd9-85dbw 3000

BROWSER: http://localhost:3000 --- admin:admin --- import json file if needed.

```

### K8s addons via ansible with MetalLB

https://metallb.universe.tf/tutorial/layer2/

```
$ kubectl get svc --all-namespaces|grep Load
kube-system   opinionated-eel-nginx-ingress-controller           LoadBalancer   10.101.216.228    <pending>   80:32220/TCP,443:30614/TCP   24m
```
Kubernetes does not offer an implementation of network load-balancers (Services of type LoadBalancer) for bare metal clusters. The implementations of Network LB that Kubernetes does ship with are all glue code that calls out to various IaaS platforms (GCP, AWS, Azure…). If you’re not running on a supported IaaS platform (GCP, AWS, Azure…), LoadBalancers will remain in the “pending” state indefinitely when created.

Bare metal cluster operators are left with two lesser tools to bring user traffic into their clusters, “NodePort” and “externalIPs” services. Both of these options have significant downsides for production use, which makes bare metal clusters second class citizens in the Kubernetes ecosystem.

MetalLB aims to redress this imbalance by offering a Network LB implementation that integrates with standard network equipment, so that external services on bare metal clusters also “just work” as much as possible.

To implement MetalLB in this Vagrant env:
```
change from nginx-internal to nginx-external

davar@home ~/LABS/k8s-ansible-kubeadm-addons-helm/roles/addons/templates/helm-values $ grep external prometheus.yml.j2 
      kubernetes.io/ingress.class: nginx-external
      kubernetes.io/ingress.class: nginx-external
      kubernetes.io/ingress.class: nginx-external
davar@home ~/LABS/k8s-ansible-kubeadm-addons-helm/roles/addons/templates/helm-values $ grep external grafana.yml.j2 
    kubernetes.io/ingress.class: nginx-external
    
$ vagrant destroy -f; vagrant up    

Install metallb:

$ kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.7.3/manifests/metallb.yaml

or use provided file : 

$ kubectl create -f metallb.yaml

You should see one controller pod, and one speaker pod for each node in your cluster.

Configure MetalLB

$ kubectl create -f example-layer2-config.yaml

We have a sample MetalLB configuration in example-layer2-config.yaml. Let’s take a look at it:

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
