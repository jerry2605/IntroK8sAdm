Module 4: Scheduler

## Objectives

This exercise focuses on enabling you to do the following:

  - > Manual schedule a Pod

  - > Create taints and tolerations

  - > Define node affinities

  - > Work with Resource Limits
    
    **NOTE: All the tasks in the following module are executed on the
    Kubernetes Master node. You must SSH the RHEL3 host in your labs, or
    copy the config file to the Windows jump host, in order to be able
    to execute the kubectl commands.**

## Task 1: Manual schedule a Pod

In this task, you will manually schedule a Pod.

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
<td><p>Navigate to the folder where you cloned the GitHub repository on the RHEL3 host: </p>
<p>cd ~/k8s/course/ </p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Instantiate a copy of the manual Pod:</p>
<p>kubectl create -f manual-schedule.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td>What is the state of the Pod? Use kubectl get pods</td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Why is the Pod’s state Pending? View the content of manual-schedule.yaml:</p>
<p>cat manual-schedule.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td>Update manual-schedule file so that the nodeName key has a value of one of the worker node names in your environment.</td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Instantiate a copy of the manual Pod:</p>
<p>kubectl create -f manual-schedule.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td>What is the state of the Pod? You should have successfully manually scheduled the Pod to run on a worker node.</td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Delete the Pod:</p>
<p>kubectl delete pod manual</p></td>
</tr>
</tbody>
</table>

## Task 2: CREATE Taints and tolerations 

In this task, you will learn how to set taints on nodes and tolerations
on pods to ensure that nodes only run certain pods.

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
<td>How many nodes are in the cluster? Use kubectl get nodes</td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td>Are there any taints on RHEL1? Use kubectl describe node rhel1</td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Create a taint on RHEL1:</p>
<p>kubectl taint nodes rhel1 app=blue:NoSchedule</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td>Are there any taints on RHEL2? Use kubectl describe node rhel2</td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Create a taint on RHEL2:</p>
<p>kubectl taint nodes rhel2 app=blue:NoSchedule</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Create a red Pod:</p>
<p>kubectl create -f toleration-red.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td>What is the state of the red Pod and why? Use kubectl get pods</td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Create a blue Pod:</p>
<p>kubectl create -f toleration-blue.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td>What is the state of the blue Pod and why? Use kubectl get pods</td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td>Are there any taints on RHEL3 (the master node)? Use kubectl describe node rhel3</td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Remove the taint from the master node:</p>
<p>kubectl taint nodes rhel3 node-role.kubernetes.io/master:NoSchedule-</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td>What is the status of the red Pod?</td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td>What node is it running on? Use kubectl get pods -o wide</td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td><p>Re-add the taint to the master node:</p>
<p>kubectl taint node rhel3 node-role.kubernetes.io/master=true:NoSchedule</p></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td>What is the status of the red Pod and why?</td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td><p>Delete the red Pod:</p>
<p>kubectl delete pod red</p></td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td><p>Delete the blue Pod:</p>
<p>kubectl delete pod blue</p></td>
</tr>
<tr class="even">
<td><ol start="18" type="1">
<li></li>
</ol></td>
<td><p>Remove the taint on RHEL1:</p>
<p>kubectl taint nodes rhel1 app=blue:NoSchedule-</p></td>
</tr>
<tr class="odd">
<td><ol start="19" type="1">
<li></li>
</ol></td>
<td><p>Remove the taint on RHEL2:</p>
<p>kubectl taint nodes rhel2 app=blue:NoSchedule-</p></td>
</tr>
</tbody>
</table>

## Task 3: DEFINE NOde affinities

In this task, you will create a label on a node and then observe the
effects of node affinities set on Pods.

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
<td>What labels are on RHEL1? Use kubectl describe node rhel1</td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Apply a label to the RHEL1 node:</p>
<p>kubectl label node rhel1 app=blue</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Create a deployment:</p>
<p>kubectl run blue --image=nginx --replicas=6</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td>Which nodes are they placed on and why?</td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Delete the deployment:</p>
<p>kubectl delete deployment blue</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Configure the affinities-1.yaml with the following configuration:</p>
<ul>
<li><p>Name: blue</p></li>
<li><p>Replicas: 6</p></li>
<li><p>Image: nginx</p></li>
<li><p>NodeAffinity: requiredDuringSchedulingIgnoredDuringExecution</p></li>
<li><p>Key: app</p></li>
<li><p>Value: blue</p></li>
</ul></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Deploy the object:</p>
<p>kubectl create -f affinities-1.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td>What nodes are in the pods on and why?</td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Delete the deployment:</p>
<p>kubectl delete deployment blue</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Configure the affinities-2.yaml with the following configuration:</p>
<ul>
<li><p>Name: red</p></li>
<li><p>Replicas: 6</p></li>
<li><p>Image: nginx</p></li>
<li><p>NodeAffinity: requiredDuringSchedulingIgnoredDuringExecution</p></li>
<li><p>Key: node-role.kubernetes.io/master</p></li>
<li><p>Operator: Exists</p></li>
</ul></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Deploy the object:</p>
<p>kubectl create -f affinities-2.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td>What is the status of the pods on and why?</td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>Remove the taint from the master node:</p>
<p>kubectl taint nodes rhel3 node-role.kubernetes.io/master:NoSchedule-</p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td>What is the status of the red Pod and why? If they are running, what node are they running on and why?</td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>Re-add the taint to the master node:</p>
<p>kubectl taint node rhel3 node-role.kubernetes.io/master=true:NoSchedule</p></td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td>What is the status of the red Pod and why?</td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td><p>Delete the deployment:</p>
<p>kubectl delete deployment red</p></td>
</tr>
</tbody>
</table>

## Task 4: work with Resources limits

In this task, you will explore the resource limits on Pods.

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
<td>Deploy the resource-1.yaml Pod.</td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td>What is the status of the Pod? What are the resource limits? How much memory does it require?</td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td>Delete the Pod stress-1.</td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td>Deploy the resource-2.yaml Pod.</td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td>What is the status? What are the resource limits? How much memory does it require?</td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td>Increase the memory limit of stress-2 Pod to 20Mi and redeploy the Pod. (You might have to delete the exist Pod and redeploy it.)</td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td>What is the status of the stress-2 Pod? It should be running.</td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td>Delete the Pod stress-2.</td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td>Deploy the resource-3.yaml Pod.</td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td>What is the status? What are the resource limits? How much cpu does it require?</td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td>Increase the cpu limit of stress-3 Pod to 2 and redeploy the Pod. (You might have to delete the exist Pod and redeploy it.)</td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td>What is the status of the stress-3 Pod? It should be running.</td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td>Delete the Pod stress-3.</td>
</tr>
</tbody>
</table>

End of Exercise
