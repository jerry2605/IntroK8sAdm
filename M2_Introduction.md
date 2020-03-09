Module 2: Introduction to Kubernetes

## Objectives

This exercise focuses on enabling you to do the following:

  - > Explore the Kubernetes cluster and configuration

  - > Communicate with the Kubernetes cluster via CLI and API

  - > Upgrade your Kubernetes cluster
    
    **NOTE: All the tasks in the following module are executed on the
    Kubernetes Master node (rhel3). You must SSH the rhel3 host in your
    labs. Task 1 will show you how to copy the kubeconfig file to the
    Windows jump host, in order to be able to execute the kubectl
    commands from PS or CMD.**

## Task 1: Kuberetes CLUSTER CONFIGURATION

In this task, you will explore the Kubernetes Cluster Configuration,
create a user, and install kubectl on Windows.

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
<td><img src="./media/image1.png" style="width:0.55208in;height:0.55208in" />Note: All the command references and architectural details are available on the official Kubernetes Website: <a href="https://kubernetes.io/docs/home/">https://kubernetes.io/docs/home/</a></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><img src="./media/image2.png" style="width:0.55208in;height:0.55208in" />Remember you created an alias “k” for “kubectl”. In the following lab exercises, you can use k instead of kubectl.</td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Using Putty, open an SSH connection to rhel3, your Kubernetes master node:</p>
<p><img src="./media/image3.png" style="width:4.00952in;height:3.92082in" /></p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Review the available options in kubectl:</p>
<p>kubectl --help</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Make sure autocompletion is enabled for kubectl:</p>
<p>source &lt;(kubectl completion bash)</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Review the Kubernetes configuration:</p>
<p>kubectl config view</p>
<p><img src="./media/image2.png" style="width:0.55208in;height:0.55208in" /></p>
<p>If you use multiple kubeconfig files at the same time and you want to merge the views:</p>
<p>KUBECONFIG=~/.kube/config:~/.kube/kubconfig2 kubectl config view</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Get the Kubernetes version in json or yaml format:</p>
<p>kubectl version -o json (or -o yaml)</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Create a new Kubernetes service account called rouser (Read-Only User):</p>
<p>kubectl create serviceaccount rouser</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Create a cluster role called rouser:</p>
<p>kubectl create clusterrole rouser --verb=get --verb=list --verb=watch --resource=pods</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Create a cluster role binding:</p>
<p>kubectl create clusterrolebinding rouser --serviceaccount=default:rouser --clusterrole=rouser</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Now get the token from secret of ServiceAccount we have created before. we will use this token to authenticate user:</p>
<p>TOKEN=$(kubectl describe secrets "$(kubectl describe serviceaccount rouser | grep -i Tokens | awk '{print $2}')" | grep token: | awk '{print $2}')</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><p>Now set the credentials for the user in kube config file. We will use “learner” as a username:</p>
<p>kubectl config set-credentials learner --token=$TOKEN</p></td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>Now we will have to create a new context for the user. We will call it “reader”. Our Cluster is called “Kubernetes” (refer to step 1-4):</p>
<p>kubectl config set-context reader@kubernetes --cluster=kubernetes --user=learner</p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td><p>Review the Kubernetes contexts:</p>
<p>kubectl config view</p></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>Switch to the newly created context:</p>
<p>kubectl config use-context reader@kubernetes</p></td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td><p>Notice that the current-context has changed to reader@kubernetes:</p>
<p>kubectl config view</p></td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td><img src="./media/image1.png" style="width:0.48889in;height:0.48889in" />Note: you can use external tools such as kubectx to easily switch between contexts.</td>
</tr>
<tr class="even">
<td><ol start="18" type="1">
<li></li>
</ol></td>
<td><p>Verify that the user has the right privileges:</p>
<p>kubectl auth can-i get pods --all-namespaces (should be yes)</p>
<p>kubectl auth can-i create pods (should be no)</p>
<p>kubectl auth can-i delete pods (should be no)</p></td>
</tr>
<tr class="odd">
<td><ol start="19" type="1">
<li></li>
</ol></td>
<td><p>Switch back to the default administrator context:</p>
<p>kubectl config use-context kubernetes-admin@kubernetes</p></td>
</tr>
<tr class="even">
<td><ol start="20" type="1">
<li></li>
</ol></td>
<td><p>Check the privileges and compare:</p>
<p>kubectl auth can-i get pods --all-namespaces</p>
<p>kubectl auth can-i create pods</p>
<p>kubectl auth can-i delete pods</p></td>
</tr>
<tr class="odd">
<td><ol start="21" type="1">
<li></li>
</ol></td>
<td><img src="./media/image1.png" style="width:0.55208in;height:0.55208in" />Note: Steps 1-22 through 1-32 are optional. We will now copy the config file of the Kubernetes cluster’s master node rhel3 to the windows jump host. The following procedure is documented here: <a href="https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-windows">https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-windows</a></td>
</tr>
<tr class="even">
<td><ol start="22" type="1">
<li></li>
</ol></td>
<td>Let’s start by installing kubectl on your jump host. Open the Chrome web browser.</td>
</tr>
<tr class="odd">
<td><ol start="23" type="1">
<li></li>
</ol></td>
<td>Within Chrome, enter: <a href="http://storage.googleapis.com/kubernetes-release/release/v1.5.3/bin/windows/amd64/kubectl.exe">http://storage.googleapis.com/kubernetes-release/release/v1.5.3/bin/windows/amd64/kubectl.exe</a></td>
</tr>
<tr class="even">
<td><ol start="24" type="1">
<li></li>
</ol></td>
<td>After you download the folder, move to a new directory: C:\kube</td>
</tr>
<tr class="odd">
<td><ol start="25" type="1">
<li></li>
</ol></td>
<td>Using the Windows search to locate the Control Panel. Select System. Select Advanced system settings. Select Environment Variables. Add C:\kube to your PATH environment variable.</td>
</tr>
<tr class="even">
<td><ol start="26" type="1">
<li></li>
</ol></td>
<td><p>Verify the installation and the version at a command prompt:</p>
<p>kubectl version --client -o json</p></td>
</tr>
<tr class="odd">
<td><ol start="27" type="1">
<li></li>
</ol></td>
<td><p>Verify the kube config:</p>
<p>kubectl config view</p>
<p><strong>Note that it is empty. This is because the automated script configuration didn’t detect the host as being a master node.</strong></p></td>
</tr>
<tr class="even">
<td><ol start="28" type="1">
<li></li>
</ol></td>
<td><p>Create a new folder called kube under your home directory (C:\Users\Administrator.DEMO):</p>
<p>mkdir .kube</p></td>
</tr>
<tr class="odd">
<td><ol start="29" type="1">
<li></li>
</ol></td>
<td><p>Using WinSCP or copy/paste, transfer the “config” file from $HOME/.kube/config on the rhel3 master node to the newly created .kube folder on your windows jump host:</p>
<p><img src="./media/image4.png" style="width:4.80851in;height:1.35893in" /></p></td>
</tr>
<tr class="even">
<td><ol start="30" type="1">
<li></li>
</ol></td>
<td><p>Now go back to the command prompt and type:</p>
<p>kubectl get nodes</p>
<p><strong>You have now successfully created a management host for one or multiple Kubernetes clusters</strong>.</p></td>
</tr>
<tr class="odd">
<td><ol start="31" type="1">
<li></li>
</ol></td>
<td><p>Start a Web proxy on the default port 8001:</p>
<p>kubectl proxy</p></td>
</tr>
<tr class="even">
<td><ol start="32" type="1">
<li></li>
</ol></td>
<td><p>Verify that the connection is working properly and that you can access the Kubernetes APIs.</p>
<p>Using Chrome, browse the URL <a href="http://127.0.0.1:8001/api/v1">http://127.0.0.1:8001</a>. Explore more on your own.</p></td>
</tr>
</tbody>
</table>

