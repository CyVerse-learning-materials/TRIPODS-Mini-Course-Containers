**Introduction to Docker**
--------------------------

.. image:: ../img/docker.jpg
  :width: 150
  :height: 150

This is the introductory session of Docker. The topics include Docker installation, running prebuilt Docker containers, managing data in Docker, and exposing container ports. This session gets you ready to run most of the existing Docker containers.

1. Prerequisites
================

There are no specific skills needed for this tutorial beyond a basic comfort with the command line and using a text editor. Prior experience in python will be helpful but is not required.

2. Docker Installation
======================

Getting all the tooling setup on your computer can be a daunting task, but not with Docker. Getting Docker up and running on your favorite OS (Mac/Windows/Linux) is very easy.

The getting started guide on Docker has detailed instructions for setting up Docker on `Mac <https://docs.docker.com/docker-for-mac/install/>`_/`Windows <https://docs.docker.com/docker-for-windows/install/>`_/`Linux <https://docs.docker.com/install/linux/docker-ce/ubuntu/>`_.

.. Note::

	If you're using an older version of Windows or MacOS you may need to use `Docker Machine <https://docs.docker.com/machine/overview/>`_ instead. All commands work in either bash or Powershell on Windows.

Once you are done installing Docker, test your Docker installation by running the following command to make sure you are using version 1.13 or higher:

.. code-block:: bash

	$ docker --version
	Docker version 18.03.1-ce, build 9ee9f40 # This is the version on my computer

When run without ``--version`` you should see a whole bunch of lines showing the different options available with ``docker``. Alternatively you can test your installation by running the following:

.. code-block:: bash

	$ docker run hello-world
	Unable to find image 'hello-world:latest' locally
	latest: Pulling from library/hello-world
	03f4658f8b78: Pull complete
	a3ed95caeb02: Pull complete
	Digest: sha256:8be990ef2aeb16dbcb9271ddfe2610fa6658d13f6dfb8bc72074cc1ca36966a7
	Status: Downloaded newer image for hello-world:latest

	Hello from Docker.
	This message shows that your installation appears to be working correctly.

	To generate this message, Docker took the following steps:
	 1. The Docker client contacted the Docker daemon.
	 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
	 3. The Docker daemon created a new container from that image which runs the
	    executable that produces the output you are currently reading.
	 4. The Docker daemon streamed that output to the Docker client, which sent it
	    to your terminal.

	To try something more ambitious, you can run an Ubuntu container with:
 	$ docker run -it ubuntu bash

	Share images, automate workflows, and more with a free Docker ID:
 	https://hub.docker.com/

	For more examples and ideas, visit:
 	https://docs.docker.com/engine/userguide/   

.. Note::

	Depending on how you've installed Docker on your system, you might see a ``permission denied`` error after running the above command. If you're on Linux, you may need to prefix your Docker commands with `sudo`. Alternatively to run docker command without `sudo`, you need to add your user (who has root privileges) to docker group. For this run:

	1. Create the docker group::

		$ sudo groupadd docker
	
	2. Add your user to the docker group::

		$ sudo usermod -aG docker ${USER}
	
	3. Log out and log back in so that your group membership is re-evaluated

3. Running Docker containers
============================

Now that you have everything setup, it's time to get our hands dirty. In this section, you are going to run a container from `Alpine Linux <http://www.alpinelinux.org/>`_ (a lightweight linux distribution) image on your system and get a taste of the ``docker run`` command.

But wait, what exactly is a Container and Image?

**Containers** - Running instances of Docker images. Containers run the actual applications. A container includes an application and all of its dependencies. It shares the kernel with other containers, and runs as an isolated process in user space on the host OS.

**Images** - The file system and configuration of our application which are used to create containers. To find out more about a Docker image, run ``docker inspect hello-world`` and ``docker history hello-world``.

Now that we know what a container and image is, let's run the following command in our terminal:

.. code-block:: bash

	$ docker run alpine ls -l
	total 52
	drwxr-xr-x    2 root     root          4096 Dec 26  2016 bin
	drwxr-xr-x    5 root     root           340 Jan 28 09:52 dev
	drwxr-xr-x   14 root     root          4096 Jan 28 09:52 etc
	drwxr-xr-x    2 root     root          4096 Dec 26  2016 home
	drwxr-xr-x    5 root     root          4096 Dec 26  2016 lib
	drwxr-xr-x    5 root     root          4096 Dec 26  2016 media
	........

