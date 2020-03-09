Module 1: Docker

## Objectives

This exercise focuses on enabling you to do the following:

  - > Get familiar with the docker common CLI commands

  - > Modify an existing container

  - > Create your own docker image using dockerfile
    
    **NOTE: All the tasks in the following module are executed on the
    Docker host rhel5 or rhel6.**

## Task 1: Get familiar with the docker common CLI command

In this task, you will explore the docker CLI.

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
<td><p>Using Putty, open an SSH connection to rhel5:</p>
<p><img src="M1_Docker_Media/media/image1.png" style="width:3.70476in;height:3.6389in" /></p></td>
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
<td><p>Issue the following command and review the available options:</p>
<p>docker</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Now issue the following command and review the available options:</p>
<p>docker container</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Review the running containers:</p>
<p>docker ps</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Check the version of docker installed on your system:</p>
<p>docker version</p>
<p>Visit the release notes website to find out about the version you are running:</p>
<p><a href="https://docs.docker.com/engine/release-notes/">https://docs.docker.com/engine/release-notes/</a></p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Display the detailed information about your instance:</p>
<p>docker info</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Verify that no images are present on the system:</p>
<p>docker images</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Run your first ephemeral container:</p>
<p>docker run hello-world</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Verify a new image was downloaded for the hello-world container:</p>
<p>docker images</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Run the following command to search for a specific docker image:</p>
<p>docker search httpd</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><p>Open the following Website from a browser <a href="https://index.docker.io">https://index.docker.io</a> and search for “httpd”.</p>
<p>Note: index.docker.io is the default repository used by docker to search and download images. It can be viewed using the “docker info” command.</p></td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>Download the httpd version 2.2 docker image:</p>
<p>docker pull httpd:2.2</p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td><p>Verify the image downloaded:</p>
<p>docker images</p></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>Start a container using the newly downloaded docker image:</p>
<p>docker run -d --name webserver httpd:2.2</p>
<p><img src="M1_Docker_Media/media/image2.png" style="width:0.55208in;height:0.55208in" /></p>
<p>The -d option indicates that the container is to run in detached mode rather than in the foreground, so that you will not tie up your command line session while the container is running.</p></td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td><p>Verify that your container is running:</p>
<p>docker ps</p></td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td><p>Check the logs for your container:</p>
<p>docker logs webserver</p>
<p>Note: you can also use the 3 first digits of the container ID from the docker ps output.</p></td>
</tr>
<tr class="even">
<td><ol start="18" type="1">
<li></li>
</ol></td>
<td><p>Now open Chrome on the jump host and browse the rhel5 shortcut:</p>
<p><img src="M1_Docker_Media/media/image3.png" style="width:2.84762in;height:1.44206in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="19" type="1">
<li></li>
</ol></td>
<td><p>The following should appear:</p>
<p><img src="M1_Docker_Media/media/image4.png" style="width:2.77143in;height:3.04704in" /></p></td>
</tr>
<tr class="even">
<td><ol start="20" type="1">
<li></li>
</ol></td>
<td>This is expected behavior, as we did not specify any port mapping between the container and the host, so the container is completely isolated.</td>
</tr>
<tr class="odd">
<td><ol start="21" type="1">
<li></li>
</ol></td>
<td><p>Stop and remove the container:</p>
<p>docker stop webserver</p>
<p>docker rm webserver</p></td>
</tr>
</tbody>
</table>

## Task 2: modify an existing container

