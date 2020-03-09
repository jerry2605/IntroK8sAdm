Module 5: Pod Persistence

## Objectives

This exercise focuses on enabling you to do the following:

  - > Create ReplicaSets

  - > Create Deployments

  - > Add a DaemonSet

  - > Configure a StatefulSet

  - > Use Jobs and CronJobs

  - > Use Labels and Selectors
    
    **NOTE: All the tasks in the following module are executed on the
    Kubernetes Master node. You must SSH the RHEL3 host in your labs or
    copy the config file to the Windows jumphost.**

## Task 1: Create ReplicaSets

In this task, you will create and scale a ReplicaSet. Explore and gain
an understanding of how the Pods are generated from the Pod template and
how they are targeted with selectors.

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
<td>Make sure that you are in the ~/k8s/course/ folder on RHEL3.</td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Create a ReplicaSet definition file with the following configuration:</p>
<ul>
<li><p>Name: rs-example</p></li>
<li><p>Replicas: 3</p></li>
<li><p>Image: nginx:stable-alpine</p></li>
<li><p>Labels and selectors to target app=nginx and env=prod</p></li>
</ul></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Instantiate the ReplicaSet (a default file rs-example.yaml is provide in the Courses download):</p>
<p>kubectl create -f rs-example.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Watch as the newly created ReplicaSet provisions the Pods based off the Pod template:</p>
<p>kubectl get Pods --watch --show-labels</p>
<p><strong>Note:</strong> The newly provisioned Pods are given a name based off the ReplicaSet name appended with a 5-character random string. These Pods are labeled with the labels as specified in the manifest.</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Scale ReplicaSet rs-example up to 5 replicas with the below command:</p>
<p>kubectl scale replicaset rs-example --replicas=5</p>
<p><strong>Tip:</strong> replicaset can be substituted with rs when using kubectl.</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Describe rs-example and take note of the Replicas and Pod Status field in addition to the Events:</p>
<p>kubectl describe rs rs-example</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Now, using the scale command bring the replicas back down to 3:</p>
<p>kubectl scale rs rs-example --replicas=3</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Watch as the ReplicaSet Controller terminates 2 of the Pods to bring the cluster back into its desired state of 3 replicas:</p>
<p>kubectl get pods --show-labels --watch</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Once rs-example is back down to 3 Pods, create the additional Pod with the same labels as the one targeted by rs-example ReplicaSet:</p>
<p>kubectl create -f rs-example-pod.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Immediately watch the Pod creation:</p>
<p>kubectl get pods --show-labels –-watch</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Describe rs-example and look at the events:</p>
<p>kubectl describe rs rs-example</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td>Why is the non-rs-pod not running?</td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>Cleanup:</p>
<p>kubectl delete rs rs-example</p></td>
</tr>
</tbody>
</table>

## Task 2: create Deployments