Similar to ``docker run hello-world`` command in the demo above, ``docker run alpine ls -l`` command fetches the ``alpine:latest`` image from the Docker registry first, saves it in our system and then runs a container from that saved image.

When you run ``docker run alpine``, you provided a command ``ls -l``, so Docker started the command specified and you saw the listing

You can use the ``docker images`` command to see a list of all images on your system

.. code-block:: bash

	$ docker images
	REPOSITORY              TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	alpine                 	latest              c51f86c28340        4 weeks ago         1.109 MB
	hello-world             latest              690ed74de00f        5 months ago        960 B

Let's try something more exciting.

.. code-block:: bash

	$ docker run alpine echo "Hello world"
	Hello world

OK, that's some actual output. In this case, the Docker client dutifully ran the ``echo`` command in our ``alpine`` container and then exited it. If you've noticed, all of that happened pretty quickly. Imagine booting up a virtual machine, running a command and then killing it. Now you know why they say containers are fast!

Try another command.

.. code-block:: bash

	$ docker run alpine sh

Wait, nothing happened! Is that a bug? Well, no. These interactive shells will exit after running any scripted commands such as ``sh``, unless they are run in an interactive terminal - so for this example to not exit, you need to ``docker run -it alpine sh``. You are now inside the container shell and you can try out a few commands like ``ls -l``, ``uname -a`` and others.

Before doing that, now it's time to see the ``docker ps`` command which shows you all containers that are currently running.

.. code-block:: bash

	$ docker ps
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

Since no containers are running, you see a blank line. Let's try a more useful variant: ``docker ps -a``

.. code-block:: bash

	$ docker ps -a
	CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
	36171a5da744        alpine              "/bin/sh"                5 minutes ago       Exited (0) 2 minutes ago                        fervent_newton
	a6a9d46d0b2f        alpine             "echo 'hello from alp"    6 minutes ago       Exited (0) 6 minutes ago                        lonely_kilby
	ff0a5c3750b9        alpine             "ls -l"                   8 minutes ago       Exited (0) 8 minutes ago                        elated_ramanujan
	c317d0a9e3d2        hello-world         "/hello"                 34 seconds ago      Exited (0) 12 minutes ago                       stupefied_mcclintock

What you see above is a list of all containers that you ran. Notice that the STATUS column shows that these containers exited a few minutes ago.

If you want to run scripted commands such as ``sh``, they should be run in an interactive terminal. In addition, interactive terminal allows you to run more than one command in a container. Let's try that now:

.. code-block:: bash

	$ docker run -it alpine sh
	/ # ls
	bin    dev    etc    home   lib    media  mnt    proc   root   run    sbin   srv    sys    tmp    usr    var
	/ # uname -a
	Linux de4bbc3eeaec 4.9.49-moby #1 SMP Wed Sep 27 23:17:17 UTC 2017 x86_64 Linux

Running the ``run`` command with the ``-it`` flags attaches us to an interactive ``tty`` in the container. Now you can run as many commands in the container as you want. Take some time to run your favorite commands.

Exit out of the container by giving the ``exit`` command.

.. code-block:: bash

	/ # exit

.. Note::

	If you type ``exit`` your **container** will exit and is no longer active. To check that, try the following::

		$ docker ps -l
		CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS                          PORTS                    NAMES
		de4bbc3eeaec        alpine                "/bin/sh"                3 minutes ago       Exited (0) About a minute ago                            pensive_leavitt

	If you want to keep the container active, then you can use keys ``Ctrl+p, Ctrl+q``. To make sure that it is not exited run the same ``docker ps -a`` command again::

		$ docker ps -l
		CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS                         PORTS                    NAMES
		0db38ea51a48        alpine                "sh"                     3 minutes ago       Up 3 minutes                                            elastic_lewin

	Now if you want to get back into that container, then you can type ``docker attach <container id>``. This way you can save your container::

		$ docker attach 0db38ea51a48

4. Building Docker images
=========================

So far you have been running, containers from existing Docker images. But how do you build one? One area where Docker shines is when you need to use a command line utility that has a large number of dependencies.

In this session, let's dive deeper into what Docker images are. Later on we will build our own image and use that image to run an application locally.

4.1 Docker images
~~~~~~~~~~~~~~~~~

