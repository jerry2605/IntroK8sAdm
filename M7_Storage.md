Module 7: Storage in Kubernetes

## Objectives

This exercise focuses on enabling you to do the following:

  - > Provision NetApp storage for Kubernetes

  - > Create a Persistent Volume on ONTAP

  - > Create a Persistent Volume Snapshot on ONTAP with Trident

  - > Create a PVC and implement it with a Pod

  - > Resize an NFS Persistent Volume using Trident

Trident integrates with Kubernetes' standard interface for managing
storage providers. In Kubernetes, you request the creation of a
Persistent Volume (PV) by submitting a Persistent Volume Claim (PVC),
and the details of the PVC identify the desired storage class.

## Task 1: Provision NetApp storage for Kubernetes 

In order to handle the provisioning of NetApp storage for your
Kubernetes cluster, Trident needs to know what NetApp storage product(s)
you are using and the configuration details (IP addresses, credentials,
etc.) necessary to interact with them. The Trident administrator creates
a construct called a "backend" to contain this information. List the
backends configured in this environment. Backends are a Trident
construct rather than a native part of Kubernetes, so you need to use
the tridentctl command to query for the configured backends.

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
<td>Make sure that you are in the ~/k8s/course/ folder on RHEL3. </td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>List the available backends:</p>
<p>tridentctl get backends -n trident</p>
<p>Sample output:</p>
<p>+---------------------+----------------+--------------------------------------+--------+---------+</p>
<p>|        NAME         | STORAGE DRIVER |                 UUID                 | STATE  | VOLUMES |</p>
<p>+---------------------+----------------+--------------------------------------+--------+---------+</p>
<p>| BackendForSolidFire | solidfire-san  | d9d6bef6-eef9-4ff0-b5c8-c69d048b739e | online |       0 |</p>
<p>| BackendForNAS       | ontap-nas      | e098abb8-8e16-4b4f-a4bc-a6c9557b39b1 | online |       0 |</p>
<p>+---------------------+----------------+--------------------------------------+--------+---------+</p>
<p>The lab includes definitions for two backends: BackendForSolidFire and BackendForNAS. The value in the STORAGE DRIVER column indicates what kind of storage solution this backup offers. The ontap-nas driver provides the integration for delivering NFS storage on ONTAP platforms, and the solidfire-san driver handles block storage hosted on Element platforms.</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Query Trident for greater detail of the current configuration:</p>
<p>tridentctl get backend BackendForNAS -n trident -o json</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Display the contents of the json configuration file that was used to create the BackendForNAS backend:</p>
<p>cat backend-nas.json</p>
<p>Sample output:</p>
<p>{</p>
<p>"version": 1,</p>
<p>"storageDriverName": "ontap-nas",</p>
<p>"backendName": "BackendForNAS",</p>
<p>"managementLIF": "192.168.0.135",</p>
<p>"dataLIF": "192.168.0.132",</p>
<p>"svm": "svm1",</p>
<p>"username": "vsadmin",</p>
<p>"password": "Netapp1!"</p>
<p>}</p>
<p>The file shows that the backend is using the ontap-nas driver, which means the backend handles NFS volume provisioning from an ONTAP controller. The other fields in the backend description provide the SVM name, IP addresses, and login credentials that Trident needs when provisioning storage on ONTAP using this backend.</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>List the Storage Classes defined for the cluster:</p>
<p>kubectl get sc</p>
<p>Sample output:</p>
<p>NAME PROVISIONER AGE</p>
<p>sf-gold csi.trident.netapp.io 86d</p>
<p>sf-silver csi.trident.netapp.io 86d</p>
<p>storage-class-nas csi.trident.netapp.io 163d</p>
<p>storage-class-ssd csi.trident.netapp.io 129d</p>
<p>storage-class-storagepool csi.trident.netapp.io 163d</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Display the details of the storage-class-nas storage class:</p>
<p>kubectl describe sc storage-class-nas</p>
<p>Sample output:</p>
<p>Name: storage-class-nas</p>
<p>IsDefaultClass: No</p>
<p>Annotations: &lt;none&gt;</p>
<p>Provisioner: csi.trident.netapp.io</p>
<p>Parameters: backendType=ontap-nas</p>
<p>AllowVolumeExpansion: True</p>
<p>MountOptions: &lt;none&gt;</p>
<p>ReclaimPolicy: Delete</p>
<p>VolumeBindingMode: Immediate</p>
<p>Events: &lt;none&gt;</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Examine the json file that was used to create the storage-class-nas storage class:</p>
<p>cat sc-nas.yaml</p>
<p>Sample output:</p>
<p>apiVersion: storage.k8s.io/v1</p>
<p>kind: StorageClass</p>
<p>metadata:</p>
<p>name: storage-class-nas</p>
<p>provisioner: csi.trident.netapp.io</p>
<p>parameters:</p>
<p>backendType: "ontap-nas"</p>
<p>allowVolumeExpansion: true</p>
<p>If this lab did not already have the storage-class-nas storage class created, you could have used this YAML file to create it by issuing the kubectl create -f sc-nas.yaml command.</p></td>
</tr>
</tbody>
</table>

