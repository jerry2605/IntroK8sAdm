Module 3: Pod Administration

## Objectives

This exercise focuses on enabling you to do the following:

  - > Deploy a pod to the Kubernetes cluster

  - > Create a single-container pod

  - > Create a multi-container pod

  - > Create a service

  - > Implement Secrets and ConfigMaps

## Task 1: Deploy a POD to the Kubernetes Cluster 

This activity demonstrates how to deploy a pre-configured pod within the
Kubernetes cluster, and also how to delete a pod.

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
<td><p>Connect to RHEL3 via SSH using Putty or use the Windows Command Line Prompt. Review your Kubernetes cluster:</p>
<p>kubectl get nodes</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Get detailed information on your master node:</p>
<p>kubectl describe node rhel3</p>
<p><strong>The output includes information about the Kubernetes version, supporting pods, resource availability/utilization, assigned tags, and more.</strong></p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Display a list of the cluster's namespaces. Remember that namespaces are simply a way to administratively divide cluster resources among multiple users, as Kubernetes clusters are often a resource shared by multiple teams, projects, and users:</p>
<p>kubectl get namespaces</p>
<p>Pods without a specified namespace deploy and run in the “default” namespace.</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>List the namespaces for all the pods that are currently running on the cluster:</p>
<p>kubectl get pods --all-namespaces</p>
<p>The vast majority of the pods listed are associated with the “kube-system” namespace. These pods represent the services created by Kubernetes to support its own operation. The Trident pods in this lab all run in the “trident” namespace.</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Display a list of the pods in this cluster that are running in the "trident" namespace (specified by including the -n trident option in the kubectl command):</p>
<p>kubectl get pods -n trident</p>
<p>Trident has several pods running in this namespace. A system administrator chooses which namespace to assign to Trident at installation time, so in another environment Trident may utilize a different namespace.</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>List all the services from the default namespace, and then from the trident namespace:</p>
<p>kubectl get services</p>
<p>kubectl get services -n trident</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Navigate to the folder where you cloned the GitHub repository on the RHEL3 host:</p>
<p>cd ~/course/</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Check the contents of the alpine.yaml file:</p>
<p>cat alpine.yaml</p>
<p>The “namespace:” entry indicates that this configuration is for the pod's default namespace.</p>
<p>The file's single “container:” section specifies that this pod only runs a single container.</p>
<p>The “image:” entry indicates that container utilizes the alpine3:2 image. Alpine is a lightweight, security-optimized Linux distribution that is often used as a foundational container image for creating other containers.</p>
<p>The “command:” section indicates the command that is to be executed inside the container when it is started, which in this case is to simply to sleep for 60 minutes.</p>
<p>The “restartPolicy:” entry is set to “Always”. This means Kubernetes should automatically restart the container in the event something happens to it (for example, the process inside crashes).</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td>Note: When this lab guide needs to reference a specific entry in a YAML file, it uses the “.” character to distinguish the block (i.e., indentation) levels in the YAML output. For example, using the YAML file you examined in this step, “metadata.name” refers to the “name” property inside the “metadata” block.<img src="./media/image1.png" style="width:0.55208in;height:0.55208in" /></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Tell Kubernetes to launch a new pod from the file and verify the status:</p>
<p>kubectl create -f alpine.yaml</p>
<p>kubectl get pod alpine</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Review the detailed information about the newly created pod:</p>
<p>kubectl describe pod alpine</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><img src="./media/image2.png" style="width:0.55208in;height:0.55208in" />The describe command returns a substantial amount of information about the containers in the pod, including the internal IP address(es), the node(s) used for execution, and a log of events associated with the containers. If any errors occur during container instantiation, they will be listed in the “Events:” section of the output as well.</td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>Destroy the pod, and verify its deletion by periodically querying the pod status (<strong>Note</strong>: The “kubectl delete pod” command will take 30-60 seconds to complete):</p>
<p>kubectl delete pod alpine</p>
<p>kubectl get pods</p></td>
</tr>
</tbody>
</table>

## Task 2: Create a single-container pod 