In this task, you will learn how to configure container network ports
and modify the container templates.

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
<td><p>Create a new httpd container, this time with a port mapping from the container’s TCP port 80 to the host’s TCP port 80:</p>
<p>docker run -d --name webserver -p 80:80 httpd:2.2</p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Verify that the container is running, and the port mapping information is displayed:</p>
<p>docker ps</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Open a browser again and visit the rhel5.demo.netapp.com website, or refresh the previous page:</p>
<p><img src="M1_Docker_Media/media/image5.png" style="width:3.40952in;height:1.77295in" /></p>
<p>This is the Apache default Webserver page.</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Now let’s change the contents displayed by our webserver. We will need to enter the container image’s CLI first:</p>
<p>docker exec -it webserver /bin/bash</p>
<p><img src="M1_Docker_Media/media/image6.png" style="width:0.55208in;height:0.625in" />Notice the change in the command prompt. The docker exec command runs a command inside a running container. In this example that command is the bash shell, and the combination of the -i and -t options for the exec sub-command indicates that Docker should maintain an interactive tty connection to that bash shell until you explicitly terminate it.</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>Navigate to the container's htdoc's directory:</p>
<p>cd /usr/local/apache2/htdocs</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Display the directory's contents (you should see the default index.html):</p>
<p>ls -al</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Review the contents of index.html:</p>
<p>cat index.html</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Now modify the contents using the following command:</p>
<p>echo '&lt;h1&gt;Welcome to the NetApp Data Fabric!&lt;/h1&gt;' &gt; index.html</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Review the contents of index.html:</p>
<p>cat index.html</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td>Open a browser again and visit the rhel5.demo.netapp.com website, or refresh the previous page:<img src="M1_Docker_Media/media/image7.png" style="width:4.8in;height:1.92335in" /></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Exit the container and stop it:</p>
<p>exit</p>
<p>docker stop webserver</p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><p>Refresh the website:</p>
<p><img src="M1_Docker_Media/media/image4.png" style="width:3.06667in;height:3.37164in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>Start the container:</p>
<p>docker start webserver</p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td><p>Refresh the website:</p>
<p><img src="M1_Docker_Media/media/image7.png" style="width:4.8in;height:1.92335in" /></p>
<p>This time the page displays properly, demonstrating that the content was preserved across restarts of the hosting container.</p></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>Stop and remove the container:</p>
<p>docker stop webserver &amp;&amp; docker rm webserver</p>
<p><img src="M1_Docker_Media/media/image6.png" style="width:0.55208in;height:0.55208in" /></p>
<p>Tip: The “&amp;&amp;” syntax in the above command allows you to logically link the execution of two independent commands together. In this example the “docker rm my_www” command will execute only if the “docker stop my_www” completes successfully.</p></td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td>Now let’s update the version of our Apache Server container image.</td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td><p>Instantiate a container for the latest httpd:</p>
<p>docker run -d --name webserver -p 80:80 httpd:latest</p>
<p>Since the latest httpd container image is not available locally, Docker initiates an image download operation that once finished, instantiates the container.</p>
<p><img src="M1_Docker_Media/media/image2.png" style="width:0.55208in;height:0.55208in" /></p>
<p>Attention: If you get an error message stating “...lookup index.docker.io: no such host” while running this command, try re-issuing the command. This image gets pulled from a public Docker server that has, on occasion, emitted this error, but the error resolves itself when you re-run the command.</p></td>
</tr>
<tr class="even">
<td><ol start="18" type="1">
<li></li>
</ol></td>
<td><p>Issue the following command to review the new image:</p>
<p>docker images</p></td>
</tr>
<tr class="odd">
<td><ol start="19" type="1">
<li></li>
</ol></td>
<td><p>Verify that the container is running, and the port mapping information and image version are displayed:</p>
<p>docker ps</p></td>
</tr>
<tr class="even">
<td><ol start="20" type="1">
<li></li>
</ol></td>
<td><p>Refresh your browser to verify that the new httpd container is running:</p>
<p><img src="M1_Docker_Media/media/image8.png" style="width:3.26667in;height:1.44828in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="21" type="1">
<li></li>
</ol></td>
<td><p>Stop and remove the container:</p>
<p>docker stop webserver &amp;&amp; docker rm webserver</p></td>
</tr>
<tr class="even">
<td><ol start="22" type="1">
<li></li>
</ol></td>
<td><p>Now create a new Docker local container volume named “webserver” using the local file system driver (it will create that volume on one of rhel5's local disks, outside of any given container's native directory trees) and verify it:</p>
<p>docker volume create --name=webserver</p>
<p>docker volume ls</p></td>
</tr>
<tr class="odd">
<td><ol start="23" type="1">
<li></li>
</ol></td>
<td><p>Inspect the newly created webserver volume:</p>
<p>docker volume inspect webserver</p>
<p>Notice that the Mountpoint value indicates that this persistent volume maps to the /var/lib/docker/volumes/webserver/_data directory on the container host (i.e., on rhel5).</p></td>
</tr>
<tr class="even">
<td><ol start="24" type="1">
<li></li>
</ol></td>
<td><p>You have created a persistent volume, but that volume is not currently associated with any container, so it is empty. Create a new “webserver” container that has the “webserver” volume attached as /usr/local/apache2/htdocs (the location of the index file):</p>
<p>docker run -d --name webserver -v webserver:/usr/local/apache2/htdocs -p 80:80 httpd:2.2</p></td>
</tr>
<tr class="odd">
<td><ol start="25" type="1">
<li></li>
</ol></td>
<td><p>Inspect the “webserver” container you just created and find the “HostConfig” and “Mounts” sections:</p>
<p>docker inspect webserver</p></td>
</tr>
<tr class="even">
<td><ol start="26" type="1">
<li></li>
</ol></td>
<td><p>Refresh the Chrome browser to display <a href="http://rhel5.demo.netapp.com">http://rhel5.demo.netapp.com</a>:</p>
<p><img src="M1_Docker_Media/media/image8.png" style="width:3.86667in;height:1.71428in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="27" type="1">
<li></li>
</ol></td>
<td><p>Now from RHEL5, display the contents of the Host volume you mapped inside the container:</p>
<p>ls /var/lib/docker/volumes/webserver/_data</p>
<p><img src="M1_Docker_Media/media/image6.png" style="width:0.55208in;height:0.55208in" /></p>
<p>Note: This folder is on your Docker Host, but contains files from your container path specified in step 2-7: /usr/local/apache2/htdocs.</p>
<p>Those are, for an Apache Webserver, the index.html file.</p></td>
</tr>
<tr class="even">
<td><ol start="28" type="1">
<li></li>
</ol></td>
<td><p>Now using the Docker host, let’s change the Webserver’s default file:</p>
<p>echo ‘&lt;h1&gt;This is a persistent page!&lt;/h1&gt;’ &gt; /var/lib/docker/volumes/webserver/_data/index.html</p></td>
</tr>
<tr class="odd">
<td><ol start="29" type="1">
<li></li>
</ol></td>
<td><p>Refresh Chrome to display the updated Webserver:</p>
<p><img src="M1_Docker_Media/media/image9.png" style="width:3.66667in;height:1.69365in" /></p></td>
</tr>
<tr class="even">
<td><ol start="30" type="1">
<li></li>
</ol></td>
<td><p>Stop and remove the container:</p>
<p>docker stop webserver &amp;&amp; docker rm webserver</p></td>
</tr>
<tr class="odd">
<td><ol start="31" type="1">
<li></li>
</ol></td>
<td><p>Now from RHEL5, check the contents of your Docker Volume and the index file:</p>
<p>ls /var/lib/docker/volumes/webserver/_data</p>
<p>cat /var/lib/docker/volumes/webserver/_data/index.html</p></td>
</tr>
<tr class="even">
<td><ol start="32" type="1">
<li></li>
</ol></td>
<td><p>Now let’s create a new webserver container with our persistent configuration:</p>
<p>docker run -d --name webserver -v webserver:/usr/local/apache2/htdocs -p 80:80 httpd:latest</p></td>
</tr>
<tr class="odd">
<td><ol start="33" type="1">
<li></li>
</ol></td>
<td><p>Refresh Chrome to display the new Webserver:</p>
<p><img src="M1_Docker_Media/media/image9.png" style="width:3.66667in;height:1.69365in" /></p></td>
</tr>
<tr class="even">
<td><ol start="34" type="1">
<li></li>
</ol></td>
<td><p>Cleanup what you have created:</p>
<p>docker stop webserver &amp;&amp; docker rm webserver</p>
<p>docker ps -a</p>
<p>docker volume rm webserver</p>
<p>docker volume ls</p></td>
</tr>
</tbody>
</table>