Deployments are a declarative method of managing Pods via ReplicaSets.
They provide rollback functionality in addition to more granular update
control mechanisms. This task will allow you to create, update and scale
a Deployment as well as explore the relationship of Deployment,
ReplicaSet and Pod.

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
<td><p>Review the deploy-example file:</p>
<p>cat deploy-example.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Create the Deployment:</p>
<p>kubectl create -f deploy-example.yaml --record</p>
<p><strong>Note:</strong> The --record flag saves the command as an annotation, and it can be thought of similar to a git commit message.</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Check the status of the Deployment:</p>
<p>kubectl get deployments</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Once the Deployment is ready, view the current ReplicaSets and review the labels:</p>
<p>kubectl get rs --show-labels</p>
<p>Notice the name and pod-template-hash label of the newly created ReplicaSet. The created ReplicaSet's name will include the pod-template-hash.</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Describe the generated ReplicaSet:</p>
<p>kubectl describe rs deploy-example-&lt;pod-template-hash&gt;</p>
<p>Look at both the Labels and the Selectors fields. The pod-template-hash value has automatically been added to both the Labels and Selector of the ReplicaSet. Then take note of the Controlled By field. This will reference the direct parent object, and in this case the original deploy-example Deployment.</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Get the Pods and pass the --show-labels flag:</p>
<p>kubectl get pods --show-labels</p>
<p>Just as with the ReplicaSet, the Pods name are labels include the pod-template-hash.</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Describe one of the Pods.</p>
<p>kubectl describe pod deploy-example-&lt;pod-template-hash-&lt;random&gt;</p>
<p>Look at the Controlled By field. It will contain a reference to the parent ReplicaSet, but not the parent Deployment.</p>
<p>Now that the relationship from Deployment to ReplicaSet to Pod is understood. It is time to update the deploy-example and see an update in action.</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Update the deploy-example manifest and add a few additional labels to the Pod template. Once done, apply the change with the --record flag:</p>
<p>kubectl edit deploy deploy-example –record</p>
<p><strong>Tip:</strong> deploy can be substituted for deployment when using kubectl.</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Immediately watch the Pods:</p>
<p>kubectl get pods --show-labels –watch</p>
<p>The old version of the Pods will be phased out one at a time and instances of the new version will take its place. The way in which this is controlled is through the strategy stanza. For specific documentation this feature, see the <a href="https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy">Deployment Strategy Documentation</a>.</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Now view the ReplicaSets:</p>
<p>kubectl get rs --show-labels</p>
<p>There will now be two ReplicaSets, with the previous version of the Deployment being scaled down to 0.</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Now, scale the Deployment up as you would a ReplicaSet, and set the replicas=5:</p>
<p>kubectl scale deploy deploy-example --replicas=5</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><p>List the ReplicaSets:</p>
<p>kubectl get rs --show-labels</p>
<p>Notice that there is <strong>NO</strong> new ReplicaSet generated. Scaling actions do <strong>NOT</strong> trigger a change in the Pod Template.</p></td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>Like before, describe the Deployment, ReplicaSet and one of the Pods. Note the Events and Controlled By fields. It should present a clear picture of relationship between objects during an update of a Deployment:</p>
<p>kubectl describe deploy deploy-example</p>
<p>kubectl describe rs deploy-example-&lt;pod-template-hash&gt;</p>
<p>kubectl describe pod deploy-example-&lt;pod-template-hash-&lt;random&gt;</p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td>Deployments are the main method of managing applications deployed within Kubernetes. They create and supervise targeted ReplicaSets by generating a unique hash called the pod-template-hash and attaching it to child objects as a Label along with automatically including it in their Selector. This method of managing rollouts along with being able to define the methods and tolerances in the update strategy permits for a safe and seamless way of updating an application in place.</td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>To rollback a deployment, we have to identify the versions available first. Use the rollout command to view the history of the Deployment deploy-example:</p>
<p>kubectl rollout history deployment deploy-example</p>
<p>There should be two revisions. One for when the Deployment was first created, and another when the additional Labels were added. The number of revisions saved is based off of the revisionHistoryLimit attribute in the Deployment spec.</p></td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td><p>Look at the details of a specific revision by passing the --revision=&lt;revision number&gt; flag:</p>
<p>kubectl rollout history deployment deploy-example --revision=1</p>
<p>kubectl rollout history deployment deploy-example --revision=2</p>
<p>Viewing the specific revision will display a summary of the Pod Template.</p></td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td><p>Choose to go back to revision 1 by using the rollout undo command:</p>
<p>kubectl rollout undo deployment deploy-example --to-revision=1</p>
<p><strong>Tip:</strong> The --to-revision flag can be omitted if you wish to just go back to the previous configuration.</p></td>
</tr>
<tr class="even">
<td><ol start="18" type="1">
<li></li>
</ol></td>
<td><p>Immediately watch the Pods:</p>
<p>kubectl get pods --show-labels -–watch</p>
<p>They will cycle through rolling back to the previous revision.</p></td>
</tr>
<tr class="odd">
<td><ol start="19" type="1">
<li></li>
</ol></td>
<td><p>Describe the Deployment deploy-example:</p>
<p>$ kubectl describe deployment deploy-example</p>
<p>The events will describe the scaling back of the previous and switching over to the desired revision.</p></td>
</tr>
<tr class="even">
<td><ol start="20" type="1">
<li></li>
</ol></td>
<td>Understanding how to use rollout command to both get a diff of the different revisions as well as be able to roll-back to a previously known good configuration is an important aspect of Deployments that cannot be left out.</td>
</tr>
<tr class="odd">
<td><ol start="21" type="1">
<li></li>
</ol></td>
<td><p>Cleanup the deployment:</p>
<p>kubectl delete deploy deploy-example</p></td>
</tr>
</tbody>
</table>

## 

## Task 3: Add a DaemonSet 