In this task, you will familiarize yourself with the YAML syntax in
Kubernetes, create a YAML single-container definition file, and
instantiate the pod.

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
<td><img src="./media/image3.png" style="width:0.55208in;height:0.55208in" />The following tasks are not 100% guided, and you will have to look for specific information in order to create your own YAML files. We will create 3 different YAML files throughout the task. To search for a specific public container image, use the docker hub.</td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>First, let’s create a simple YAML file on your Windows machine, that creates a single container pod running a Web server:</p>
<ul>
<li><p>The container name should be nginx</p></li>
<li><p>The pod name should be nginx-pod</p></li>
<li><p>The image should be nginx:latest</p></li>
<li><p>The namespace should be the default one</p></li>
<li><p>Don’t forget the API Version you want to use</p></li>
</ul></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>This is a list of properties we can set for a container (the red ones are mandatory and must be defined):</p>
<ul>
<li><p><strong>name</strong></p></li>
<li><p><strong>image</strong></p></li>
<li><p>command</p></li>
<li><p>args</p></li>
<li><p>workingDir</p></li>
<li><p>ports</p></li>
<li><p>env</p></li>
<li><p>resources</p></li>
<li><p>volumeMounts</p></li>
<li><p>livenessProbe</p></li>
<li><p>readinessProbe</p></li>
<li><p>lifecycle</p></li>
<li><p>terminationMessagePath</p></li>
<li><p>imagePullPolicy</p></li>
<li><p>securityContext</p></li>
<li><p>stdin</p></li>
<li><p>stdinOnce</p></li>
<li><p>tty</p></li>
</ul></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Once theYAML file is completed (using VI or VIM and then SCP), save it to the RHEL3 host and run it to parse it for syntax errors, without applying changes:</p>
<p>kubectl create --dry-run=true --output=json -f <strong><span class="underline">yourfile.yaml</span></strong></p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>If you don’t receive any error message after the command above, you can send the file to the scheduler to create your container and pod:</p>
<p>kubectl create -f <strong><span class="underline">yourfile.yaml</span></strong></p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Verify that your pod is being created:</p>
<p>watch kubectl get pods</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Once created, you can check the detailed output using:</p>
<p>kubectl describe pod nginx-pod</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>And destroy it:</p>
<p>kubectl delete pod nginx-pod</p></td>
</tr>
</tbody>
</table>

## Task 3: Create a multi-container pod 

In this task, you will familiarize yourself with the YAML syntax in
Kubernetes, create a YAML multi-container definition file, and
instantiate the pod.

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
<td><p>Create a simple YAML file, that creates a multi-container pod running a Webserver and a Repository:</p>
<ul>
<li><p>The pod name should be multi</p></li>
<li><p>The Label should be set to specify the app label is “webapp”</p></li>
<li><p>The first container name should be backend (use the debian image)</p></li>
<li><p>The first container should have a volumeMount configure with the following specs:</p></li>
</ul>
<p>image: debian</p>
<p>volumeMounts:</p>
<p>- name: html</p>
<p>mountPath: /html</p>
<p>command: ["/bin/sh", "-c"]</p>
<p>args:</p>
<p>- while true; do</p>
<p>date &gt;&gt; /html/index.html;</p>
<p>sleep 1;</p>
<p>done</p>
<ul>
<li><p>The second container name should be frontend (use the nginx image)</p></li>
<li><p>The Properties of the second container should be as follows:</p></li>
</ul>
<p>image: nginx</p>
<p>volumeMounts:</p>
<p>- name: html</p>
<p>mountPath: /usr/share/nginx/html</p>
<ul>
<li><p>The namespace should be default</p></li>
<li><p>Don’t forget the API Version you want to use</p></li>
</ul>
<p>The result will give you something like that:</p>
<p><img src="./media/image4.png" style="width:5.1743in;height:2.67481in" /></p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Once you YAML file is completed (using VI or VIM and then SCP), save it to the RHEL3 host and run it to parse it for syntax errors, without applying changes:</p>
<p>kubectl create --dry-run=true --output=json -f <strong><span class="underline">yourfile.yaml</span></strong></p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>If you don’t receive any error message after the command above, you can send the file to the scheduler to create your container and pod:</p>
<p>kubectl create -f <strong><span class="underline">yourfile.yaml</span></strong></p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Verify that your pod is being created (pay attention to the READY column):</p>
<p>watch kubectl get pods</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Once created, you can check the detailed output using:</p>
<p>kubectl describe pod multi</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Check that the backend resource is accessible:</p>
<p>kubectl exec multi -c backend -- /bin/cat /html/index.html</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Check that the frontend resource is accessible:</p>
<p>kubectl exec multi -c frontend -- /bin/cat /usr/share/nginx/html/index.html</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Once your tests are successful, delete the multi-container pod:</p>
<p>kubectl delete pod multi</p></td>
</tr>
</tbody>
</table>

## Task 4: Create a service

In this task, you will familiarize yourself with the YAML syntax in
Kubernetes, create a YAML service definition file, and instantiate the
service.

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
<td><p>Finally, we will create a third YAML file that will expose a port from a pod outside of Kubernetes. This is called a service.</p>
<ul>
<li><p>The name of the service should be webapp</p></li>
<li><p>You should map TCP port 80 to port 9876 (name it unsecure)</p></li>
<li><p>You should also map TCP port 443 to port 9877 (nme it secure)</p></li>
<li><p>Use the label: webapp</p></li>
</ul></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Once you YAML file is completed (using VI or VIM and then SCP), save it to the RHEL3 host and run it to parse it for syntax errors, without applying changes:</p>
<p>kubectl create --dry-run=true --output=json -f <strong><span class="underline">yourfile.yaml</span></strong></p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>If you don’t receive any error message after the command above, you can send the file to the scheduler to create your container and pod:</p>
<p>kubectl create -f <strong><span class="underline">yourfile.yaml</span></strong></p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Check your service is visible:</p>
<p>kubectl get services</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Verify the service configuration:</p>
<p>kubectl describe services service</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Delete your service:</p>
<p>kubectl delete -f <strong><span class="underline">yourfile.yaml</span></strong></p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Verify that the deletion was successful:</p>
<p>kubectl get services</p></td>
</tr>
</tbody>
</table>