## task 2: Create a persistEnt Volume on ONTAP

In this exercise you will submit a PVC to request a PV from ONTAP.

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
<td><p>Examine the contents of pvcfornas.yaml file:</p>
<p>cat pvcfornas.yaml</p>
<p>Sample output:</p>
<p>kind: PersistentVolumeClaim</p>
<p>apiVersion: v1</p>
<p>metadata:</p>
<p>name: persistent-volume-claim-nas</p>
<p>spec:</p>
<p>accessModes:</p>
<p>- ReadWriteOnce</p>
<p>resources:</p>
<p>requests:</p>
<p>storage: 1Gi</p>
<p>storageClassName: storage-class-nas</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Submit the PVC to Kubernetes to request the volume:</p>
<p>kubectl create -f pvcfornas.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>To verify that the volume was created, display the details of the PVC request:</p>
<p>kubectl get pvc</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>List the PVs:</p>
<p>kubectl get pv</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Display the details of for this PV:</p>
<p>kubectl describe pv &lt;name of the PV volume created by the PVC&gt;</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>List the backends and use the backendUUID property value from the previous command to determine the name of the associated backend:</p>
<p>tridentctl get backends -n trident</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Query Trident for the BackendForNAS backend’s dataLIF property:</p>
<p>tridentctl get backend BackendForNAS -n trident -o json | grep dataLIF</p>
<p>Sample output:</p>
<p>"dataLIF": "192.168.0.132"</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Open a PuTTY session to cluster1 and authenticate with the following:</p>
<p>Username: admin</p>
<p>Password: Netapp1!</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Verify the svm1’s data LIF IP address:</p>
<p>network interface show -vserver svm1</p>
<p>Sample output:</p>
<p>Logical Status Network Current Current Is</p>
<p>Vserver Interface Admin/Oper Address/Mask Node Port Home</p>
<p>----------- ---------- ---------- ------------------ ------------- ------- ----</p>
<p>svm1</p>
<p>svm1_mgmt up/up 192.168.0.135/24 cluster1-01 e0c true</p>
<p><strong>svm1_nfs_01 up/up 192.168.0.132/24 cluster1-01 e0d true</strong></p>
<p>2 entries were displayed.</p>
<p>Note: one of these LIFs is the same IP address as configured on the BackendForNAS backend that Trident uses.</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Verify that the PV’s volume was created by listing svm1’s volumes:</p>
<p>volume show -vserver svm1</p>
<p>Sample output:</p>
<p>Vserver Volume Aggregate State Type Size Available Used%</p>
<p>--------- ------------ ------------ ---------- ---- ---------- ---------- -----</p>
<p>svm1 registry aggr1 online RW 20GB 18.93GB 0%</p>
<p>svm1 svm1_root aggr1 online RW 20MB 17.45MB 8%</p>
<p><strong>svm1 trident_pvc_9c9ef0a2_fb85_4960_aef9_830e2d5bb436</strong></p>
<p><strong>aggr1 online RW 1GB 1023MB 0%</strong></p>
<p>svm1 vol_import_manage</p>
<p>aggr1 online RW 2GB 1.90GB 0%</p>
<p>svm1 vol_import_nomanage</p>
<p>aggr1 online RW 2GB 1.90GB 0%</p>
<p>svm1 www aggr1 online RW 5GB 4.75GB 0%</p>
<p>6 entries were displayed.</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>List the aggregates assigned to svm1:</p>
<p>vserver show -vserver svm1 -fields aggr-list</p>
<p>Sample output:</p>
<p>vserver aggr-list</p>
<p>------- -----------</p>
<p>svm1 aggr1,aggr2</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><p>Display the namespace junction paths for these volumes:</p>
<p>volume show -vserver svm1 -junction</p>
<p>Sample output:</p>
<p>Junction Junction</p>
<p>Vserver Volume Language Active Junction Path Path Source</p>
<p>--------- ------------ -------- -------- ------------------------- -----------</p>
<p>svm1 registry C.UTF-8 true /registry RW_volume</p>
<p>svm1 svm1_root C.UTF-8 true / -</p>
<p><strong>svm1 trident_pvc_9c9ef0a2_fb85_4960_aef9_830e2d5bb436</strong></p>
<p><strong>C.UTF-8 true /trident_pvc_9c9ef0a2_ RW_volume</strong></p>
<p><strong>fb85_4960_aef9_</strong></p>
<p><strong>830e2d5bb436</strong></p>
<p>svm1 vol_import_manage</p>
<p>C.UTF-8 true /vol_import_manage RW_volume</p>
<p>svm1 vol_import_nomanage</p>
<p>C.UTF-8 true /vol_import_nomanage RW_volume</p>
<p>svm1 www C.UTF-8 true /www RW_volume</p>
<p>6 entries were displayed.</p></td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>In your PuTTY session to rhel3, instantiate a pod that will request a persistent-volume-claim-nas PVC. To perform this task you must use a pod definition file (in YAML format) to create the desired container and to claim the sort of PVC to attach. Once again, this exercise includes a pre-configured pod definition file for that purpose. Examine the contents of that file:</p>
<p>cat pv-alpine.yaml</p>
<p>Sample output:</p>
<p>kind: Pod</p>
<p>apiVersion: v1</p>
<p>metadata:</p>
<p>name: pvpod</p>
<p>spec:</p>
<p>volumes:</p>
<p>- name: task-pv-storage</p>
<p>persistentVolumeClaim:</p>
<p>claimName: persistent-volume-claim-nas</p>
<p>containers:</p>
<p>- name: task-pv-container</p>
<p>image: alpine:3.2</p>
<p>command:</p>
<p>- /bin/sh</p>
<p>- "-c"</p>
<p>- "sleep 60m"</p>
<p>volumeMounts:</p>
<p>- mountPath: "/data"</p>
<p>name: task-pv-storage</p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td><p>Create the pod:</p>
<p>kubectl create -f pv-alpine.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>Display the status of this new pod:</p>
<p>kubectl get pod pvpod</p></td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td><p>Launch a CLI session inside the pvpod container:</p>
<p>kubectl exec -it pvpod /bin/sh</p>
<p>Sample output:</p>
<p>/ #</p></td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td><p>Display a list of the mounted volumes in the pod to verify that the PV is attached. In this example you are specifically looking for the volume mounted on /data:</p>
<p>/ # mount | grep /data</p>
<p>Sample output:</p>
<p>192.168.0.132:/trident_pvc_9c9ef0a2_fb85_4960_aef9_830e2d5bb436 on /data …</p>
<p>In this example the volume is NFS mounted onto /data from 192.168.0.132:/</p>
<p>trident_pvc_15aa89c0_ba81_4c9f_b106_8d342802120a.</p></td>
</tr>
<tr class="even">
<td><ol start="18" type="1">
<li></li>
</ol></td>
<td><p>Create a file on the volume and then log out of the pod:</p>
<p>/ # echo "THIS IS A TEST FILE" &gt; /data/testfile.txt</p>
<p>/ # exit</p></td>
</tr>
<tr class="odd">
<td><ol start="19" type="1">
<li></li>
</ol></td>
<td><p>Destroy the pod pvpod:</p>
<p>kubectl delete pod pvpod</p>
<p><strong>Note:</strong> The pod will take 30-60 seconds to delete.</p></td>
</tr>
<tr class="even">
<td><ol start="20" type="1">
<li></li>
</ol></td>
<td><p>List the PVCs:</p>
<p>kubectl get pvc</p>
<p>Even though you deleted the pod, your PVC is still present.</p></td>
</tr>
<tr class="odd">
<td><ol start="21" type="1">
<li></li>
</ol></td>
<td><p>Re-provision the pod:</p>
<p>kubectl create -f pv-alpine.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="22" type="1">
<li></li>
</ol></td>
<td><p>Make sure the pod is running:</p>
<p>kubectl get pod pvpod</p></td>
</tr>
<tr class="odd">
<td><ol start="23" type="1">
<li></li>
</ol></td>
<td><p>Verify that the new pod connected to your existing PVC:</p>
<p>kubectl exec -it pvpod /bin/sh</p></td>
</tr>
<tr class="even">
<td><ol start="24" type="1">
<li></li>
</ol></td>
<td><p>Verify the data folder still exists:</p>
<p>/ # mount | grep /data</p>
<p>Sample output:</p>
<p>192.168.0.132:/trident_pvc_9c9ef0a2_fb85_4960_aef9_830e2d5bb436 on /data</p></td>
</tr>
<tr class="odd">
<td><ol start="25" type="1">
<li></li>
</ol></td>
<td><p>Verify that the test file you created earlier is accessible:</p>
<p>/ # cat /data/testfile.txt</p>
<p>/ # exit</p>
<p>As you can see, so long as you do not delete the PVC, you can delete and recreate the pod and it will re-attach to the PVC that still retains your data.</p></td>
</tr>
<tr class="even">
<td><ol start="26" type="1">
<li></li>
</ol></td>
<td><p>Delete the pod and the PVC:</p>
<p>kubectl delete pod pvpod</p>
<p>kubectl delete pvc persistent-volume-claim-nas</p>
<p>When you deleted the PVC, the associated PV was deleted as well.</p></td>
</tr>
<tr class="odd">
<td><ol start="27" type="1">
<li></li>
</ol></td>
<td><p>In your PuTTY session to cluster1, verify that the ONTAP volume that backed the PV is gone as well:</p>
<p>volume show -vserver svm1 -junction</p>
<p>Sample output:</p>
<p>Junction Junction</p>
<p>Vserver Volume Language Active Junction Path Path Source</p>
<p>--------- ------------ -------- -------- ------------------------- -----------</p>
<p>svm1 registry C.UTF-8 true /registry RW_volume</p>
<p>svm1 svm1_root C.UTF-8 true / -</p>
<p>svm1 vol_import_manage</p>
<p>C.UTF-8 true /vol_import_manage RW_volume</p>
<p>svm1 vol_import_nomanage</p>
<p>C.UTF-8 true /vol_import_nomanage RW_volume</p>
<p>svm1 www C.UTF-8 true /www RW_volume</p>
<p>5 entries were displayed.</p>
<p>The trident_pvc volume you saw here in step 2-12 is now gone.</p></td>
</tr>
</tbody>
</table>

