### Vagrant ENV:

Run `vagrant up`. This will automatically provision two VM with Kubernetes cluster installed.

```
k8s Environment Info:
Kubernetes version: 1.13 (or latest from repo)
CNI: Weave-Net
Default number of nodes: 2

root@k8s-m1:~# export KUBECONFIG=/etc/kubernetes/admin.conf
root@k8s-m1:/etc/kubernetes# kubectl get all --all-namespaces
NAMESPACE        NAME                                                                  READY   STATUS    RESTARTS   AGE
development      pod/tiller-deploy-f5579c775-prp6d                                     1/1     Running   0          8m25s
kube-system      pod/coredns-86c58d9df4-h8ngc                                          1/1     Running   0          10m
kube-system      pod/coredns-86c58d9df4-m4jjm                                          1/1     Running   0          10m
kube-system      pod/etcd-k8s-m1                                                       1/1     Running   0          11m
kube-system      pod/kube-apiserver-k8s-m1                                             1/1     Running   0          11m
kube-system      pod/kube-controller-manager-k8s-m1                                    1/1     Running   0          11m
kube-system      pod/kube-proxy-7srrl                                                  1/1     Running   0          10m
kube-system      pod/kube-proxy-mfhnc                                                  1/1     Running   0          12m
kube-system      pod/kube-scheduler-k8s-m1                                             1/1     Running   0          11m
kube-system      pod/kubernetes-dashboard-79ff88449c-dr5r8                             1/1     Running   0          12m
kube-system      pod/original-anaconda-nginx-ingress-controller-6c78699dc7-8nr42       1/1     Running   0          6m33s
kube-system      pod/original-anaconda-nginx-ingress-default-backend-6c8f664b6frw76j   1/1     Running   0          6m33s
kube-system      pod/prometheus-alertmanager-5bbd595756-kp2ld                          2/2     Running   0          6m27s
kube-system      pod/prometheus-kube-state-metrics-b4d7c746-5xpzw                      1/1     Running   0          6m27s
kube-system      pod/prometheus-node-exporter-87n7c                                    1/1     Running   0          6m27s
kube-system      pod/prometheus-pushgateway-695c7c958b-qprp9                           1/1     Running   0          6m27s
kube-system      pod/prometheus-server-7f59447d54-g927c                                2/2     Running   0          6m27s
kube-system      pod/vocal-chipmunk-nginx-ingress-controller-65dc744458-k42z8          1/1     Running   0          6m38s
kube-system      pod/vocal-chipmunk-nginx-ingress-default-backend-6cbc98c75c-5m5f2     1/1     Running   0          6m38s
kube-system      pod/weave-net-bxp6v                                                   2/2     Running   0          12m
kube-system      pod/weave-net-prkf7                                                   2/2     Running   0          10m
kube-system      pod/yummy-lemur-grafana-98f845967-2rs7s                               1/1     Running   0          6m15s
metallb-system   pod/controller-7cc9c87cfb-45xtg                                       1/1     Running   0          6m7s
metallb-system   pod/speaker-5bv8j                                                     1/1     Running   0          6m7s
production       pod/tiller-deploy-5599546f9c-mgsxm                                    1/1     Running   0          8m20s
staging          pod/tiller-deploy-b67b98695-qgdmt                                     1/1     Running   0          8m21s

NAMESPACE     NAME                                                         TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)                      AGE
default       service/kubernetes                                           ClusterIP      10.96.0.1        <none>          443/TCP                      12m
development   service/tiller-deploy                                        ClusterIP      10.111.144.80    <none>          44134/TCP                    8m27s
kube-system   service/kube-dns                                             ClusterIP      10.96.0.10       <none>          53/UDP,53/TCP                12m
kube-system   service/kubernetes-dashboard                                 ClusterIP      10.111.234.134   <none>          443/TCP                      12m
kube-system   service/original-anaconda-nginx-ingress-controller           NodePort       10.108.161.87    <none>          80:30080/TCP,443:30443/TCP   6m34s
kube-system   service/original-anaconda-nginx-ingress-controller-metrics   ClusterIP      10.103.113.225   <none>          9913/TCP                     6m34s
kube-system   service/original-anaconda-nginx-ingress-controller-stats     ClusterIP      10.108.7.9       <none>          18080/TCP                    6m34s
kube-system   service/original-anaconda-nginx-ingress-default-backend      ClusterIP      10.100.108.244   <none>          80/TCP                       6m33s
kube-system   service/prometheus-alertmanager                              ClusterIP      10.109.166.133   <none>          80/TCP                       6m28s
kube-system   service/prometheus-kube-state-metrics                        ClusterIP      None             <none>          80/TCP                       6m28s
kube-system   service/prometheus-node-exporter                             ClusterIP      None             <none>          9100/TCP                     6m28s
kube-system   service/prometheus-pushgateway                               ClusterIP      10.111.135.94    <none>          9091/TCP                     6m28s
kube-system   service/prometheus-server                                    ClusterIP      10.107.160.203   <none>          80/TCP                       6m28s
kube-system   service/vocal-chipmunk-nginx-ingress-controller              LoadBalancer   10.99.249.199    192.16.35.240   80:31629/TCP,443:31103/TCP   6m39s
kube-system   service/vocal-chipmunk-nginx-ingress-controller-metrics      ClusterIP      10.100.176.229   <none>          9913/TCP                     6m39s
kube-system   service/vocal-chipmunk-nginx-ingress-controller-stats        ClusterIP      10.109.177.92    <none>          18080/TCP                    6m39s
kube-system   service/vocal-chipmunk-nginx-ingress-default-backend         ClusterIP      10.105.154.250   <none>          80/TCP                       6m39s
kube-system   service/yummy-lemur-grafana                                  ClusterIP      10.101.132.37    <none>          80/TCP                       6m16s
production    service/tiller-deploy                                        ClusterIP      10.99.131.223    <none>          44134/TCP                    8m20s
staging       service/tiller-deploy                                        ClusterIP      10.104.95.60     <none>          44134/TCP                    8m21s

NAMESPACE        NAME                                      DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
kube-system      daemonset.apps/kube-proxy                 2         2         2       2            2           <none>          12m
kube-system      daemonset.apps/prometheus-node-exporter   1         1         1       1            1           <none>          6m28s
kube-system      daemonset.apps/weave-net                  2         2         2       2            2           <none>          12m
metallb-system   daemonset.apps/speaker                    1         1         1       1            1           <none>          6m7s

NAMESPACE        NAME                                                              READY   UP-TO-DATE   AVAILABLE   AGE
development      deployment.apps/tiller-deploy                                     1/1     1            1           8m28s
kube-system      deployment.apps/coredns                                           2/2     2            2           12m
kube-system      deployment.apps/kubernetes-dashboard                              1/1     1            1           12m
kube-system      deployment.apps/original-anaconda-nginx-ingress-controller        1/1     1            1           6m33s
kube-system      deployment.apps/original-anaconda-nginx-ingress-default-backend   1/1     1            1           6m33s
kube-system      deployment.apps/prometheus-alertmanager                           1/1     1            1           6m27s
kube-system      deployment.apps/prometheus-kube-state-metrics                     1/1     1            1           6m27s
kube-system      deployment.apps/prometheus-pushgateway                            1/1     1            1           6m27s
kube-system      deployment.apps/prometheus-server                                 1/1     1            1           6m27s
kube-system      deployment.apps/vocal-chipmunk-nginx-ingress-controller           1/1     1            1           6m39s
kube-system      deployment.apps/vocal-chipmunk-nginx-ingress-default-backend      1/1     1            1           6m39s
kube-system      deployment.apps/yummy-lemur-grafana                               1/1     1            1           6m16s
metallb-system   deployment.apps/controller                                        1/1     1            1           6m7s
production       deployment.apps/tiller-deploy                                     1/1     1            1           8m20s
staging          deployment.apps/tiller-deploy                                     1/1     1            1           8m21s

NAMESPACE        NAME                                                                         DESIRED   CURRENT   READY   AGE
development      replicaset.apps/tiller-deploy-f5579c775                                      1         1         1       8m27s
kube-system      replicaset.apps/coredns-86c58d9df4                                           2         2         2       12m
kube-system      replicaset.apps/kubernetes-dashboard-79ff88449c                              1         1         1       12m
kube-system      replicaset.apps/original-anaconda-nginx-ingress-controller-6c78699dc7        1         1         1       6m33s
kube-system      replicaset.apps/original-anaconda-nginx-ingress-default-backend-6c8f664b6f   1         1         1       6m33s
kube-system      replicaset.apps/prometheus-alertmanager-5bbd595756                           1         1         1       6m27s
kube-system      replicaset.apps/prometheus-kube-state-metrics-b4d7c746                       1         1         1       6m27s
kube-system      replicaset.apps/prometheus-pushgateway-695c7c958b                            1         1         1       6m27s
kube-system      replicaset.apps/prometheus-server-7f59447d54                                 1         1         1       6m27s
kube-system      replicaset.apps/vocal-chipmunk-nginx-ingress-controller-65dc744458           1         1         1       6m39s
kube-system      replicaset.apps/vocal-chipmunk-nginx-ingress-default-backend-6cbc98c75c      1         1         1       6m38s
kube-system      replicaset.apps/yummy-lemur-grafana-98f845967                                1         1         1       6m16s
metallb-system   replicaset.apps/controller-7cc9c87cfb                                        1         1         1       6m7s
production       replicaset.apps/tiller-deploy-5599546f9c                                     1         1         1       8m20s
staging          replicaset.apps/tiller-deploy-b67b98695                                      1         1         1       8m21s



root@k8s-m1:/etc/kubernetes# kubectl get pods -o wide --sort-by="{.spec.nodeName}" --all-namespaces
NAMESPACE        NAME                                                              READY   STATUS    RESTARTS   AGE     IP             NODE     NOMINATED NODE   READINESS GATES
kube-system      coredns-86c58d9df4-h8ngc                                          1/1     Running   0          10m     10.32.0.2      k8s-m1   <none>           <none>
kube-system      kubernetes-dashboard-79ff88449c-dr5r8                             1/1     Running   0          12m     10.32.0.3      k8s-m1   <none>           <none>
kube-system      coredns-86c58d9df4-m4jjm                                          1/1     Running   0          10m     10.32.0.4      k8s-m1   <none>           <none>
kube-system      etcd-k8s-m1                                                       1/1     Running   0          12m     192.16.35.11   k8s-m1   <none>           <none>
kube-system      kube-apiserver-k8s-m1                                             1/1     Running   0          12m     192.16.35.11   k8s-m1   <none>           <none>
kube-system      kube-controller-manager-k8s-m1                                    1/1     Running   0          12m     192.16.35.11   k8s-m1   <none>           <none>
kube-system      kube-proxy-mfhnc                                                  1/1     Running   0          12m     192.16.35.11   k8s-m1   <none>           <none>
kube-system      weave-net-bxp6v                                                   2/2     Running   0          12m     192.16.35.11   k8s-m1   <none>           <none>
kube-system      kube-scheduler-k8s-m1                                             1/1     Running   0          12m     192.16.35.11   k8s-m1   <none>           <none>
development      tiller-deploy-f5579c775-prp6d                                     1/1     Running   0          9m5s    10.44.0.1      k8s-n1   <none>           <none>
kube-system      original-anaconda-nginx-ingress-controller-6c78699dc7-8nr42       1/1     Running   0          7m13s   10.44.0.6      k8s-n1   <none>           <none>
kube-system      original-anaconda-nginx-ingress-default-backend-6c8f664b6frw76j   1/1     Running   0          7m13s   10.44.0.5      k8s-n1   <none>           <none>
kube-system      prometheus-alertmanager-5bbd595756-kp2ld                          2/2     Running   0          7m7s    10.44.0.10     k8s-n1   <none>           <none>
kube-system      prometheus-kube-state-metrics-b4d7c746-5xpzw                      1/1     Running   0          7m7s    10.44.0.7      k8s-n1   <none>           <none>
kube-system      prometheus-node-exporter-87n7c                                    1/1     Running   0          7m7s    192.16.35.10   k8s-n1   <none>           <none>
kube-system      prometheus-pushgateway-695c7c958b-qprp9                           1/1     Running   0          7m7s    10.44.0.8      k8s-n1   <none>           <none>
kube-system      prometheus-server-7f59447d54-g927c                                2/2     Running   0          7m7s    10.44.0.9      k8s-n1   <none>           <none>
kube-system      vocal-chipmunk-nginx-ingress-controller-65dc744458-k42z8          1/1     Running   0          7m18s   192.16.35.10   k8s-n1   <none>           <none>
kube-system      vocal-chipmunk-nginx-ingress-default-backend-6cbc98c75c-5m5f2     1/1     Running   0          7m18s   10.44.0.4      k8s-n1   <none>           <none>
kube-system      kube-proxy-7srrl                                                  1/1     Running   0          10m     192.16.35.10   k8s-n1   <none>           <none>
kube-system      weave-net-prkf7                                                   2/2     Running   0          10m     192.16.35.10   k8s-n1   <none>           <none>
kube-system      yummy-lemur-grafana-98f845967-2rs7s                               1/1     Running   0          6m55s   10.44.0.11     k8s-n1   <none>           <none>
metallb-system   controller-7cc9c87cfb-45xtg                                       1/1     Running   0          6m47s   10.44.0.12     k8s-n1   <none>           <none>
metallb-system   speaker-5bv8j                                                     1/1     Running   0          6m47s   192.16.35.10   k8s-n1   <none>           <none>
production       tiller-deploy-5599546f9c-mgsxm                                    1/1     Running   0          9m      10.44.0.3      k8s-n1   <none>           <none>
staging          tiller-deploy-b67b98695-qgdmt                                     1/1     Running   0          9m1s    10.44.0.2      k8s-n1   <none>           <none>



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

### K8s addons install via ansible role with MetalLB in ARP mode to provide external IPs (Load Balancer) for the cluster.


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
root@k8s-m1:/etc/kubernetes# kubectl get ns
NAME             STATUS   AGE
default          Active   10m
development      Active   7m59s
kube-public      Active   10m
kube-system      Active   10m
metallb-system   Active   4m35s
production       Active   7m59s
staging          Active   7m59s


root@k8s-m1:~# export PROD=/etc/kubernetes/pki/helm/prod/
root@k8s-m1:~# helm list --tls --tls-ca-cert ${PROD}/tiller-prod-ca.pem --tls-cert ${PROD}/helm-prod-cert.pem --tls-key ${PROD}/helm-prod-key.pem --tiller-namespace=production --namespace=kube-system
NAME            	REVISION	UPDATED                 	STATUS  	CHART              	APP VERSION	NAMESPACE  
bunking-squirrel	1       	Thu Dec  6 17:52:00 2018	DEPLOYED	grafana-1.20.0     	5.4.0      	kube-system
crazy-emu       	1       	Thu Dec  6 17:51:39 2018	DEPLOYED	nginx-ingress-1.0.1	0.21.0     	kube-system
prometheus      	1       	Thu Dec  6 17:51:50 2018	DEPLOYED	prometheus-8.1.0   	2.5.0      	kube-system
yellow-arachnid 	1       	Thu Dec  6 17:51:44 2018	DEPLOYED	nginx-ingress-1.0.1	0.21.0     	kube-system

```