Docker images are the basis of containers. In the previous example, you pulled the ``hello-world`` image from the registry and asked the Docker client to run a container based on that image. To see the list of images that are available locally on your system, run the ``docker images`` command.

.. code-block:: bash

	$ docker images
	REPOSITORY     TAG       IMAGE ID        CREATED           SIZE
	hello-world    latest    690ed74de00f    5 months ago      960 B
	alpine         latest    3fd9065eaf02    3 months ago      4.15MB
	.........

Above is a list of images that I've pulled from the registry and those I've created myself (we'll shortly see how). You will have a different list of images on your machine. The **TAG** refers to a particular snapshot of the image and the **ID** is the corresponding unique identifier for that image.

For simplicity, you can think of an image akin to a git repository - images can be committed with changes and have multiple versions. When you do not provide a specific version number, the client defaults to latest.

For example you could pull a specific version of ubuntu image as follows:

.. code-block:: bash

	$ docker pull ubuntu:16.04

If you do not specify the version number of the image, as mentioned, the Docker client will default to a version named ``latest``.

So for example, the ``docker pull`` command given below will pull an image named ``ubuntu:latest``

.. code-block:: bash

	$ docker pull ubuntu

To get a new Docker image you can either get it from a registry (such as the Docker hub) or create your own. There are hundreds of thousands of images available on Docker hub. 

An important distinction with regard to images is between base images and child images and official images and user images (Both of which can be base images or child images.).

.. important::
	**Base images** are images that have no parent images, usually images with an OS like ubuntu, alpine or debian.

	**Child images** are images that build on base images and add additional functionality.

	**Official images** are Docker sanctioned images. Docker, Inc. sponsors a dedicated team that is responsible for reviewing and publishing all Official Repositories content. This team works in collaboration with upstream software maintainers, security experts, and the broader Docker community. These are not prefixed by an organization or user name. In the list of images above, the python, node, alpine and nginx images are official (base) images. To find out more about them, check out the Official Images Documentation.

	**User images** are images created and shared by users like you. They build on base images and add additional functionality. Typically these are formatted as ``user/image-name``. The user value in the image name is your Dockerhub user or organization name.

4.2 Building custom Docker images
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

4.2.1 Using docker commit (not recommended)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As we saw in the Docker introduction, the general Docker workflow is:

- start a container based on an image in a known state
- add things to the filesystem, such as packages, codebases, libraries, files, or anything else
- commit the changes as layers to make a new image

Let's follow this workflow to built a custom image. Instead of `alpine` this time we will use `ubuntu` linux image to install some interesting packages

As before first either pull the `ubuntu` docker image or you can just `docker run -it ubuntu` to pull and run the container interactively

.. code-block :: bash

	$ docker run -it ubuntu:16.04
	Unable to find image 'ubuntu:16.04' locally
	16.04: Pulling from library/ubuntu
	Digest: sha256:9ee3b83bcaa383e5e3b657f042f4034c92cdd50c03f73166c145c9ceaea9ba7c
	Status: Downloaded newer image for ubuntu:16.04
	root@7f989e4174aa:/#

Let's install two packages `fortune`, `cowsay`, `lolcat` inside the container. But before that it's alway good idea to update the packages that are already existing in the ubuntu using `apt-get update` command.

.. code-block :: bash

	root@7f989e4174aa:/# apt-get update
	root@7f989e4174aa:/# apt-get install -y fortune cowsay lolcat

Now exit the container and run `docker ps -a` to check to see if the status of the container (which is exit in this case)

.. code-block :: bash

	 root@7f989e4174aa:/# exit

Go ahead and commit the changes and create a new image.

.. code-block :: bash

	docker commit -m "Installed fortune cowsay lolcat" 7f989e4174aa ubuntu/fortunecowsaylolcat
	sha256:77ae42b823e60c2a350228d892aacda337e1e01c19c3ae72da104f7f4a77f83f

Congratulatios. You created your fist Docker image. Check to see your docker image in the list of images using `docker images`. Let's run a container using that newly created docker image

.. code-block :: bash

	$ docker run ubuntu/fortunecowsaylolcat /usr/games/cowsay "Hi"
	 ____
	< Hi >
	 ----
	        \   ^__^
	         \  (oo)\_______
	            (__)\       )\/\
	                ||----w |
	                ||     ||

and another one