## Task 3: Create a pErsistent Volume Snapshot on ONTAP with Trident

A snapshot is a point-in-time copy of a volume which can be used to
provision new volumes or restore volume state. Trident enables
Kubernetes users to easily create snapshots and create new volumes from
those snapshots. In this exercise, you will use Trident to create a
snapshot on a persistent volume. You will accomplish this by creating
the persistent volume that you want to snapshot, then create a
VolumeSnapshotClass, which defines a class of storage to support
provisioning a snaphot, and finally you will issue a VolumeSnapshot
request that uses that VolumeSnapshotClass to create a snapshot on the
persistent volume.

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
<td><p>Examine the YAML file you will use to create a PVC and pod for this exercise:</p>
<p>cat pvcnas-alpine-pod.yaml</p>
<p>Notice that this is a single YAML file with multiple object definitions (PersistentVolumeClaim and Pod).</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Create the persistent-volume-claim-nas PVC and the pvpod pod:</p>
<p>kubectl create -f pvcnas-alpine-pod.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Verify the PVC and the existing pod:</p>
<p>kubectl get pod</p>
<p>kubectl get pvc</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Connect to the pod and create a test file on the PV:</p>
<p>kubectl exec -it pvpod /bin/sh</p>
<p>/ #</p>
<p>/ # echo "My test file" &gt; /data/file1.txt</p>
<p>/ # exit</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>In order to create a snapshot, Kubernetes requires a VolumeSnapshotClass object. Examine the YAML file you will be using to create a VolumeSnapshotClass:</p>
<p>cat snap-sc.yaml</p>
<p>Sample output:</p>
<p>apiVersion: snapshot.storage.k8s.io/v1alpha1</p>
<p>kind: VolumeSnapshotClass</p>
<p>metadata:</p>
<p>name: csi-snapclass</p>
<p>snapshotter: csi.trident.netapp.io</p>
<p>A VolumeSnapshotClass object is similar to a StorageClass object. Just as StorageClass provides an abstract definition of storage for provisioning a volume, VolumeSnapshotClass provides an abstract definition of storage for provisioning a volume snapshot.</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Create the VolumeSnapshotClass:</p>
<p>kubectl create -f snap-sc.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Verify that VolumeSnapshotClass was created:</p>
<p>kubectl get volumesnapshotclass</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Inspect the details of the csi-snapclass VolumeSnapshotClass:</p>
<p>kubectl describe volumesnapshotclass csi-snapclass</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Examine the YAML file you will use to request the creation of a volume snapshot:</p>
<p>cat snap.yaml</p>
<p>Sample output:</p>
<p>apiVersion: snapshot.storage.k8s.io/v1alpha1</p>
<p>kind: VolumeSnapshot</p>
<p>metadata:</p>
<p>name: pvc1-snap</p>
<p>spec:</p>
<p>snapshotClassName: csi-snapclass</p>
<p>source:</p>
<p>name: persistent-volume-claim-nas</p>
<p>kind: PersistentVolumeClaim</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Create the VolumeSnaphot:</p>
<p>kubectl create -f snap.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Verify that the VolumeSnapshot object was created:</p>
<p>kubectl get volumesnapshots</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><p>Inspect the VolumeSnapshot object:</p>
<p>kubectl describe volumesnapshot pvc1-snap</p></td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>In your PuTTY session to cluster1, list the volumes on svm1:</p>
<p>volume show -vserver svm1</p>
<p>Sample output:</p>
<p>Vserver Volume Aggregate State Type Size Available Used%</p>
<p>--------- ------------ ------------ ---------- ---- ---------- ---------- -----</p>
<p>svm1 registry aggr1 online RW 20GB 18.93GB 0%</p>
<p>svm1 svm1_root aggr1 online RW 20MB 17.33MB 8%</p>
<p><strong>svm1 trident_pvc_a1b6154f_2f72_4710_b77c_41c3dc5db034</strong></p>
<p><strong>aggr1 online RW 1GB 1023MB 0%</strong></p>
<p>svm1 vol_import_manage</p>
<p>aggr1 online RW 2GB 1.90GB 0%</p>
<p>svm1 vol_import_nomanage</p>
<p>aggr1 online RW 2GB 1.90GB 0%</p>
<p>svm1 www aggr1 online RW 5GB 4.75GB 0%</p>
<p>6 entries were displayed.</p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td><p>Still on cluster1, list the snapshots for the new volume:</p>
<p>volume snapshot show -volume &lt;trident volume name&gt;</p>
<p>Sample output:</p>
<p>---Blocks---</p>
<p>Vserver Volume Snapshot Size Total% Used%</p>
<p>-------- -------- ------------------------------------- -------- ------ -----</p>
<p>svm1 trident_pvc_a1b6154f_2f72_4710_b77c_41c3dc5db034</p>
<p>snapshot-eed7b43c-ba50-44f2-accf-51785506aaee</p>
<p>156KB 0% 36%</p></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>In your PuTTY session to rhel3, delete the pvpod pod. Remember, the delete operation will take 30-60 seconds:</p>
<p>kubectl delete pod pvpod</p></td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td><p>Delete the PVC on which you created the snapshot:</p>
<p>kubectl delete pvc persistent-volume-claim-nas</p>
<p>Kubernetes is no longer aware of the PVC.</p></td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td><p>Verify that no PVs are available:</p>
<p>kubectl get pv</p></td>
</tr>
<tr class="even">
<td><ol start="18" type="1">
<li></li>
</ol></td>
<td><p>In your PuTTY session to cluster 1, list the volumes on svm1:</p>
<p>cluster1::&gt; volume show -vserver svm1</p>
<p>cluster1::&gt; volume snapshot show -volume &lt;trident volume name&gt;</p></td>
</tr>
<tr class="odd">
<td><ol start="19" type="1">
<li></li>
</ol></td>
<td><p>In your PuTTY session to rhel3, ask Kubernetes to list the VolumeSnapshots:</p>
<p>kubectl get volumesnapshots</p></td>
</tr>
<tr class="even">
<td><ol start="20" type="1">
<li></li>
</ol></td>
<td><p>List the details of the pvc-snap1 VolumeSnapshot:</p>
<p>kubectl describe volumesnapshot pvc1-snap</p></td>
</tr>
<tr class="odd">
<td><ol start="21" type="1">
<li></li>
</ol></td>
<td><p>Ask Trident to list the snapshots it manages:</p>
<p>tridentctl get snapshot -n trident</p>
<p>Sample output:</p>
<p>+-----------------------------------------------+------------------------------------------+</p>
<p>| NAME | VOLUME |</p>
<p>+-----------------------------------------------+------------------------------------------+</p>
<p>| snapshot-eed7b43c-ba50-44f2-accf-51785506aaee | pvc-a1b6154f-2f72-4710-b77c-41c3dc5db034 |</p>
<p>+-----------------------------------------------+------------------------------------------+</p></td>
</tr>
<tr class="even">
<td><ol start="22" type="1">
<li></li>
</ol></td>
<td><p>Ask Trident to display more details for the known snapshots:</p>
<p>tridentctl get snapshot -n trident -o json</p></td>
</tr>
<tr class="odd">
<td><ol start="23" type="1">
<li></li>
</ol></td>
<td><p>Ask Trident to list the volumes it manages:</p>
<p>tridentctl get volume -n trident</p></td>
</tr>
<tr class="even">
<td><ol start="24" type="1">
<li></li>
</ol></td>
<td><p>Display more details for these volumes:</p>
<p>tridentctl get volume -n trident -o json</p></td>
</tr>
</tbody>
</table>

