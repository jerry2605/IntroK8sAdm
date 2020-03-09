Module 6: Networking

## Objectives

This exercise focuses on enabling you to do the following:

  - > Identify the network components on a Kubernetes cluster

  - > Understand pod networking

  - > Investigate service networking

  - > Review the DNS configuration
    
    **NOTE: All the tasks in the following module are executed on the
    Kubernetes Master node. You must SSH the RHEL3 host in your labs or
    copy the config file to the Windows jump host, in order to be able
    to execute the kubectl commands.**

## Task 1: identify the network componentes on a kubernetes cluster

In this task, you will review the network components of your Kubernetes
cluster.

<table>
<thead>
<tr class="header">
<th>Step</th>
<th>Action</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><ol type="1">
<li></li>
</ol></td>
<td><p>List the available nodes in your cluster:</p>
<p>kubectl get nodes</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>What is the address assigned to the master node?</p>
<p>kubectl get nodes -o wide</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Review the network interfaces on the master node:</p>
<p>ifconfig -a</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Identify network interface configured for cluster connectivity on the master node:</p>
<p>ip link</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>What is the mac address assigned to the master node?</p>
<p>ip link show ens32</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>What is the address assigned to worker node RHEL1?</p>
<p>kubectl get nodes -o wide</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>What is the mac address assign to RHEL1?</p>
<p>arp rhel1</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>What is the address assigned to the worker node RHEL2?</p>
<p>kubectl get nodes -o wide</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>What is the mac address assign to the RHEL2?</p>
<p>arp rhel2</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>What is Docker interface/bridge on the master node and what is the state?</p>
<p>ip link</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>What is the state of the Docker interface?</p>
<p>ip link show docker0</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><p>What is the default gateway?</p>
<p>ip route show default</p></td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>On the master node, what port does the Kubernetes’ scheduler listen on?</p>
<p>netstat -nplt</p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td><p>Notice that ETCD is listening on two different ports across three addresses. What port of ectd is there more client connections?</p>
<p>netstat -anp | grep etcd</p></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td>Run netstat -nplt on a worker node. Compare this output to the one run on the master node.</td>
</tr>
</tbody>
</table>

## Task 2: understand POD NEtworking 

In this task, you will learn how to configure networking for the pods.

<table>
<thead>
<tr class="header">
<th>Step</th>
<th>Action</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><ol type="1">
<li></li>
</ol></td>
<td><p>Investigate the kubelet service and identify the network plugin:</p>
<p>ps -aux | grep network</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Identify list of possible CNI plugins:</p>
<p>ls /opt/cni/bin</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Compare the running process with network connection to the list of possible CNI plugins. Identify the current running CNI plugin:</p>
<p>netstat -nplt</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Review how the Weave network is configured:</p>
<p>cat /opt/cni/net.d/10-weave.conflist</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Identify how many and which nodes are Weave agents deployed in the cluster:</p>
<p>kubectl get pods -n kube-system</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>For each node in the cluster, identify the name of the Weave (bridge) network:</p>
<p>ip link</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Identify the Pod IP address range configured for your CNI plugin on the master node and worker nodes:</p>
<p>ip addr show weave</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Can you identify the default gateway configured on the Pods scheduled on RHEL1?</p>
<p>ip route</p></td>
</tr>
</tbody>
</table>

## 

## Task 3: investigate Services Networking 

In this task, you will investigate how networking functions with
services in a Kubernetes cluster.

<table>
<thead>
<tr class="header">
<th>Step</th>
<th>Action</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><ol type="1">
<li></li>
</ol></td>
<td><img src="./media/image1.png" style="width:0.55208in;height:0.55208in" />A service is an abstraction for pods, providing a stable, so called virtual IP (VIP) address. While pods may come and go and with it their IP addresses, a service allows clients to reliably connect to the containers running in the pod using the VIP. The virtual in VIP means it is not an actual IP address connected to a network interface, but its purpose is purely to forward traffic to one or more pods. Keeping the mapping between the VIP and the pods up-to-date is the job of <a href="https://kubernetes.io/docs/admin/kube-proxy/">kube-proxy</a>, a process that runs on every node, which queries the API server to learn about new services in the cluster.</td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td>Make sure that you are in the ~/k8s/course/ folder on RHEL3.</td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Let’s create a pod supervised by a Replication Controller and a Simple Service along with it:</p>
<p>kubectl create -f ReplicationController.yaml</p>
<p>kubectl create -f sise.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Now we have the supervised pod running:</p>
<p>kubectl get pods -l app=sise</p>
<p>kubectl describe pod rcsise-XXX</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>You can, from within the cluster, access the pod directly via its assigned IP (use the describe operation above to get the IP address):</p>
<p>curl 10.44.0.x:9876/info</p>
<p>Sample output:</p>
<p>{"host": "10.44.0.2:9876", "version": "0.5.0", "from": "10.32.0.1"}</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>This is however, as mentioned above, not advisable since the IPs assigned to pods may change. That’s the reason why we create the simple service:</p>
<p>kubectl get svc</p>
<p>kubectl describe svc simpleservice</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>This means that from within the cluster, you can access your service using the cluster IP shown in the command from the step above:</p>
<p>curl 10.106.214.101/info</p>
<p>Sample output:</p>
<p>{"host": "10.106.214.101", "version": "0.5.0", "from": "10.32.0.1"}</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>The cluster VIP 10.106.214.101 forwards traffic to the pod using Iptables. To view the IPtables for our simple service, use the following command:</p>
<p>iptables-save | grep simpleservice</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Let’s now add a second pod by scaling up the RC supervising it:</p>
<p>kubectl scale --replicas=2 rc/rcsise</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Check the IPtables again to display the new rules:</p>
<p>iptables-save | grep simpleservice</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>You can remove all the resources created by doing:</p>
<p>kubectl delete svc simpleservice</p>
<p>kubectl delete rc rcsise</p></td>
</tr>
</tbody>
</table>

## Task 4: Review the DNS configuration

In this task, you will learn how DNS is configured in your cluster.

<table>
<thead>
<tr class="header">
<th>Step</th>
<th>Action</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><ol type="1">
<li></li>
</ol></td>
<td><p>Identify which DNS system has been deployed in your cluster:</p>
<p>kubectl get pods -n kube-system</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td>How many pods are being used for DNS?</td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>What is the service being used for DNS?</p>
<p>kubectl get service -n kube-system</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td>What is the IP address for DNS service?</td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td>What would be the Fully Qualified Domain Name for the DNS service? (see lecture)</td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>How is the file passed into the CoreDNS pod?</p>
<p>kubectl get configmap -n kube-system</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>What is the root domain?</p>
<p>kubectl describe configmap coredns -n kube-system</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td>Launch the alpine.yaml Pod.</td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Verify how the Pod’s resolv.conf is configured:</p>
<p>kubectl exec alpine cat /etc/resolv.conf</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td>Delete the alpine Pod.</td>
</tr>
</tbody>
</table>

End of Exercise