DaemonSets ensure that all nodes matching certain criteria will run an
instance of the supplied Pod. A DaemonSet bypasses the default
scheduling mechanisms and restrictions and are ideal for cluster wide
services such as log forwarding, or health monitoring.

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
<td><p>Review the ds-example file:</p>
<p>cat ds-example.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Create DaemonSet ds-example and pass the --record flag:</p>
<p>kubectl create -f ds-example.yaml --record</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>View the current DaemonSets:</p>
<p>kubectl get daemonset</p>
<p>As there are no matching nodes, no Pods should be scheduled.</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Label the RHEL1 node with nodeType=edge:</p>
<p>kubectl label node rhel1 nodeType=edge</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>View the current DaemonSets once again:</p>
<p>kubectl get daemonsets</p>
<p>There should now be a single instance of the DaemonSet ds-example deployed.</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>View the current Pods and display their labels with --show-labels:</p>
<p>kubectl get Pods --show-labels</p>
<p>Notice that the deployed Pod has a controller-revision-hash label. This is used like the pod-template-hash in a Deployment to track and allow for rollback functionality.</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Describing the DaemonSet will provide you with status information regarding the DaemonSet cluster wide:</p>
<p>kubectl describe ds ds-example</p>
<p><strong>Tip:</strong> ds can be substituted for daemonset when using kubectl.</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Update the DaemonSet by adding a few additional labels to the Pod Template and use the --record flag:</p>
<p>kubectl apply -f ds-example.yaml --record</p>
<p>&lt; or &gt;</p>
<p>kubectl edit ds ds-example --record</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Watch the Pods and be sure to show the labels:</p>
<p>kubectl get pods --show-labels –-watch</p>
<p>The old version of the DaemonSet will be phased out one at a time and instances of the new version will take its place. Similar to Deployments, DaemonSets have their own equivalent to a Deployment's strategy in the form of updateStrategy. The defaults are generally suitable, but other tuning options may be set. For reference, see the <a href="https://kubernetes.io/docs/tasks/manage-daemon/update-daemon-set/#performing-a-rolling-update">Updating DaemonSet Documentation</a>.</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Cleanup the DaemonSet:</p>
<p>kubectl delete ds ds-example</p></td>
</tr>
</tbody>
</table>

## Task 4: Configure a StatefulSet

