![](M0_Check_Media/media/image1.jpeg)  
  
  
  
  
NETApp University

Introduction to Kubernetes Administration

Lab Guide

Course ID: STRSW-ILT-NKSADM

Catalog Number: TBD

Attention

The information contained in this course is intended only for training.
This course contains information and activities that, while beneficial
for the purposes of training in a closed, non-production environment,
can result in downtime or other severe consequences in a production
environment. This course material is not a technical reference and
should not, under any circumstances, be used in production environments.
To obtain reference materials, refer to the NetApp product documentation
that is located at <http://mysupport.netapp.com/>.

copyright

© 2020 NetApp, Inc. All rights reserved. Printed in the U.S.A.
Specifications subject to change without notice.

No part of this document covered by copyright may be reproduced in any
form or by any means—graphic, electronic, or mechanical, including
photocopying, recording, taping, or storage in an electronic retrieval
system—without prior written permission of NetApp, Inc.

U.S. Government rights

Commercial Computer Software. Government users are subject to the
NetApp, Inc. standard license agreement and applicable provisions of the
FAR and its supplements.

Trademark information

NETAPP, the NETAPP logo, and the marks listed at
<http://www.netapp.com/TM> are trademarks of NetApp, Inc. Other company
and product names may be trademarks of their respective owners.

Table of Contents

INTRODUCTION E0

Module 1 E1

Module 2 E2

Module 3 E3

Module 4 E4

Module 5 E5

Module 6 E6

Module 7 E7

Module 8 E8

Module 9 E9

Module 0: Checking Lab equipment

## Study Aid Icons

In your exercises, you might see one or more of the following icons.

<table>
<thead>
<tr class="header">
<th><img src="M0_Check_Media/media/image2.png" style="width:0.55208in;height:0.55208in" /></th>
<th><p><strong>Warning</strong></p>
<p>If you misconfigure a step marked with this icon, later steps might not work properly. Check the step carefully before you move forward.</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><img src="M0_Check_Media/media/image3.png" style="width:0.55208in;height:0.55208in" /></td>
<td><p><strong>Attention</strong></p>
<p>Review this step or comment carefully to save time, learn a best practice, or avoid errors.</p></td>
</tr>
<tr class="even">
<td><img src="M0_Check_Media/media/image4.png" style="width:0.55208in;height:0.55208in" alt="A close up of a logo Description automatically generated" /></td>
<td><p><strong>Information</strong></p>
<p>A comment labeled with this icon provides information about the topic or procedure.</p></td>
</tr>
<tr class="odd">
<td><img src="M0_Check_Media/media/image5.png" style="width:0.55208in;height:0.55208in" /></td>
<td><p><strong>References</strong></p>
<p>A comment labeled with this icon identifies reference material that provides additional information.</p></td>
</tr>
</tbody>
</table>

## Checking the Lab Equipment

In this exercise, you familiarize yourself with your equipment and
ensure that the credentials provided by the instructor are operational.

## Objectives

This exercise focuses on enabling you to do the following:

  - > Ensure access to your jump host

  - > Ensure connectivity to an ONTAP cluster

  - > Ensure connectivity to the Element Cluster

  - > Test the access to the Linux VMs and verify that all tools are
    > running properly

## GITHUB Containing the YAML files 

You can download or clone the Github public repositor containing the
necessary YAML files for the labs:
<https://github.com/netapp-devops/course>

## Exercise Equipment Diagram 

Each Lab Kit contains the following Labs:

  - **Modules 1 to 8**: Using Trident with Kubernetes and ONTAP 

  - **Module 9**: Getting Started with NKS on NetApp HCI

  - **Optional**: Use Cloud Manager to Move Kubernetes Application
    Workloads Between Hybrid-Cloud Endpoints

## 

**IMPORTANT: SELECT THE LAB Using Trident with Kubernetes and ONTAP **

## ![A screenshot of a cell phone Description automatically generated](M0_Check_Media/media/image6.png)

## ![A screenshot of a cell phone Description automatically generated](M0_Check_Media/media/image7.png)![A screenshot of a cell phone Description automatically generated](M0_Check_Media/media/image8.png)

##   

## Task 1: Ensure Connectivity to Your ONTAP AND ELEMENT ClusterS