## Task 2: OPTIONAL TASK - Software Upgrade 

In this task, you will learn how to upgrade the Kubernetes software
components using the kubeadm command.

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
<td><img src="./media/image5.png" style="width:0.55208in;height:0.55208in" />You only can upgrade from one MINOR version to the next MINOR version, or between PATCH versions of the same MINOR. That is, you cannot skip MINOR versions when you upgrade. For example, you can upgrade from 1.y to 1.y+1, but not from 1.y to 1.y+2</td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Using Putty, SSH to host rhel3, your master node:</p>
<p><img src="./media/image3.png" style="width:4.00952in;height:3.92082in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Enter the following command to verify if new version of the Kubernetes components used are available:</p>
<p>kubeadm upgrade plan</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Verify the version of Kubernetes on your nodes (note the difference with rhel6, the node added in task 3):</p>
<p>kubectl get nodes</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Verify the version of kubeadm:</p>
<p>kubeadm version</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Install the new kubeadm version on the master node (v1.15.10):</p>
<p>yum install -y kubeadm-1.15.10-0 --disableexcludes=kubernetes</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Verify the version of kubeadm:</p>
<p>kubeadm version</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Drain the master node to remove it from the cluster:</p>
<p>kubectl drain rhel3 --ignore-daemonsets</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Apply the new version of kubeadm (answer y when prompted):</p>
<p>kubeadm upgrade apply v1.15.10</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>On the master node, install the same version of kubectl and kubelet:</p>
<p>yum install -y kubelet-1.15.10-0 kubectl-1.15.10-0 --disableexcludes=kubernetes</p>
<p>systemctl restart kubelet</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Reactivate the node as part of the active kubernetes cluster by making it schedulable:</p>
<p>kubectl uncordon rhel3</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><p>Verify the version of Kubernetes on your nodes (the master should now run 1.15.10):</p>
<p>kubectl get nodes</p></td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>Now, let’s upgrade kubeadm to v1.15.10 on all worker nodes (steps must be repeated on rhel1 and rhel2):</p>
<p>yum install -y kubeadm-1.15.10-0 --disableexcludes=kubernetes</p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td><p>On your master node rhel3, you can follow the upgrade process:</p>
<p>watch kubectl get nodes</p></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>Drain the worker nodes to remove them from the cluster (in the labs, you can perform both workers at the same time. In production, you must ensure a minimum number of nodes are running to sustain your traffic/application):</p>
<p>kubectl drain rhel1 --ignore-daemonsets</p>
<p>kubectl drain rhel2 --ignore-daemonsets</p></td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td><p>Apply the new version of kubeadm on rhel1 and rhel2:</p>
<p>kubeadm upgrade node</p></td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td><p>Install the same version of kubectl and kubelet on both worker nodes:</p>
<p>yum install -y kubelet-1.15.10-0 kubectl-1.15.10-0 --disableexcludes=kubernetes</p></td>
</tr>
<tr class="even">
<td><ol start="18" type="1">
<li></li>
</ol></td>
<td><p>Restart the kubelet service to apply the new version to your nodes:</p>
<p>systemctl restart kubelet</p></td>
</tr>
<tr class="odd">
<td><ol start="19" type="1">
<li></li>
</ol></td>
<td><p>Reactivate the worker nodes as part of the active kubernetes cluster by making them schedulable:</p>
<p>kubectl uncordon rhel1</p>
<p>kubectl uncordon rhel2</p></td>
</tr>
<tr class="even">
<td><ol start="20" type="1">
<li></li>
</ol></td>
<td><p><img src="./media/image2.png" style="width:0.55208in;height:0.55208in" />You kubernetes cluster is now entirely running version 1.15.10.</p>
<p>Optionally, you can repeat the steps to upgrade to version 1.16.7. It is not recommended to upgrade to version 1.17.x for stability reasons with Trident.</p></td>
</tr>
<tr class="odd">
<td><ol start="21" type="1">
<li></li>
</ol></td>
<td><img src="./media/image5.png" style="width:0.55208in;height:0.55208in" />Upgrading Kubernetes clusters is long and complex, and there is a lot of possibilities for human errors. That’s why most productive environments implement version control and deployment automation with external tools such as Ansible. Hosted Kubernetes Services such as NetApp Kubernetes Service offer version control and upgrade automation as part of the standard offering and interface.</td>
</tr>
</tbody>
</table>

End of Exercise
