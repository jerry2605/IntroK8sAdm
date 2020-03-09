Module 9: Deploying Kubernetes using  
NetApp Kubernetes Service

## Objectives

This exercise focuses on enabling you to do the following:

  - > Launch the NKS Lab

  - > Install Kubernetes using NKS
    
    **NOTE: All the tasks in the following module are executed on
    different lab kit as used in the previous modules.**

## Task 1: Launch the NKS lab

In this task, you will log into the LOD portal and launch the NetApp
Kubernetes Service.

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
<p><a href="https://naulabportal.netapp.com/">https://naulabportal.netapp.com/</a></p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>At the login prompt, enter the credentials provided to you by your instructor:</p>
<p><strong>Login ID</strong>: bXXXXu[YOUR KIT]</p>
<p><strong>Password</strong>: ask the instructor</p>
<p><img src="./media/image1.png" style="width:5.88264in;height:3.40417in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td>Select the Lab called “<strong>Getting Started with NKS on NetApp HCI</strong>” and connect to it.</td>
</tr>
</tbody>
</table>

## Task 2: Install kubernetes using NKS

In this task, you will log into the LOD portal and launch the NetApp
Kubernetes Service.

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
<td><p>To sign into NetApp HCI, launch Firefox and go to: <a href="https://192.168.0.100/hcc/login">https://192.168.0.100/hcc/login</a> and enter the HCI storage credentials provided by your instructor and click “Sign In”:</p>
<p><img src="./media/image2.png" style="width:4.84375in;height:5.0625in" /></p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td>Before you configure HCI for NetApp Cloud Services, take a moment to verify the management node is running the latest release. Click <strong>Upgrade</strong>. <img src="./media/image3.png" style="width:5.88264in;height:2.55694in" /></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td>Proceed to upgrade to the newest release provided. Click <strong>Begin Upgrade</strong> if the button is available. If not, continue to the next step. <img src="./media/image4.png" style="width:5.88264in;height:2.64931in" /></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Configure HCI to be a provisioning target for NKS. Click the cloud icon. Select Enable Cloud Services.</p>
<p><img src="./media/image5.png" style="width:5.88264in;height:2.27292in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Select NetApp Kubernetes Service (NKS). Click Continue.</p>
<p><img src="./media/image6.png" style="width:5.88264in;height:2.73333in" /></p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td>Next, you will need a NKS API Token. This token allows the NKS service portal to authenticate with your HCI management node. You will use a unique demonstration ID to register with the NKS service. To retrieve your unique ID and open the Cloud Central sign-up page, click the lock icon on the taskbar. <img src="./media/image7.png" style="width:5.88264in;height:3.33333in" /></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>A new browser tab will open with the NetApp Cloud Central Sign-up URL loaded and a Notepad window will open a few seconds later with the unique ID. Copy the email address from the Notepad window. Paste the email address into the "Email" field on the form. Fill in the following for the remaining fields:</p>
<p>• Password: Netapp1!</p>
<p>• Company: NetApp</p>
<p>• Full Name: NetApp</p>
<p>Check the box to accept the terms and conditions. Click SIGN UP.<img src="./media/image8.png" style="width:5.88264in;height:3.95in" /></p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Under the Kubernetes Service, click Start Free Trial.</p>
<p><img src="./media/image9.png" style="width:5.88264in;height:2.84375in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td>Click TAKE ME TO CLASSIC NKS to continue. <img src="./media/image10.png" style="width:5.88264in;height:3.48264in" /></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Click on the icon shown and select Edit Profile to create an API token for NKS.</p>
<p><img src="./media/image11.png" style="width:5.88264in;height:2.65625in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Scroll down to the API Tokens section and click ADD TOKEN</p>
<p><img src="./media/image12.png" style="width:5.88264in;height:3.50903in" /></p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><p>Name the token HCI, and click CREATE</p>
<p><img src="./media/image13.png" style="width:4.86458in;height:3.14583in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>This token cannot be retrieved after leaving this page. Highlight and copy the token to the clipboard.</p>
<p><img src="./media/image14.png" style="width:5.88264in;height:3.05903in" /></p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td>Switch to the Firefox tab for “NetApp Hybrid Cloud Control”. Paste the NKS API key into the field and click Connect. <img src="./media/image15.png" style="width:5.88264in;height:4.5in" /></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>Select the organization to register this HCI environment with. The environment name is automatically generated when NKS is launched for the first time and can be modified from the NKS interface. For this step, select the random-generated organization name from this menu. Enter “demo” for the region name. Click Continue.</p>
<p><img src="./media/image16.png" style="width:5.88264in;height:4.02292in" /></p></td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td><p>Now NKS is communicating with vCenter. Select the vSphere Datacenter and Cluster that NKS will deploy with. Select the following menu options:</p>
<p>• Datacenter: NetApp-HCI-Datacenter.</p>
<p>• Cluster: NetApp-HCI-Cluster-01.</p>
<p>Click Continue.</p>
<p><img src="./media/image17.png" style="width:5.81875in;height:4.57222in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td><p>For the network settings, use the following:</p>
<p>• Management Network Port Group: VM_Network</p>
<p>• NetApp Kubernetes Service Workload Network: VM_Network</p>
<p>• NetApp Kubernetes Service Data Network: iSCSI-A</p>
<p>Click Continue.</p>
<p><img src="./media/image18.png" style="width:5.81875in;height:4.45903in" /></p></td>
</tr>
<tr class="even">
<td><ol start="18" type="1">
<li></li>
</ol></td>
<td><p>Click Enable Services in the NKS wizard.</p>
<p><img src="./media/image19.png" style="width:5.81875in;height:4.55972in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="19" type="1">
<li></li>
</ol></td>
<td><p>In about 30 minutes, you will have a Kubernetes management cluster running on the HCI system. Click Close.</p>
<p><img src="./media/image20.png" style="width:5.81875in;height:3.30486in" /></p></td>
</tr>
</tbody>
</table>

End of Exercise