## Task 4: Create a Persistent Volume Claim 

In this exercise you will create a persistent volume claim from the
snapshot you created in the

preceding exercise. You therefore must have completed the Create a
Persistent Volume Snapshot on ONTAP with Trident exercise before you can
successfully proceed with this exercise.

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
<td><p>In your PuTTY session to rhel3, examine a YAML file for issuing a PVC to create a PV from a volume snapshot and to provision an alpine pod that uses it:</p>
<p>cat pod-alpine-clone.yaml</p>
<p>Sample output:</p>
<p>apiVersion: v1</p>
<p>kind: PersistentVolumeClaim</p>
<p>metadata:</p>
<p>name: pvc-from-snap</p>
<p>spec:</p>
<p>accessModes:</p>
<p>- ReadWriteOnce</p>
<p>storageClassName: storage-class-nas</p>
<p>resources:</p>
<p>requests:</p>
<p>storage: 1Gi</p>
<p>dataSource:</p>
<p>name: pvc1-snap</p>
<p>kind: VolumeSnapshot</p>
<p>apiGroup: snapshot.storage.k8s.io</p>
<p>---</p>
<p>kind: Pod</p>
<p>apiVersion: v1</p>
<p>metadata:</p>
<p>name: pvpod-clone</p>
<p>spec:</p>
<p>volumes:</p>
<p>- name: task-pv-storage</p>
<p>persistentVolumeClaim:</p>
<p>claimName: pvc-from-snap</p>
<p>containers:</p>
<p>- name: task-pv-container</p>
<p>image: alpine:3.2</p>
<p>command:</p>
<p>- /bin/sh</p>
<p>- "-c"</p>
<p>- "sleep 60m"</p>
<p>volumeMounts:</p>
<p>- mountPath: "/data"</p>
<p>name: task-pv-storage</p>
<p>This YAML file defines a request to create a PVC named pvc-from-snap from the VolumeSnapshot object named pvc1-snap.</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Issue the PVC request:</p>
<p>kubectl create -f pod-alpine-clone.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Verify that the PVC and pod were created:</p>
<p>kubectl get pod</p>
<p>kubectl get pvc</p>
<p>The new cloned PVC is 1Gi, which is the same as the source PV’s 1Gi size.</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Inspect the details of the pvc-from-snap PVC:</p>
<p>kubectl describe pvc pvc-from-snap</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>In your PuTTY session to cluster1, verify the new volume was created on svm1:</p>
<p>volume show -vserver svm1</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Connect to the pod and verify that the /data/file1.txt file that you created on the source PV is present on the clone PV as well:</p>
<p>kubectl exec -it pvpod-clone /bin/sh</p>
<p>/ # cat /data/file1.txt</p>
<p>/ # exit</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>In your PuTTY session to rhel3, delete the pvpod-clone pod and pvc-from-snap PVC, which will in turn delete the associated PV:</p>
<p>kubectl delete pod pvpod-clone</p>
<p>kubectl delete pvc pvc-from-snap</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Delete the VolumeSnapshot you used as the source for creating the clone volume PVC:</p>
<p>kubectl delete volumesnapshot pvc1-snap</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Verify that Trident deleted the snapshot and finished deleting the source volume:</p>
<p>tridentctl get snapshot -n trident</p>
<p>tridentctl get volume -n trident</p>
<p>Once you deleted the snapshot, there was no longer anything preventing the pending delete operation on the associated volume from continuing, and so Trident deleted that volume as well.</p></td>
</tr>
</tbody>
</table>