.. code-block :: bash

	$ docker run ubuntu/fortunecowsay /usr/games/fortune
	It's all in the mind, ya know.

Pretty cool isn't it.. 

**Exercise**: Can you figure out a way to combine these two commands in this order `fortune`, `cowsay` and `lolcat` to print what cowsay of the fortune output?

**Hint**: Use pipe and use interactive terminal

4.2.2 Using Dockerfile (recommended)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As you noticed by now that this method of making images is not reproducible. For example if you share this image with someone (we will see how it is done later), then they wouldn't know what is installed in this image. Ofcourse you can provide them with your notes but still it's not reproducible. Rather than just running commands and installing commands using `apt-get install`, we'll put our instructions in a special file called the Dockerfile

What exactly is a Dockerfile? 

A `Dockerfile <https://docs.docker.com/engine/reference/builder/>`_ is a text document that contains all the commands a user could call on the command line to assemble an image. Using `docker build` users can create an automated build that executes several command-line instructions in succession. Let's create a Dockerfile for the above image

Open up a text editor of your choice and type in the following commands and save it as `Dockerfile` 

.. Tip ::

	You can name your Dockerfile as anything but according to best practices it is recommended to name it as `Dockerfile` for reasons we will see later

.. code-block :: bash

	FROM ubuntu:16.04
	MAINTAINER Upendra Devisetty <upendra@cyverse.org>
	LABEL version="1.0" description="This Dockerfile is for building fortune cowsay lolcat ubuntu image"
	RUN apt-get update
	RUN apt-get install -y fortune cowsay lolcat

	ENV PATH=/usr/games/:$PATH
	CMD fortune | cowsay | lolcat

That's it. Now building the Docker image using `docker build` command as below. The ``docker build command`` is quite simple - it takes an optional tag name with the ``-t`` flag, and the location of the directory containing the Dockerfile - the ``.`` indicates the current directory:

.. code-block :: bash

	docker build -t ubuntu/fortunecowsaylolcat2 .
	Sending build context to Docker daemon  2.048kB
	Step 1/5 : FROM ubuntu:16.04
	 ---> c9d990395902
	Step 2/5 : MAINTAINER Upendra Devisetty <upendra@cyverse.org>
	 ---> Running in a365c28eb283
	Removing intermediate container a365c28eb283
	 ---> 91d18ff89d44
	Step 3/5 : LABEL Description "This Dockerfile is for building fortune coway ubuntu image"
	 ---> Running in d24ff4a347fa
	Removing intermediate container d24ff4a347fa
	 ---> 73daa1277fea
	Step 4/5 : RUN apt-get update
	 ---> Running in eed1e2fe25de
	 ..........
	 ..........
	 Successfully built ffe89a681d5c
	Successfully tagged ubuntu/fortunecowsaylolcat2:latest

Great! We successfully built a Docker image using Dockerfile. Let's test it out by launching a container using `docker run`. 

.. code-block :: bash

	$ docker run -it --rm ubuntu/fortunecowsaylolcat2:1.0 
 	_________________________________________
	/ FORTUNE PROVIDES QUESTIONS FOR THE      \
	| GREAT ANSWERS: #5 A: The Halls of       |
	| Montezuma and the Shores of Tripoli. Q: |
	| Name two families whose kids won't join |
	\ the Marines.                            /
	 -----------------------------------------
	        \   ^__^
	         \  (oo)\_______
	            (__)\       )\/\
	                ||----w |
	                ||     ||

.. Tip ::

	If you have noticed, I am running `docker run` with an extra flag `--rm`. This flag is quite useful and it removes the container after the container exits. So you don't have to manually remove them

Superb! So you have build a Docker image using Dockerfile. See how easy it is and it is also reproducible since you know how it is built. In addition, you can version control (using git or others) this Dockerfile (which we will see in a minute)

Before we go further, let's look at what those commands in Dockerfile mean

**FROM**

This instruction is used to set the base image for subsequent instructions. It is mandatory to set this in the first line of a Dockerfile. You can use it any number of times though.

**MAINTAINER**

This is a non-executable instruction used to indicate the author of the Dockerfile.

**LABEL**

You can assign metadata in the form of key-value pairs to the image using this instruction. It is important to notice that each LABEL instruction creates a new layer in the image, so it is best to use as few LABEL instructions as possible

**RUN**