### MetalLB (Vagrant ENV) in ARP mode to provide external IPs (Load Balancer) for the cluster.

https://metallb.universe.tf/tutorial/layer2/

Kubernetes does not offer an implementation of network load-balancers (Services of type LoadBalancer) for bare metal clusters. The implementations of Network LB that Kubernetes does ship with are all glue code that calls out to various IaaS platforms (GCP, AWS, Azure…). If you’re not running on a supported IaaS platform (GCP, AWS, Azure…), LoadBalancers will remain in the “pending” state indefinitely when created.

Bare metal cluster operators are left with two lesser tools to bring user traffic into their clusters, “NodePort” and “externalIPs” services. Both of these options have significant downsides for production use, which makes bare metal clusters second class citizens in the Kubernetes ecosystem.

MetalLB aims to redress this imbalance by offering a Network LB implementation that integrates with standard network equipment, so that external services on bare metal clusters also “just work” as much as possible.

To implement MetalLB in this Vagrant env:
```
nginx-external : prometheus.yml.j2 & grafana.yml.j2

  
We have a sample MetalLB configuration in example-layer2-config.yaml.j2:

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

Note: sudo route add -net 192.168.35.0/24 gw 192.16.35.1 if we using 192.168.35.240/28 in example-layer2-config.yaml

root@k8s-m1:/etc/kubernetes# kubectl get svc --all-namespaces|grep LoadBalancer
kube-system   vocal-chipmunk-nginx-ingress-controller              LoadBalancer   10.99.249.199    192.16.35.240   80:31629/TCP,443:31103/TCP   4m6s

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