The StatefulSet controller is tailored to managing Pods that must
persist or maintain state. Pod identity including hostname, network, and
storage can be considered **persistent**. In this task, you will be
creating, updating, and rolling back a StatefulSet. Additionally, delve
into the process of how they are scheduled and how an update occurs.

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
<td><p>Review the sts-example file:</p>
<p>cat sts-example.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Create the StatefulSet from its definition file:</p>
<p>kubectl create -f sts-example.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Immediately watch the Pods being created:</p>
<p>kubectl get Pods --show-labels --watch</p>
<p>Unlike Deployments or DaemonSets, the Pods of a StatefulSet are created one-by-one, going by their ordinal index. Meaning, sts-example-0 will fully be provisioned before sts-example-1 starts up. Additionally, take notice of the controller-revision-hash label. This serves the same purpose as the controller-revision-hash label in a DaemonSet or the pod-template-hash in a Deployment. It provides a means of tracking the revision of the Pod Template and enables rollback functionality.</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>More information on the StatefulSet can be gleaned about the state of the StatefulSet by describing it:</p>
<p>kubectl describe statefulset sts-example</p>
<p>Within the events, notice that it is creating claims for volumes before each Pod is created.</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>View the current Persistent Volume Claims:</p>
<p>kubectl get pvc</p>
<p>The StatefulSet controller creates a volume for each instance based off the volumeClaimTemplate. It prepends the volume name to the Pod name. e.g. www-sts-example-0. We will discuss persistent volume claims in Module 7.</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Update the StatefulSet's Pod Template and add a few additional labels:</p>
<p>kubectl apply -f sts-example.yaml --record</p>
<p>&lt; or &gt;</p>
<p>kubectl edit statefulset sts-example --record</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Return to watching the Pods:</p>
<p>kubectl get Pods --show-labels</p>
<p>None of the Pods are being updated to the new version of the Pod.</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Delete the sts-example-2 Pod:</p>
<p>kubectl delete Pod sts-example-2</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Immediately view the Pods:</p>
<p>kubectl get Pods --show-labels -–watch</p>
<p>The new sts-example-2 Pod should be created with the new additional labels. The OnDelete Update Strategy will not spawn a new iteration of the Pod until the previous one was <strong>deleted</strong>. This allows for manual gating the update process for the StatefulSet.</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Update the StatefulSet and change the Update Strategy Type to RollingUpdate:</p>
<p>kubectl apply -f sts-example.yaml --record</p>
<p>&lt; or &gt;</p>
<p>kubectl edit statefulset sts-example --record</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Again, immediately view the Pods:</p>
<p>kubectl get Pods --show-labels –-watch</p>
<p>Note that the Pods are sequentially updated in descending order, or largest to smallest based on the Pod's ordinal index. This means that if sts-example-2 was not updated already, it would be updated first, then sts-example-1 and finally sts-example-0.</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><p>Delete the StatefulSet sts-example:</p>
<p>kubectl delete statefulset sts-example</p></td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>View the Persistent Volume Claims:</p>
<p>kubectl get pvc</p>
<p>Created PVCs are <strong>NOT</strong> garbage collected automatically when a StatefulSet is deleted. They must be reclaimed independently of the StatefulSet itself.</p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td><p>Recreate the StatefulSet using the same manifest:</p>
<p>kubectl create -f sts-example.yaml --record</p></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>View the Persistent Volume Claims:</p>
<p>kubectl get pvc</p>
<p>Note that new PVCs were <strong>NOT</strong> provisioned. The StatefulSet controller assumes if the matching name is present, that PVC is intended to be used for the associated Pod.</p></td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td><strong>Summary:</strong> Like many applications where state must be taken into account, the planning and usage of StatefulSets requires forethought. The consistency brought by standard naming, ordered updates/deletes and templated storage does however make this task easier.</td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td>We will now create a <em>"headless service"</em> or a service without a ClusterIP (ClusterIP=None) for use with the StatefulSet sts-example, then explore how this enables consistent service discovery.</td>
</tr>
<tr class="even">
<td><ol start="18" type="1">
<li></li>
</ol></td>
<td><p>Review the sts-example-service file:</p>
<p>cat sts-example-service.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="19" type="1">
<li></li>
</ol></td>
<td><p>Create the headless service app using the app=stateful selector from the yaml above:</p>
<p>kubectl create -f sts-example-service.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="20" type="1">
<li></li>
</ol></td>
<td><p>Describe the newly created service:</p>
<p>kubectl describe svc app</p>
<p>‌</p>
<p>Notice that it does not have a clusterIP, but does have the Pod Endpoints listed. Headless services are unique in this behavior.</p></td>
</tr>
<tr class="odd">
<td><ol start="21" type="1">
<li></li>
</ol></td>
<td><p>Query the DNS entry for the app service:</p>
<p>kubectl exec sts-example-0 -- nslookup app.default.svc.cluster.local</p>
<p>An A record will have been returned for each instance of the StatefulSet. Querying the service directly will do simple DNS round-robin load-balancing.</p></td>
</tr>
<tr class="even">
<td><ol start="22" type="1">
<li></li>
</ol></td>
<td><p>Finally, query one of instances directly:</p>
<p>kubectl exec sts-example-0 -- nslookup sts-example-1.app.default.svc.cluster.local</p>
<p>This is a unique feature to StatefulSets. This allows for services to directly interact with a specific instance of a Pod. If the Pod is updated and obtains a new IP, the DNS record will immediately point to it enabling consistent service discovery.</p></td>
</tr>
<tr class="odd">
<td><ol start="23" type="1">
<li></li>
</ol></td>
<td><strong>Summary:</strong> StatefulSet service discovery is unique within Kubernetes in that it augments a headless service (A service without a unique ClusterIP) to provide a consistent mapping to the individual Pods. These mappings take the form of an A record in format of: &lt;StatefulSet Name&gt;-&lt;ordinal&gt;.&lt;service name&gt;.&lt;namespace&gt;.svc.cluster.local and can be used consistently throughout other Workloads.</td>
</tr>
<tr class="even">
<td><ol start="24" type="1">
<li></li>
</ol></td>
<td><p>Cleanup commands:</p>
<p>kubectl delete svc app</p>
<p>kubectl delete statefulset sts-example</p>
<p>kubectl delete pvc www-sts-example-0</p>
<p>kubectl delete pvc www-sts-example-1 www-sts-example-2</p></td>
</tr>
</tbody>
</table>