This instruction lets you execute a command on top of an existing layer and create a new layer with the results of command execution

**CMD** 

This defines the commands that will run on the Image at start-up. Unlike a **RUN**, this does not create a new layer for the Image, but simply runs the command. There can only be one CMD per a Dockerfile/Image. If you need to run multiple commands, the best way to do that is to have the CMD run a script. CMD requires that you tell it where to run the command, unlike RUN.

**ENV**

This defines Environmental variables (one or more) in the Docker image

**WORKDIR**

The WORKDIR directive is used to set where the command defined with CMD is to be executed.

**ENTRYPOINT** 

This argument sets the concrete default application that is used every time a container is created using the image. For example, if you have installed a specific application inside an image and you will use this image to only run that application, you can state it with ENTRYPOINT and whenever a container is created from that image, your application will be the target

5. Docker registries
====================

To demonstrate the portability of what we just created, let’s upload our built Docker image and run it somewhere else (CyVerse Atmosphere cloud or Discovery Environment). After all, you’ll need to learn how to push to registries when you want to deploy containers to production.

.. important::

	So what exactly is a registry?

	A registry is a collection of repositories, and a repository is a collection of images—sort of like a GitHub repository, except the code is already built. An account on a registry can create many repositories. The docker CLI uses Docker’s public registry by default. You can even set up your own private registry using Docker Trusted Registry

There are several things you can do with Docker registries:

- Pushing images
- Finding images
- Pulling images
- Sharing images

5.1 Public repositories
~~~~~~~~~~~~~~~~~~~~~~~

Some example of public registries include `Docker cloud <https://cloud.docker.com/>`_, `Docker hub <https://hub.docker.com/>`_ and `quay.io <https://quay.io/>`_ etc.,

5.1.1 Log in with your Docker ID
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Now that you've created and tested your image, you can push it to Docker cloud or Docker hub.

.. Note::

	If you don’t have a Docker account, sign up for one at `Docker cloud <https://cloud.docker.com/>`_ or `Docker hub <https://hub.docker.com/>`_. Make note of your username. There are several advantages of registering to Dockerhub which we will see later on in the session

First you have to login to your Docker hub account. To do that:

.. code-block:: bash

	$ docker login -u <dockerhub username> 
	Password: 

Enter you Password when prompted.

5.1.2 Tag the image
^^^^^^^^^^^^^^^^^^^

The notation for associating a local image with a repository on a registry is ``username/repository:tag``. The tag is optional, but recommended, since it is the mechanism that registries use to give Docker images a version. Give the repository and tag meaningful names for the context, such as ``get-started:part2``. This will put the image in the ``get-started`` repository and tag it as ``part2``.

.. Note::

	By default the docker image gets a ``latest`` tag if you don't provide one. Thought convenient, it is not recommended for reproducibility purposes.

Now, put it all together to tag the image. Run docker tag image with your username, repository, and tag names so that the image will upload to your desired destination. For our docker image since we already have our Dockerhub username we will just add tag which in this case is ``1.0``

.. code-block:: bash

	$ docker tag debian/astroml:1.0 <dockerhub username>/fortunecowsaylolcat2:1.0

5.1.3 Publish the image
^^^^^^^^^^^^^^^^^^^^^^^

Upload your tagged image to the Dockerhub repository

.. code-block:: bash

	$ docker push <dockerhub username>/fortunecowsaylolcat2:1.0

Once complete, the results of this upload are publicly available. If you log in to Docker Hub, you will see the new image there, with its pull command.

Congrats! You just made your first Docker image and shared it with the world!

5.1.4 Pull and run the image from the remote repository
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's try to run the image from the remote repository on Cloud server by logging into CyVerse Atmosphere, `launching an instance <../atmosphere/boot.html>`_. If you don't have access to CyVerse Atmosphere, you can use any cloud of your choice.

First install Docker on Atmosphere using from here ``https://docs.docker.com/install/linux/docker-ce/ubuntu`` or alternatively you can use ``ezd`` command which is a short-cut command for installing Docker on Atmosphere

.. code-block:: bash

	$ ezd

Now run the following command to run the docker image from Dockerhub

.. code-block:: bash

	$ docker run --rm upendradevisetty/fortunecowsaylolcat2:1.0

.. Note::

	You don't have to run ``docker pull`` since if the image isn’t available locally on the machine, Docker will pull it from the repository.
