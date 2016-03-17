##################################
Running in a container environment
##################################

Learning objectives: learn about Docker containers, and how they can help deal
with dependencies.

.. note::

   To go through the following, you'll need to have Docker installed.
   For most of it, you'll also need to have an Amazon Web Services
   account.

(This is an abbreviated & focused version of a 2-day Docker workshop
that I ran at UC BIDS; `see the full notes
<https://github.com/ngs-docs/2016-bids-docker/blob/master/AGENDA.md>`__)

Introduction: dependencies, and containers
------------------------------------------

If you think back to our original overview, we've got three of the four
workflow components in place - we've got a workflow that takes our raw
data and converts it into a summary, we've got an analysis notebook that
generates results from that data, and we've put it in git.
           
.. thumbnail:: images/overview-2-highlighted.png
   :width: 20%

But, in order to run it, we still need a lot of things installed.  In
this case, we don't have that weird a stack of software: we need
Python, make, Jupyter Notebook, and wordcloud - which depends on numpy
and matplotlib.  Not terribly.

But, hypothetically, we could have *many* dependencies - for example,
bioinformatic workflows often involve dozens of dependencies, with
things written in Java and Ruby and Perl and ...

Installing all of these dependencies on your own laptop may be
difficult enough, but it becomes nearly impossible in shared
environments like lab workstations and HPCs, where you generally don't
have admin/install privileges, and where you have to be concerned
about conflicts between dependencies because it's a shared environment.

This is where isolated environments come in.  These allow you to install
a collection of software in such away that it only interacts within that
collection.

There's a bunch of ways to do this, ranging from language-specific
(virtualenv in Python) to whole-machine (virtual machines and cloud).

Docker containers are a new-ish way to do this.  Docker basically
provides a lightweight virtual machine that is quick and easy to start
up, and the Docker ecosystem includes ways of specifying
configurations and also shipping these fully configured "containers"
around.

A super quick introduction to Docker
====================================

Before Getting Started
----------------------

Can you run this? ::

   docker run hello-world

and see something like this? ::

   Hello from Docker.
   ...

If that works, you should do::

   docker pull ubuntu:14.04

If that doesn't work, you `might need
<https://stackoverflow.com/questions/28301151/docker-pull-error>`__ to
run this to reset your local docker install::

   docker-machine restart default && eval "$(docker-machine env default)"

Getting started
---------------

Try::

   docker run ubuntu:14.04 /bin/echo 'Hello world'

Now try::

   docker run -it ubuntu:14.04 /bin/bash

Q: What does the '-it' do?

Points to cover:

* you can use CTRL-D or type 'exit' to leave the container
* "images" are the disk, "containers" are the running bit

----

Try creating a file in your container; first run::

   docker run -it ubuntu:14.04 /bin/bash

and then *inside the docker container* run::

   echo hello, there > /home/foo.txt

Now, exit the container.  Make note of the container ID if you can -
it's the string right after 'root@' and before the ':', and should be
something like '003eafc0422c'.

First, if you run 'docker run ubuntu:14.04 -it /bin/bash' you will see
that the file is not there::

   cat /home/foo.txt

This is because every container starts fresh from the base image (here,
ubuntu:14.04).

However, you can still access files from old containers (unless you specify
'--rm' when you run things). Here, ::

   docker cp 003eafc0422c:/home/foo.txt .

will copy the file into your current directory.
  
So: data is transient, unless you make other provisions (we'll talk
about this later!)

----

You can get a list of running containers by doing::

  docker ps

You can get a list of *all* containers (running and stopped) by doing::

  docker ps -a

You can get a list of all *images* by doing::

  docker images

You can remove a container with 'docker rm', and remove an image with
'docker rmi'.  The 

Two handy commands to clean up (a) stopped containers and (b)  ::

  docker rm $(docker ps -a -q)
  docker rmi $(docker images | grep "^<none>" | awk "{print $3}")

Using docker-machine to run Docker on AWS
=========================================

Documentation: https://docs.docker.com/machine/; also see `Amazon Web
Services driver docs <https://docs.docker.com/machine/aws/>`__

Here, we're going to use Amazon to host and run our Docker images, while
controlling it from our local machine.

Start by logging into the `AWS EC2 console <https://console.aws.amazon.com/ec2/v2/home>`__.

Find your AWS credentials and your VPC ID.

* your AWS credentials are `here <https://console.aws.amazon.com/iam/home?region=us-east-1#security_credential>`__, and if you haven't used them before you
  may need to "Create a New Access Key".  (Be sure not to store these in a place
  that other people can view them.)

  Your AWS

* to get your VPC ID, go into https://console.aws.amazon.com/vpc/home and
  select "Your VPCs".  Your VPC ID should look something like vpc-9efe1afa
  (that's mine and won't work for you ;)

Then, set your AWS_KEY and AWS_SECRET and VPC_ID; on Linux/Mac, fill in
the values and execute::

  export AWS_KEY=
  export AWS_SECRET=
  export VPC_ID=

...not sure what to do on Windows, maybe build the command below in a text
editor?

Then, run::
  
  docker-machine create --driver amazonec2 --amazonec2-access-key ${AWS_KEY} \
        --amazonec2-secret-key ${AWS_SECRET} --amazonec2-vpc-id ${VPC_ID} \
        --amazonec2-zone b --amazonec2-instance-type m3.xlarge \
        aws

and to connect to it, do::

  eval $(docker-machine env aws)

and now you can run all the 'docker' commands as you would expect, EXCEPT
that your docker host is now running Somewhere Else.

If you have trouble getting a subnet, make sure that your VPC has subnets
in the zone/region you're trying to use. You can set these with::

    --amazonec2-region "us-east-1" --amazonec2-zone b

Take a look at the help for the EC2 driver here::

  docker-machine create --driver amazonec2 -h | less

Things to discuss:

* diagram out what we're doing!
* docker-machine manages your docker host; docker manages your
  containers/images ON that host.
* talk about AWS host sizes/instance types: https://aws.amazon.com/ec2/instance-types/
* explain docker client, docker host, docker container relationship
* also include -p, -v discussion.

---

You can use 'docker-machine stop aws' and 'docker-machine start aws' to
stop and start this machine; with AWS, you will need to do a
'docker-machine regenerate-certs aws' after starting it in order to
connect to it with docker-machine env.

To kill the machine, do 'docker-machine kill aws'.  This will also, I believe,
trash the configuration settings so you would need to reconfigure it
with a 'create'.

Note that while the machine is running or stopped, you should be able
to see it at the `AWS EC2 console
<https://console.aws.amazon.com/ec2/v2/home>`__.

----

Let's talk more about why you would want to do *this* :).

Also, diagrams!

Building your own Docker image (by writing a Dockerfile)
========================================================

On your local machine, create a new (empty) directory called
'wordcloud-image'.  In that directory, create a file 'Dockerfile'
that contains::

   FROM jupyter/notebook
   RUN apt-get update && apt-get -y install python-matplotlib python-numpy \
       unzip

and then execute::

   docker build -t wordcloud-image .

Now run it::

  docker run -it -p 9000:8888 -v wordcloud-image

and in the docker container you should be able to execute your entire
workflow from within the notebook:

In the terminal,

* check out your source code;
* change into the directory;
* run make;

In the notebook,

* run the notebook analysis.

----
  
Return to index: :doc:`index`