## Task 5: Use Jobs and CronJObs

The Job Controller ensures one or more Pods are executed and
successfully terminate. Essentially a task executor that can be run in
parallel. CronJobs are an extension of the Job Controller and enable
Jobs to be run on a schedule. In this task, you will create a Kubernetes
Job and work to understand how the Pods are managed with completions and
parallelism directives.

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
<td><p>Review the job-example file:</p>
<p>cat job-example.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Instantiate the job-example:</p>
<p>kubectl create -f job-example.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Watch the Pods as they are being created:</p>
<p>kubectl get Pods --show-labels –-watch</p>
<p>Only two Pods are being provisioned at a time; adhering to the parallelism attribute. This is done until the total number of completions is satisfied. Additionally, the Pods are labeled with controller-uid, this acts as a unique ID for that specific Job.</p>
<p>When done, the Pods persist in a Completed state. They are not deleted after the Job is completed or failed. This is intentional to better support troubleshooting.</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>A summary of these events can be seen by describing the Job itself:</p>
<p>kubectl describe job job-example</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Delete the job:</p>
<p>kubectl delete job job-example</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>View the Pods once more:</p>
<p>kubectl get Pods</p>
<p>The Pods will now be deleted. They are cleaned up when the Job itself is removed.</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><strong>Summary:</strong> Jobs are fire and forget one off tasks, batch processing or as an executor for a workflow engine. They <em>"run to completion"</em> or terminate gracefully adhering to the completions and parallelism directives.</td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Review the cronjob-example file:</p>
<p>cat cronjob-example.yaml</p>
<p>It is configured to run the previous job example every minute, using the cron schedule "*/1 * * * *". This schedule is <strong>UTC ONLY.</strong></p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Instantiate CronJob:</p>
<p>kubectl create -f cronjob-example.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Give it some time to run, and then list the Jobs:</p>
<p>kubectl get jobs</p>
<p>There should be at least one Job named in the format &lt;cronjob-name&gt;-&lt;unix time stamp&gt;. Note the timestamp of the oldest Job.</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Give it a few minutes and list the Jobs once again:</p>
<p>kubectl get jobs</p>
<p>The oldest Job should have been removed. The CronJob controller will purge Jobs according to the successfulJobHistoryLimit and failedJobHistoryLimit attributes. In this case, it is retaining strictly the last 3 successful Jobs.</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><p>Describe the CronJob cronjob-example:</p>
<p>kubectl describe CronJob cronjob-example</p>
<p>The events will show the records of the creation and deletion of the Jobs.</p></td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>Edit the CronJob cronjob-example and locate the Suspend field. Then set it to true:</p>
<p>kubectl edit CronJob cronjob-example</p>
<p>This will prevent the cronjob from firing off any future events and is useful to do to initially troubleshoot an issue without having to delete the CronJob directly.</p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td><p>Delete the CronJob:</p>
<p>kubectl delete cronjob cronjob-example</p>
<p>Deleting the CronJob <strong>WILL</strong> delete all child Jobs. Use Suspend to <em>'stop'</em> the Job temporarily if attempting to troubleshoot.</p></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>Clean the CronJob:</p>
<p>kubectl delete CronJob cronjob-example</p></td>
</tr>
</tbody>
</table>

## Task 6: USE Labels and selectors 

In this task, you will learn how labels and selectors are used in
Kubernetes.

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
<td><p>Create several objects to investigate:</p>
<p>kubectl create -f label-1.yaml</p>
<p>kubectl create -f label-2.yaml</p>
<p>kubectl create -f label-3.yaml</p>
<p>kubectl create -f label-4.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>List the labels create on the Pods in the default namespace:</p>
<p>kubectl get Pods --show-labels</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>List the labels create on the Pods in the default namespace:</p>
<p>kubectl get all --show-labels</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>There is an error in the label-5.yaml file. Fix the error and deploy the ReplicaSet by running:</p>
<p>kubectl create -f label-5.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Cleanup:</p>
<p>kubectl delete deployment app-1</p>
<p>kubectl delete deployment app-2</p>
<p>kubectl delete service app-1</p>
<p>kubectl delete service app-2</p>
<p>kubectl delete replicaset app-3</p></td>
</tr>
</tbody>
</table>

## End of Exercise