## Task 3: CREATE A new DOCKER CUSTOM IMAGE using the dockerfile

There are two ways to create a custom container image. The first is to
take an existing image, modify it, and then push it into the registry.
The second is to build an image using a Dockerfile, then push it to the
registry.

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
<td><p>Using Putty, open an SSH connection to rhel5:</p>
<p><img src="M1_Docker_Media/media/image1.png" style="width:3.70476in;height:3.6389in" /></p></td>
</tr>
<tr class="even">
<td><ol start="2" type="1">
<li></li>
</ol></td>
<td><p>Instantiate the <strong>new</strong> httpd container instance that you will customize:</p>
<p>docker run -d --name my_webserver httpd:latest</p></td>
</tr>
<tr class="odd">
<td><ol start="3" type="1">
<li></li>
</ol></td>
<td><p>Establish an interactive CLI session into the container:</p>
<p>docker exec -it my_webserver /bin/bash</p></td>
</tr>
<tr class="even">
<td><ol start="4" type="1">
<li></li>
</ol></td>
<td><p>Create a new index.html file for the container's web server:</p>
<p>echo '&lt;h1&gt;Build your own Data Fabric!&lt;/h1&gt;' &gt; htdocs/index.html</p></td>
</tr>
<tr class="odd">
<td><ol start="5" type="1">
<li></li>
</ol></td>
<td><p>End the CLI session:</p>
<p>exit</p></td>
</tr>
<tr class="even">
<td><ol start="6" type="1">
<li></li>
</ol></td>
<td><p>Verify that the container is still running and then stop it:</p>
<p>docker stop my_webserver</p></td>
</tr>
<tr class="odd">
<td><ol start="7" type="1">
<li></li>
</ol></td>
<td><p>Display a list of all the containers on the host, including those that are not running:</p>
<p>docker ps -a</p></td>
</tr>
<tr class="even">
<td><ol start="8" type="1">
<li></li>
</ol></td>
<td><p>Commit the changes you just made to the “my_custom_httpd” image:</p>
<p>docker commit my_webserver registry.demo.netapp.com/my_webserver:latest</p>
<p><img src="M1_Docker_Media/media/image6.png" style="width:0.55208in;height:0.55208in" />When you commit the container, you are preserving its state so that it can be re-used to instantiate other container instances. On a commit operation, Docker creates a new local image, but only stores the differences between the container's source image and the container's current state.</p></td>
</tr>
<tr class="odd">
<td><ol start="9" type="1">
<li></li>
</ol></td>
<td><p>Generate a list of the local images: docker images</p>
<p><img src="M1_Docker_Media/media/image6.png" style="width:0.55208in;height:0.55208in" />Note the repository name for the newly committed image, “registry.demo.netapp.com/my_webserver_image”. By default, the docker command assumes commits are supposed to go to the main docker hub (hub.docker.com), but this lab exercise utilizes a local docker registry server (registry.demo.netapp.com, which is an alias for rhel4).</p></td>
</tr>
<tr class="even">
<td><ol start="10" type="1">
<li></li>
</ol></td>
<td><p>Push the new “my_webserver” image to the registry server so that server can offer the image to other users and registry servers:</p>
<p>docker push registry.demo.netapp.com/my_webserver</p></td>
</tr>
<tr class="odd">
<td><ol start="11" type="1">
<li></li>
</ol></td>
<td><p>Switch to a Putty Session on rhel6:</p>
<p><img src="M1_Docker_Media/media/image10.png" style="width:3.82857in;height:3.74387in" /></p></td>
</tr>
<tr class="even">
<td><ol start="12" type="1">
<li></li>
</ol></td>
<td><p>Pull a copy of the “my_webserver” image from the registry server to the local host:</p>
<p>docker pull registry.demo.netapp.com/my_webserver</p></td>
</tr>
<tr class="odd">
<td><ol start="13" type="1">
<li></li>
</ol></td>
<td><p>Instantiate a new container from this image:</p>
<p>docker run -d -p 80:80 registry.demo.netapp.com/my_webserver</p></td>
</tr>
<tr class="even">
<td><ol start="14" type="1">
<li></li>
</ol></td>
<td><p>In Chrome, open the web page for <a href="http://rhel6.demo.netapp.com">http://rhel6.demo.netapp.com</a>:</p>
<p><img src="M1_Docker_Media/media/image11.png" style="width:4.02857in;height:1.52681in" /></p>
<p>The web page published by rhel6 matches the customized web page you included in the customized container image you created using rhel5.</p></td>
</tr>
<tr class="odd">
<td><ol start="15" type="1">
<li></li>
</ol></td>
<td><p>Terminate your container instance using the First 3 digits of the Container ID. You will need the container ID from your lab instance to do it. This can be displayed using the following command:</p>
<p>docker ps</p>
<p>CONTAINER ID IMAGE</p>
<p><strong>cffee8cc30b8</strong> httpd:latest</p></td>
</tr>
<tr class="even">
<td><ol start="16" type="1">
<li></li>
</ol></td>
<td><p>Cleanup what you have created (you can use the entire Container ID, or the first 3 digits):</p>
<p>docker stop <strong>cff</strong> &amp;&amp; docker rm <strong>cff</strong></p></td>
</tr>
<tr class="odd">
<td><ol start="17" type="1">
<li></li>
</ol></td>
<td><p>Exit rhel6 and go back to rhel5.</p>
<p><img src="M1_Docker_Media/media/image6.png" style="width:0.55208in;height:0.55208in" />Next you will build an image using a Dockerfile. A Dockerfile contains a set of instructions on how to build new images, and the docker build command executes those instructions. Dockerfiles offer a more robust way to build images because they document and automate the build process, which makes it easier to share image development processes among a team.</p></td>
</tr>
<tr class="even">
<td><ol start="18" type="1">
<li></li>
</ol></td>
<td><p>On rhel5, let’s cerate a temporary folder :</p>
<p>mkdir /tmp/custom</p>
<p>cd /tmp/custom</p></td>
</tr>
<tr class="odd">
<td><ol start="19" type="1">
<li></li>
</ol></td>
<td><p>Create a new index.html file that you will add to the new image you are creating:</p>
<p>echo '&lt;h1&gt;This is the Docker Fabric!&lt;/h1&gt;' &gt; index.html</p></td>
</tr>
<tr class="even">
<td><ol start="20" type="1">
<li></li>
</ol></td>
<td><p>Create a Dockerfile that contains instructions for customizing your image and check it:</p>
<p>echo 'FROM httpd:latest' &gt; Dockerfile</p>
<p>echo 'ADD index.html /usr/local/apache2/htdocs/index.html' &gt;&gt; Dockerfile</p>
<p>cat Dockerfile</p>
<p>These instructions indicate that Docker should use the “httpd:latest” image as a starting point, and then install the customized index.html file in the image as /usr/local/apache2/htdocs/index.html.</p></td>
</tr>
<tr class="odd">
<td><ol start="21" type="1">
<li></li>
</ol></td>
<td><p>Build the customized image:</p>
<p>docker build -t registry.demo.netapp.com/another_webserver /tmp/custom</p></td>
</tr>
<tr class="even">
<td><ol start="22" type="1">
<li></li>
</ol></td>
<td><p>List the docker images on this host and look for ‘another_webserver’:</p>
<p>docker images</p></td>
</tr>
<tr class="odd">
<td><ol start="23" type="1">
<li></li>
</ol></td>
<td><p>Start a new container based on that image:</p>
<p>docker run -d -p 80:80 registry.demo.netapp.com/another_webserver</p></td>
</tr>
<tr class="even">
<td><ol start="24" type="1">
<li></li>
</ol></td>
<td><p>Refresh the Chrome browser to display <a href="http://rhel5.demo.netapp.com">http://rhel5.demo.netapp.com</a>:</p>
<p><img src="M1_Docker_Media/media/image12.png" style="width:3.32381in;height:1.51669in" /></p></td>
</tr>
<tr class="odd">
<td><ol start="25" type="1">
<li></li>
</ol></td>
<td><p>Terminate your container instance using the First 3 digits of the Container ID. You will need the container ID from your lab instance to do it. This can be displayed using the following command:</p>
<p>docker ps</p>
<p>CONTAINER ID IMAGE</p>
<p><strong>7e972e3969a1</strong> registry.demo.netapp.com/another_webserver</p></td>
</tr>
<tr class="even">
<td><ol start="26" type="1">
<li></li>
</ol></td>
<td><p>Cleanup what you have created (you can use the entire Container ID, or the first 3 digits):</p>
<p>docker stop <strong>7e9</strong> &amp;&amp; docker rm <strong>7e9</strong></p></td>
</tr>
<tr class="odd">
<td><ol start="27" type="1">
<li></li>
</ol></td>
<td><p>Terminate your PuTTY session to rhel5:</p>
<p>exit</p></td>
</tr>
<tr class="even">
<td><ol start="28" type="1">
<li></li>
</ol></td>
<td><img src="M1_Docker_Media/media/image6.png" style="width:0.55208in;height:0.55208in" />You can now build your own Docker Images based on available images from Docker.io, or private repositories. If you want to try something more complex, you could deploy an Ansible Server in Docker using the ansible/ansible Dockerfile available here: <a href="https://hub.docker.com/r/ansible/ansible/dockerfile">https://hub.docker.com/r/ansible/ansible/dockerfile</a></td>
</tr>
</tbody>
</table>

End of Exercise