## Task 5: implement Secrets and ConfigMaps

To explore Secrets and ConfigMaps, we will use the following scenario:

You're running the [official MariaDB container
image](https://hub.docker.com/_/mariadb) in Kubernetes and must do some
configuration to get the container to run. The image requires an
environment variable to be set for **MYSQL\_ROOT\_PASSWORD**,

**MYSQL\_ALLOW\_EMPTY\_PASSWORD**, or

**MYSQL\_RANDOM\_ROOT\_PASSWORD** to initialize the database. It also
allows for extensions to the MySQL configuration file **my.cnf** by
placing custom config files in **/etc/mysql/conf.d**.

You could build a custom image, setting the environment variables and
copying the configuration files into it to create the container image.
However, it is considered best practice to create and use generic images
and add unique configuration to the containers through an external
method. This is a perfect use-case for ConfigMaps and Secrets. The
**MYSQL\_ROOT\_PASSWORD** can be set in a Secret and added to the
container as an environment variable, and the configuration files can be
stored in a ConfigMap and mounted into the container as a file on
startup.

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
<td><p>To create the Secret containing the <strong>MYSQL_ROOT_PASSWORD</strong>, choose a password and convert it to base64 (sample password NetAppRocks!):</p>
<p>$ echo -n 'NetAppRocks!' | base64</p>
<p>Sample output:</p>
<p>TmV0QXBwUm9ja3Mh</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Make a note of the encoded string. You need it to create the YAML file for the Secret:</p>
<p>apiVersion: v1</p>
<p>kind: Secret</p>
<p>metadata:</p>
<p>  name: mariadb-root-password</p>
<p>type: Opaque</p>
<p>data:</p>
<p>  password: TmV0QXBwUm9ja3Mh</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Save that file as <strong>mysql-secret.yaml</strong> and create the Secret in Kubernetes with the <strong>kubectl apply</strong> command:</p>
<p>kubectl apply -f mysql-secret.yaml</p>
<p>Sample output:</p>
<p>secret/mariadb-root-password created</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Now that you've created the Secret, use <strong>kubectl describe</strong> to see it:</p>
<p>kubectl describe secret mariadb-root-password</p>
<p>Sample output:</p>
<p>Name:         mariadb-root-password</p>
<p>Namespace:    secrets-and-configmaps</p>
<p>Labels:       &lt;none&gt;</p>
<p>Annotations:</p>
<p>Type:         Opaque</p>
<p>Data</p>
<p>====</p>
<p>password:  16 bytes</p>
<p>Note that the <strong>Data</strong> field contains the key you set in the YAML: <strong>password</strong>. The value assigned to that key is the password you created, but it is not shown in the output. Instead, the value's size is shown in its place, in this case, 16 bytes.</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>You can also use the <strong>kubectl edit secret &lt;secretname&gt;</strong> command to view and edit the Secret. If you edit the Secret, you'll see something like this:</p>
<p># Please edit the object below. Lines beginning with a '#' will be ignored,</p>
<p># and an empty file will abort the edit. If an error occurs while saving this file will be</p>
<p># reopened with the relevant failures.</p>
<p>#</p>
<p>apiVersion: v1</p>
<p>data:</p>
<p>password: TmV0QXBwUm9ja3Mh</p>
<p>kind: Secret</p>
<p>metadata:</p>
<p>annotations:</p>
<p>kubectl.kubernetes.io/last-applied-configuration: |</p>
<p>{"apiVersion":"v1","data":{"password":"TmV0QXBwUm9ja3Mh"},"kind":"Secret","metadata":{"annotations":{},"name":"mariadb-root-password","namespace":"secrets-and-configmaps"},"type":"Opaque"}</p>
<p>creationTimestamp: 2019-05-29T12:06:09Z</p>
<p>name: mariadb-root-password</p>
<p>namespace: secrets-and-configmaps</p>
<p>resourceVersion: "85154772"</p>
<p>selfLink: /api/v1/namespaces/secrets-and-configmaps/secrets/mariadb-root-password</p>
<p>uid: 2542dadb-820a-11e9-ae24-005056a1db05</p>
<p>type: Opaque</p>
<p>Again, the <strong>data</strong> field with the <strong>password</strong> key is visible, and this time you can see the base64-encoded Secret.</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>In this case, you will use the output format <strong>-o jsonpath=&lt;path&gt;</strong> to extract only the Secret value using a JSONPath template:</p>
<p>kubectl get secret mariadb-root-password -o jsonpath='{.data.password}'</p>
<p>Sample output:</p>
<p>TmV0QXBwUm9ja3Mh</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>It is easy to decode the Secret by extracting the value and piping it to base64:</p>
<p>kubectl get secret mariadb-root-password -o jsonpath='{.data.password}' | base64 --decode</p>
<p>Sample output:</p>
<p>NetAppRocks!</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Alternative: You can also create Secrets directly using the <strong>kubectl create secret</strong> command. The MariaDB image permits setting up a regular database user with a password by setting the <strong>MYSQL_USER</strong> and <strong>MYSQL_PASSWORD</strong> environment variables. A Secret can hold more than one key/value pair, so you can create a single Secret to hold both strings. As a bonus, by using <strong>kubectl create secret</strong>, you can let Kubernetes mess with base64 so that you don't have to:</p>
<p>kubectl create secret generic mariadb-user-creds \</p>
<p>      --from-literal=MYSQL_USER=kubeuser\</p>
<p>      --from-literal=MYSQL_PASSWORD=netapp-still-rocks</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td>Note the <strong>--from-literal</strong>, which sets the key name and the value all in one. You can pass as many <strong>--from-literal</strong> arguments as you need to create one or more key/value pairs in the Secret.</td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Validate that the username and password were created and stored correctly with the <strong>kubectl get secrets</strong> command:</p>
<p>kubectl get secret mariadb-user-creds -o jsonpath='{.data.MYSQL_USER}' | base64 --decode</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Validate that the password were created and stored correctly with the <strong>kubectl get secrets</strong> command:</p>
<p>kubectl get secret mariadb-user-creds -o jsonpath='{.data.MYSQL_PASSWORD}' | base64 --decode</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td>ConfigMaps are similar to Secrets. They can be created and shared in the containers in the same ways. The only big difference between them is the base64-encoding obfuscation. ConfigMaps are intended for non-sensitive data—configuration data—like config files and environment variables and are a great way to create customized running services from generic container images.</td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td>ConfigMaps can be created in the same ways as Secrets. You can write a YAML representation of the ConfigMap manually and load it into Kubernetes, or you can use the <strong>kubectl create configmap</strong> command to create it from the command line. The following example creates a ConfigMap using the latter method but, instead of passing literal strings (as with <strong>--from-literal=&lt;key&gt;=&lt;string&gt;</strong> in the Secret above), it creates a ConfigMap from an existing file—a MySQL config intended for <strong>/etc/mysql/conf.d</strong> in the container. This config file overrides the <strong>max_allowed_packet</strong> setting that MariaDB sets to 16M by default.</td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td><p>First, create a file named <strong>max_allowed_packet.cnf</strong> with the following content:</p>
<p>[mysqld]</p>
<p>max_allowed_packet = 64M</p>
<p>This will override the default setting in the <strong>my.cnf</strong> file and set <strong>max_allowed_packet</strong> to 64M.</p></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>Once the file is created, you can create a ConfigMap named <strong>mariadb-config</strong> using the <strong>kubectl create configmap</strong> command that contains the file:</p>
<p>kubectl create configmap mariadb-config --from-file=max_allowed_packet.cnf</p></td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td><p>First, validate that the ConfigMap was, indeed, created:</p>
<p>kubectl get configmap mariadb-config</p>
<p>Sample output:</p>
<p>NAME             DATA      AGE</p>
<p>mariadb-config   1         9m</p></td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td><p>The contents of the ConfigMap can be viewed with the <strong>kubectl describe</strong> command. Note that the full contents of the file are visible and that the key name is, in fact, the file name, <strong>max_allowed_packet.cnf</strong>:</p>
<p>kubectl describe cm mariadb-config</p>
<p>Sample output:</p>
<p>Name:         mariadb-config</p>
<p>Namespace:    secrets-and-configmaps</p>
<p>Labels:       &lt;none&gt;</p>
<p>Annotations:  &lt;none&gt;</p>
<p>Data</p>
<p>====</p>
<p>max_allowed_packet.cnf:</p>
<p>----</p>
<p>[mysqld]</p>
<p>max_allowed_packet = 64M</p>
<p>Events:  &lt;none&gt;</p>
<p>A ConfigMap can be edited live within Kubernetes with the <strong>kubectl edit</strong> command. Doing so will open a buffer with the default editor showing the contents of the ConfigMap as YAML. When changes are saved, they will immediately be live in Kubernetes. While not really the <em>best</em> practice, it can be handy for testing things in development.</p></td>
</tr>
<tr class="even">
<td><ol start="18" type="1">
<li></li>
</ol></td>
<td><p>Say you want a <strong>max_allowed_packet</strong> value of 32M instead of the default 16M or the 64M in the <strong>max_allowed_packet.cnf</strong> file. Use <strong>kubectl edit configmap mariadb-config</strong> to edit the value:</p>
<p>kubectl edit configmap mariadb-config</p>
<p>Sample output:</p>
<p># Please edit the object below. Lines beginning with a '#' will be ignored,</p>
<p># and an empty file will abort the edit. If an error occurs while saving this file will be</p>
<p># reopened with the relevant failures.</p>
<p>#</p>
<p>apiVersion: v1</p>
<p>data:</p>
<p>  max_allowed_packet.cnf: |</p>
<p>    [mysqld]</p>
<p>    max_allowed_packet = 32M</p>
<p>kind: ConfigMap</p>
<p>metadata:</p>
<p>  creationTimestamp: 2019-05-30T12:02:22Z</p>
<p>  name: mariadb-config</p>
<p>  namespace: secrets-and-configmaps</p>
<p>  resourceVersion: "85609912"</p>
<p>  selfLink: /api/v1/namespaces/secrets-and-configmaps/configmaps/mariadb-config</p>
<p>  uid: c83ccfae-82d2-11e9-832f-005056a1102f</p></td>
</tr>
<tr class="odd">
<td><ol start="19" type="1">
<li></li>
</ol></td>
<td><p>After saving the change, verify the data has been updated:</p>
<p>kubectl get configmap mariadb-config -o "jsonpath={.data['max_allowed_packet\.cnf']}"</p>
<p>Sample output:</p>
<p>[mysqld]</p>
<p>max_allowed_packet = 32M</p></td>
</tr>
<tr class="even">
<td><ol start="20" type="1">
<li></li>
</ol></td>
<td><p>Secrets and ConfigMaps can be mounted as environment variables or as files within a container. For the MariaDB container, you will need to mount the Secrets as environment variables and the ConfigMap as a file. First, though, you need to write a Deployment for MariaDB so that you have something to work with. Create a file named <strong>mariadb-deployment.yaml</strong> with the following:</p>
<p>apiVersion: apps/v1</p>
<p>kind: Deployment</p>
<p>metadata:</p>
<p>labels:</p>
<p>app: mariadb</p>
<p>name: mariadb-deployment</p>
<p>spec:</p>
<p>replicas: 1</p>
<p>selector:</p>
<p>matchLabels:</p>
<p>app: mariadb</p>
<p>template:</p>
<p>metadata:</p>
<p>labels:</p>
<p>app: mariadb</p>
<p>spec:</p>
<p>containers:</p>
<p>- name: mariadb</p>
<p>image: docker.io/mariadb:10.4</p>
<p>ports:</p>
<p>- containerPort: 3306</p>
<p>protocol: TCP</p>
<p>volumeMounts:</p>
<p>- mountPath: /var/lib/mysql</p>
<p>name: mariadb-volume-1</p>
<p>volumes:</p>
<p>- emptyDir: {}</p>
<p>name: mariadb-volume-1</p></td>
</tr>
<tr class="odd">
<td><ol start="21" type="1">
<li></li>
</ol></td>
<td><p>You have two Secrets that need to be added to the Deployment:</p>
<ol type="1">
<li><p><strong>mariadb-root-password</strong> (with one key/value pair)</p></li>
<li><p><strong>mariadb-user-creds</strong> (with two key/value pairs)</p></li>
</ol></td>
</tr>
<tr class="even">
<td><ol start="22" type="1">
<li></li>
</ol></td>
<td><p>For the <strong>mariadb-root-password</strong> Secret, specify the Secret and the key you want by adding an <strong>env</strong> list/array to the container spec in the Deployment and setting the environment variable value to the value of the key in your Secret. In this case, the list contains only a single entry, for the variable <strong>MYSQL_ROOT_PASSWORD</strong>:</p>
<p>env:</p>
<p>   - name: MYSQL_ROOT_PASSWORD</p>
<p>     valueFrom:</p>
<p>       secretKeyRef:</p>
<p>         name: mariadb-root-password</p>
<p>         key: password</p></td>
</tr>
<tr class="odd">
<td><ol start="23" type="1">
<li></li>
</ol></td>
<td><p>Note that the name of the object is the name of the environment variable that is added to the container. The <strong>valueFrom</strong> field defines <strong>secretKeyRef</strong> as the source from which the environment variable will be set; i.e., it will use the value from the <strong>password</strong> key in the <strong>mariadb-root-password</strong> Secret you set earlier.</p>
<p>Add this section to the definition for the <strong>mariadb</strong> container in the <strong>mariadb-deployment.yaml</strong> file. It should look something like this:</p>
<p>spec:</p>
<p>   containers:</p>
<p>   - name: mariadb</p>
<p>     image: docker.io/mariadb:10.4</p>
<p>     env:</p>
<p>       - name: MYSQL_ROOT_PASSWORD</p>
<p>         valueFrom:</p>
<p>           secretKeyRef:</p>
<p>             name: mariadb-root-password</p>
<p>             key: password</p>
<p>     ports:</p>
<p>     - containerPort: 3306</p>
<p>       protocol: TCP</p>
<p>     volumeMounts:</p>
<p>     - mountPath: /var/lib/mysql</p>
<p>       name: mariadb-volume-1</p>
<p>In this way, you have explicitly set the variable to the value of a specific key from your Secret. This method can also be used with ConfigMaps by using <strong>configMapRef</strong> instead of <strong>secretKeyRef</strong>.</p></td>
</tr>
<tr class="even">
<td><ol start="24" type="1">
<li></li>
</ol></td>
<td><p>You can also set environment variables from <em>all</em> key/value pairs in a Secret or ConfigMap to automatically use the key name as the environment variable name and the key's value as the environment variable's value. By using <strong>envFrom</strong> rather than <strong>env</strong> in the container spec, you can set the <strong>MYSQL_USER</strong> and <strong>MYSQL_PASSWORD</strong> from the <strong>mariadb-user-creds</strong> Secret you created earlier, all in one go:</p>
<p>envFrom:</p>
<p>- secretRef:</p>
<p>    name: mariadb-user-creds</p></td>
</tr>
<tr class="odd">
<td><ol start="25" type="1">
<li></li>
</ol></td>
<td><p><strong>envFrom</strong> is a list of sources for Kubernetes to take environment variables. Use <strong>secretRef</strong> again, this time to specify <strong>mariadb-user-creds</strong> as the source of the environment variables. That's it! All the keys and values in the Secret will be added as environment variables in the container.</p>
<p>The container spec should now look like this:</p>
<p>spec:</p>
<p>  containers:</p>
<p>  - name: mariadb</p>
<p>    image: docker.io/mariadb:10.4</p>
<p>    env:</p>
<p>      - name: MYSQL_ROOT_PASSWORD</p>
<p>        valueFrom:</p>
<p>          secretKeyRef:</p>
<p>            name: mariadb-root-password</p>
<p>            key: password</p>
<p>    envFrom:</p>
<p>    - secretRef:</p>
<p>        name: mariadb-user-creds</p>
<p>    ports:</p>
<p>    - containerPort: 3306</p>
<p>      protocol: TCP</p>
<p>    volumeMounts:</p>
<p>    - mountPath: /var/lib/mysql</p>
<p>      name: mariadb-volume-1</p></td>
</tr>
<tr class="even">
<td><ol start="26" type="1">
<li></li>
</ol></td>
<td><img src="./media/image1.png" style="width:0.55208in;height:0.55208in" /><em>Note:</em> You could have just added the <strong>mysql-root-password</strong> Secret to the <strong>envFrom</strong> list and let it be parsed as well, as long as the <strong>password</strong> key was named <strong>MYSQL_ROOT_PASSWORD</strong> instead. There is no way to manually specify the environment variable name with <strong>envFrom</strong> as with <strong>env</strong>.</td>
</tr>
<tr class="odd">
<td><ol start="27" type="1">
<li></li>
</ol></td>
<td><p>The <strong>mariadb-deployment.yaml</strong> already has a volume and volumeMount specified, an <strong>emptyDir</strong> (effectively a temporary or ephemeral) volume mounted to <strong>/var/lib/mysql</strong> to store the MariaDB data:</p>
<p>&lt;...&gt;</p>
<p>  volumeMounts:</p>
<p>  - mountPath: /var/lib/mysql</p>
<p>    name: mariadb-volume-1</p>
<p>&lt;...&gt;</p>
<p>volumes:</p>
<p>- emptyDir: {}</p>
<p>name: mariadb-volume-1</p>
<p>&lt;...&gt;</p></td>
</tr>
<tr class="even">
<td><ol start="28" type="1">
<li></li>
</ol></td>
<td><img src="./media/image1.png" style="width:0.55208in;height:0.55208in" /><em>Note:</em> This is not a production configuration. When the Pod restarts, the data in the <strong>emptyDir</strong> volume is lost. This is primarily used for development or when the contents of the volume don't need to be persistent.</td>
</tr>
<tr class="odd">
<td><ol start="29" type="1">
<li></li>
</ol></td>
<td><p>You can add your ConfigMap as a source by adding it to the volume list and then adding a volumeMount for it to the container definition:</p>
<p><em>&lt;...&gt;</em></p>
<p><em>volumeMounts:</em></p>
<p><em>- mountPath: /var/lib/mysql</em></p>
<p><em>name: mariadb-volume-1</em></p>
<p><em>- mountPath: /etc/mysql/conf.d</em></p>
<p><em>name: mariadb-config</em></p>
<p><em>&lt;...&gt;</em></p>
<p><em>volumes:</em></p>
<p><em>- emptyDir: {}</em></p>
<p><em>name: mariadb-volume-1</em></p>
<p><em>- configMap:</em></p>
<p><em>name: mariadb-config</em></p>
<p><em>items:</em></p>
<p><em>- key: max_allowed_packet.cnf</em></p>
<p><em>path: max_allowed_packet.cnf</em></p>
<p><em>name: mariadb-config-volume</em></p>
<p><em>&lt;...&gt;</em></p></td>
</tr>
<tr class="even">
<td><ol start="30" type="1">
<li></li>
</ol></td>
<td><p>The <strong>volumeMount</strong> is pretty self-explanatory—create a volume mount for the <strong>mariadb-config-volume</strong> (specified in the <strong>volumes</strong> list below it) to the path <strong>/etc/mysql/conf.d</strong>.</p>
<p><img src="./media/image1.png" style="width:0.55208in;height:0.55208in" />Then, in the <strong>volumes</strong> list, <strong>configMap</strong> tells Kubernetes to use the <strong>mariadb-config</strong> ConfigMap, taking the contents of the key <strong>max_allowed_packet.cnf</strong> and mounting it to the path <strong>max_allowed_packed.cnf</strong>. The name of the volume is <strong>mariadb-config-volume</strong>, which was referenced in the <strong>volumeMounts</strong> above.</p>
<p><em>Note:</em> The <strong>path</strong> from the <strong>configMap</strong> is the name of a file that will contain the contents of the key's value. In this case, your key was a file name, too, but it doesn't have to be. Note also that <strong>items</strong> is a list, so multiple keys can be referenced and their values mounted as files. These files will all be created in the <strong>mountPath</strong> of the <strong>volumeMount</strong> specified above: <strong>/etc/mysql/conf.d</strong>.</p></td>
</tr>
<tr class="odd">
<td><ol start="31" type="1">
<li></li>
</ol></td>
<td><p>At this point, you should have enough to create a MariaDB instance. You have two Secrets, one holding the <strong>MYSQL_ROOT_PASSWORD</strong> and another storing the <strong>MYSQL_USER</strong>, and the <strong>MYSQL_PASSWORD</strong> environment variables to be added to the container. You also have a ConfigMap holding the contents of a MySQL config file that overrides the <strong>max_allowed_packed</strong> value from its default setting.</p>
<p>You also have a <strong>mariadb-deployment.yaml</strong> file that describes a Kubernetes deployment of a Pod with a MariaDB container and adds the Secrets as environment variables and the ConfigMap as a volume-mounted file in the container. It should look like this:</p></td>
</tr>
<tr class="even">
<td><ol start="32" type="1">
<li></li>
</ol></td>
<td><p>apiVersion: apps/v1</p>
<p>kind: Deployment</p>
<p>metadata:</p>
<p>labels:</p>
<p>app: mariadb</p>
<p>name: mariadb-deployment</p>
<p>spec:</p>
<p>replicas: 1</p>
<p>selector:</p>
<p>matchLabels:</p>
<p>app: mariadb</p>
<p>template:</p>
<p>metadata:</p>
<p>labels:</p>
<p>app: mariadb</p>
<p>spec:</p>
<p>containers:</p>
<p>- image: docker.io/mariadb:10.4</p>
<p>name: mariadb</p>
<p>env:</p>
<p>- name: MYSQL_ROOT_PASSWORD</p>
<p>valueFrom:</p>
<p>secretKeyRef:</p>
<p>name: mariadb-root-password</p>
<p>key: password</p>
<p>envFrom:</p>
<p>- secretRef:</p>
<p>name: mariadb-user-creds</p>
<p>ports:</p>
<p>- containerPort: 3306</p>
<p>protocol: TCP</p>
<p>volumeMounts:</p>
<p>- mountPath: /var/lib/mysql</p>
<p>name: mariadb-volume-1</p>
<p>- mountPath: /etc/mysql/conf.d</p>
<p>name: mariadb-config-volume</p>
<p>volumes:</p>
<p>- emptyDir: {}</p>
<p>name: mariadb-volume-1</p>
<p>- configMap:</p>
<p>name: mariadb-config</p>
<p>items:</p>
<p>- key: max_allowed_packet.cnf</p>
<p>path: max_allowed_packet.cnf</p>
<p>name: mariadb-config-volume</p>
<p><img src="./media/image3.png" style="width:0.55208in;height:0.55208in" /> <strong>(Note: The file is available on the git you cloned in module 0)</strong></p></td>
</tr>
<tr class="odd">
<td><ol start="33" type="1">
<li></li>
</ol></td>
<td><p>Create a new MariaDB instance from the YAML file with the <strong>kubectl create</strong> command:</p>
<p>kubectl create -f mariadb-deployment.yaml</p></td>
</tr>
<tr class="even">
<td><ol start="34" type="1">
<li></li>
</ol></td>
<td><p>Once the deployment has been created, use the <strong>kubectl get</strong> command to view the running MariaDB pod:</p>
<p>kubectl get pods</p>
<p>Sample output:</p>
<p>NAME                        READY     STATUS    RESTARTS   AGE</p>
<p>mariadb-deployment-54657jfqm   1/1       Running   0          3m</p>
<p>Make a note of the Pod name (different from the example above).</p></td>
</tr>
<tr class="odd">
<td><ol start="35" type="1">
<li></li>
</ol></td>
<td><p>Use the <strong>kubectl exec</strong> command (with your Pod name) to validate that the Secrets and ConfigMaps are in use. For example, check that the environment variables are exposed in the container:</p>
<p>kubectl exec -it mariadb-deployment-1234-abcd env |grep MYSQL</p>
<p>Sample output:</p>
<p>MYSQL_PASSWORD=netapp-still-rocks</p>
<p>MYSQL_USER=kubeuser</p>
<p>MYSQL_ROOT_PASSWORD=NetAppRocks!</p>
<p>Success! All three environment variables—the one using the <strong>env</strong> setup to specify the Secret, and two using <strong>envFrom</strong> to mount all the values from the Secret—are available in the container for MariaDB to use.</p></td>
</tr>
<tr class="even">
<td><ol start="36" type="1">
<li></li>
</ol></td>
<td><p>Spot check that the <strong>max_allowed_packet.cnf</strong> file was created in <strong>/etc/mysql/conf.d</strong> and that it contains the expected content:</p>
<p>kubectl exec -it mariadb-deployment-1234-abcd ls /etc/mysql/conf.d</p>
<p>Sample output:</p>
<p>max_allowed_packet.cnf</p></td>
</tr>
<tr class="odd">
<td><ol start="37" type="1">
<li></li>
</ol></td>
<td><p>Spot check that the <strong>max_allowed_packet.cnf</strong> file was created in <strong>/etc/mysql/conf.d</strong> and that it contains the expected content:</p>
<p>kubectl exec -it mariadb-deployment-1234-abcd cat /etc/mysql/conf.d/max_allowed_packet.cnf</p>
<p>Sample output:</p>
<p>[mysqld]</p>
<p>max_allowed_packet = 32M</p></td>
</tr>
<tr class="even">
<td><ol start="38" type="1">
<li></li>
</ol></td>
<td><p>Finally, validate that MariaDB used the environment variable to set the root user password and read the <strong>max_allowed_packet.cnf</strong> file to set the <strong>max_allowed_packet</strong> configuration variable. Use the <strong>kubectl exec</strong> command again, this time to get a shell inside the running container:</p>
<p>kubectl exec -it mariadb-deployment-1234-abcd bin/sh</p></td>
</tr>
<tr class="odd">
<td><ol start="39" type="1">
<li></li>
</ol></td>
<td><p>Within the shell, list the databases:</p>
<p># mysql -uroot -p${MYSQL_ROOT_PASSWORD} -e 'show databases;'</p>
<p>Sample output:</p>
<p>+--------------------+</p>
<p>| Database           |</p>
<p>+--------------------+</p>
<p>| information_schema |</p>
<p>| mysql              |</p>
<p>| performance_schema |</p>
<p>+--------------------+</p></td>
</tr>
<tr class="even">
<td><ol start="40" type="1">
<li></li>
</ol></td>
<td><p>Within the shell, show the max_allowed_packet variable that was passed in using a ConfigMap:</p>
<p># mysql -uroot -p${MYSQL_ROOT_PASSWORD} -e "SHOW VARIABLES LIKE 'max_allowed_packet';"</p>
<p>Sample output:</p>
<p>+--------------------+----------+</p>
<p>| Variable_name      | Value    |</p>
<p>+--------------------+----------+</p>
<p>| max_allowed_packet | 33554432 |</p>
<p>+--------------------+----------+</p></td>
</tr>
<tr class="odd">
<td><ol start="41" type="1">
<li></li>
</ol></td>
<td><p>Exit the shell:</p>
<p># exit</p></td>
</tr>
<tr class="even">
<td><ol start="42" type="1">
<li></li>
</ol></td>
<td><p>Delete the deployment:</p>
<p>kubectl delete -f mariadb-deployment.yaml</p></td>
</tr>
<tr class="odd">
<td><ol start="43" type="1">
<li></li>
</ol></td>
<td><img src="./media/image1.png" style="width:0.55208in;height:0.55208in" />This exercise explained how to create Kubernetes Secrets and ConfigMaps and how to use those Secrets and ConfigMaps by adding them as environment variables or files inside of a running container instance. This makes it easy to keep the configuration of individual instances of containers separate from the container image. By separating the configuration data, overhead is reduced to maintaining only a single image for a specific type of instance while retaining the flexibility to create instances with a wide variety of configurations.</td>
</tr>
</tbody>
</table>

End of Exercise