In this task, you familiarize yourself with the Lab on Demand kit
provided by your instructor. You ensure connectivity to the ONTAP
cluster and verify the health of the ONTAP cluster.

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
<td><p>Open a browser and visit the following Website:</p>
<p><a href="https://lod-bootcamp.netapp.com/">https://lod-bootcamp.netapp.com</a></p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>At the login prompt, enter the credentials provided to you by your instructor:</p>
<p><strong>Login ID</strong>: bXXXXu[YOUR KIT]</p>
<p><strong>Password</strong>: ask the instructor</p>
<p><img src="M0_Check_Media/media/image9.png" style="width:5.88264in;height:3.40417in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td>Select the Lab called “<strong>Using Trident with Kubernetes and ONTAP</strong>” and connect to it.</td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td>Once you see the Windows Desktop (your jump host), verify that you have a file called “Stateful Cmds.txt” on the Desktop.</td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td>Make sure your keyboard is set to the correct layout.</td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Open the C:/LOD folder and make sure you see the config folder:</p>
<p><img src="M0_Check_Media/media/image10.png" style="width:5.88264in;height:2.67778in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>On the menu bar, click the Putty icon and verify that you can see 6 RHEL hosts:</p>
<p><img src="M0_Check_Media/media/image11.png" style="width:5.2in;height:5.11897in" /></p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>In Putty, double click the “cluster1” session and login to the ONTAP cluster:</p>
<p>Username: admin</p>
<p>Password: Netapp1!</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td>Enter the command “date” and verify that the time is synchronized.</td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td>Open a web browser and navigate to cluster1.demo.netapp.com.</td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Login using the credentials in step 1-8 and navigate your cluster:</p>
<p><img src="M0_Check_Media/media/image12.png" style="width:3.82857in;height:3.37977in" /></p></td>
</tr>
</tbody>
</table>

## 

## Task 2: Verify That Required SOFTWARE AND TOOLS ARE Installed

The RHEL hosts must have several tools running, including Docker and
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
<td><p>Open the Putty Client and double click on the rhel5 session:</p>
<p><img src="M0_Check_Media/media/image11.png" style="width:4.83333in;height:4.75802in" /></p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Login using the credentials:</p>
<p>Username: root</p>
<p>Password: Netapp1!</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Run the following command to verify that the docker daemon is running:</p>
<p><strong>ps -aef | grep dockerd</strong></p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Verify if containers are running:</p>
<p><strong>docker ps</strong></p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Verify the connection to the public Docker repositories:</p>
<p><strong>docker search netapp</strong></p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Run a Docker container and destroy it:</p>
<p><strong>docker run -it debian</strong></p>
<p><strong>ls -al</strong></p>
<p><strong>exit</strong></p>
<p><strong>docker ps</strong></p>
<p><strong>docker ps -a</strong> (to list stopped containers)</p>
<p><strong>docker rm $(docker ps -a -q)</strong> (to delete ALL stopped containers)</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Verify that the docker0 interface is configured on the host:</p>
<p><strong>ifconfig docker0</strong></p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Optionally, create an alias for the docker command (makes recurrent commands faster):</p>
<p><strong>alias d=’docker’</strong></p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Close the putty session and open a new one on rhel3 (master node of the Kubernetes cluster):</p>
<p><img src="M0_Check_Media/media/image11.png" style="width:4.51042in;height:4.44013in" /></p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Login using the credentials:</p>
<p>Username: <strong>root</strong></p>
<p>Password: <strong>Netapp1!</strong></p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Verify that the Kubernetes services are running:</p>
<p><strong>ps -aux | grep kubernetes</strong></p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><p>Optionally, create an alias for the most utilized Kubernetes command:</p>
<p><strong>alias k=’kubectl’</strong></p>
<p><span class="underline">Note: If you use the alias, you can replace all the upcoming “kubectl” commands with “k”.</span></p></td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>Verify the version of Kubernetes:</p>
<p><strong>k version</strong></p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td><p>Verify the node type you connected to:</p>
<p><strong>k cluster-info</strong></p></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>Verify that the Kubernetes cluster is showing you the 3 nodes:</p>
<p><strong>k get nodes</strong></p>
<p>Use the -o option to change the output (this option can be used in many commands):</p>
<p><strong>k get nodes -o wide</strong></p>
<p><strong>k get nodes -o json</strong></p></td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td><p>Label the worker nodes (with the role set to “none”) with a recognizable tag:</p>
<p><strong>k label node rhel1 node-role.kubernetes.io/worker=</strong></p>
<p><strong>k label node rhel2 node-role.kubernetes.io/worker=</strong></p></td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td><p>Verify that the Kubernetes nodes are showing the right labels:</p>
<p><strong>k get nodes</strong></p></td>
</tr>
<tr class="even">
<td><ol start="18" type="1">
<li></li>
</ol></td>
<td><p>Open the Kubernetes Configuration Files:</p>
<p><strong>cat $HOME/.kube/config</strong></p></td>
</tr>
<tr class="odd">
<td><ol start="19" type="1">
<li></li>
</ol></td>
<td><p>View the Kubernetes Configuration:</p>
<p><strong>k config view</strong></p></td>
</tr>
<tr class="even">
<td><ol start="20" type="1">
<li></li>
</ol></td>
<td><p>Navigate to the root user’s directory:</p>
<p><strong>cd /root</strong></p></td>
</tr>
<tr class="odd">
<td><ol start="21" type="1">
<li></li>
</ol></td>
<td><p>Clone the contents of the NetAppU DevOps git repository and browse the new folder:</p>
<p><strong>git clone https://github.com/netapp-devops/course/</strong></p>
<p><strong>cd course/</strong></p>
<p><strong>ls -al</strong></p></td>
</tr>
</tbody>
</table>

End of Exercise