## Task 5: resize an nfs Persistent Volume using Trident 

Trident supports the ability to expand existing NFS persistent volumes.
You can expand NFS persistent volumes even if the PVC is currently
attached to a Pod.

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
<td><p>Create a new persistent-volume-claim-nas PVC:</p>
<p>kubectl create -f pvcfornas.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>List the PVCs that exist in the kubernetes cluster:</p>
<p>kubectl get pvc</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Enter the following command to begin editing the persistent-volume-claim-nas PVC:</p>
<p>kubectl edit pvc persistent-volume-claim-nas</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>In the editor, alter the PVC’s size to expand it to 2 Gi by using the following procedure:</p>
<ol type="a">
<li><p>Use the arrow keys to navigate to down to the spec.resources.storage line (under the spec section, then the resources subsection), then navigate to the right to position the cursor on top of the “1” digit.</p></li>
<li><p>Enter the key sequence <strong>r2</strong> key, which will change the value under the cursor from 1 to 2. After this operation, the editor’s contents should look like the gray text box that follows step c.</p></li>
<li><p>Enter the key sequence <strong>:wq</strong> which will save the change and close the editor window.</p></li>
</ol>
<p>Sample output:</p>
<p># Please edit the object below. Lines beginning with a '#' will be ignored,</p>
<p># and an empty file will abort the edit. If an error occurs while saving this file will be</p>
<p># reopened with the relevant failures.</p>
<p>#</p>
<p>apiVersion: v1</p>
<p>kind: PersistentVolumeClaim</p>
<p>…</p>
<p>spec:</p>
<p>accessModes:</p>
<p>- ReadWriteOnce</p>
<p>resources:</p>
<p>requests:</p>
<p>storage: <strong>1</strong>Gi</p>
<p>storageClassName: storage-class-nas</p>
<p>volumeMode: Filesystem</p>
<p>volumeName: pvc-b8441452-fbf3-41b5-a011-e472443732d9</p>
<p>status:</p>
<p>accessModes:</p>
<p>- ReadWriteOnce</p>
<p>capacity:</p>
<p>storage: 1Gi</p>
<p>phase: Bound</p>
<p>After the editor closes, verify that the update was successful.</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Verify that the volume has resized:</p>
<p>kubectl get pvc</p>
<p>The persistent-volume-claim-nas PVC now reports a capacity of 2Gi.</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>In your PuTTY session to cluster1, examine the size of the volume on svm1 by looking for the volume whose name contains the same UID value you identified in the preceding step:</p>
<p>volume show</p>
<p>Observe that the trident_pvc volume’s capacity is now set to 2 GB.</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>In your PuTTY session to rhel3, clean up from this exercise by deleting the persistent-volume-claim-nas PVC:</p>
<p>kubectl delete pvc persistent-volume-claim-nas</p></td>
</tr>
</tbody>
</table>

End of Exercise
