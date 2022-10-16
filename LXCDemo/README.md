# Introduction to LXD projects

- [Introduction to LXD projects](#introduction-to-lxd-projects)
  - [Overview](#overview)
    - [Requirements](#requirements)
  - [Installing LXD Snap](#installing-lxd-snap)
  - [Creating our first project](#creating-our-first-project)
  - [Creating containers in our project](#creating-containers-in-our-project)
  - [Listing projects](#listing-projects)
  - [Listing containers in a project](#listing-containers-in-a-project)
  - [Creating further isolated projects](#creating-further-isolated-projects)
    - [A new client project](#a-new-client-project)


## Overview

- LXD is a container hypervisor providing a REST API to manage LXC containers. It provides a virtual machine like experience without incurring the overhead of a traditional hypervisor.

- However when you are managing lots of containers providing different services, it can become confusing to see which containers are dependent on each other.

Th- e projects feature is designed to help in this situation by providing the ability to group one or more containers together into related “projects” that can be used with the lxc tool.

- This demo will show how to create LXD projects, add containers into a project and explore the features that can be specified at a project level.

### Requirements

- *LXD snap installed and running (although we will cover this briefly if not)*
- *You should know how to create and launch a LXD container*

## Installing LXD Snap

We will need LXD installed and running before we can use it to create a project.

The easiest way to stay up to date with LXD is to use the Snap package.

We can install LXD using Snap as follows:

```console
snap install lxd
```

If you are running Ubuntu 18.04 LTS or earlier then you may already have LXD installed as an apt package.

You can migrate your containers to the newer Snap package and remove the old packages by running:

```console
lxd.migrate -yes
```

If this is the first time you have used LXD, then you also need to initialise it.

```console
lxd init --auto
```

Lets check that LXD is working by running:

```console
lxc ls
```

## Creating our first project

Imagine we have a scenario where we are setting up a web site for a particular client. We want to create two containers; one for the web service and one for the database service. Because we have lots of different web sites for different clients we want to ensure that we can quickly see and manage all of the containers for this particular web site.

Projects help us in this situation because we can create a project called client-website and add the containers to it that are related to providing the client’s web site.

```console
lxc project create client-website -c features.images=false -c features.profiles=false
```

In the above command we have passed the `-c features.images=false` and `-c features.profiles=false` flags so that our new project will still use the images and profiles of the default project and will not namespace them (we’ll come onto that some more later in this demo).

Now we switch the lxc tool into that project.

```console
lxc project switch client-website
```

Let’s check that we have switched to the new empty project:

```console
lxc ls
```

We would expect to see an empty list at this point.

## Creating containers in our project

Now that we have an empty project, we can go ahead and create our two containers (the web server and the database server).

```console
lxc launch ubuntu:18.04 webserver
lxc launch ubuntu:18.04 dbserver
```

Lets check we now have those containers created:

```console
lxc ls
```

We should expect to see output similar to this:

```console
+-----------+---------+----------------------+-----------------------------------------------+------------+-----------+
|   NAME    |  STATE  |         IPV4         |                     IPV6                      |    TYPE    | SNAPSHOTS |
+-----------+---------+----------------------+-----------------------------------------------+------------+-----------+
| dbserver  | RUNNING | 10.140.78.128 (eth0) | fd42:bc8c:c2b1:32fa:216:3eff:feb8:2bec (eth0) | PERSISTENT |           |
+-----------+---------+----------------------+-----------------------------------------------+------------+-----------+
| webserver | RUNNING | 10.140.78.202 (eth0) | fd42:bc8c:c2b1:32fa:216:3eff:fea5:e8a9 (eth0) | PERSISTENT |           |
+-----------+---------+----------------------+-----------------------------------------------+------------+-----------+
```

## Listing projects

Sometimes you don’t know which project you want to work in, so LXD allows you to list the projects we have.

Lets list our projects so that we can see our client-website project in relation to the default project:

```console
lxc project ls
```

You should see output that looks like this:

```console
+--------------------------+--------+----------+---------+
|           NAME           | IMAGES | PROFILES | USED BY |
+--------------------------+--------+----------+---------+
| client-website (current) | NO     | NO       | 2       |
+--------------------------+--------+----------+---------+
| default                  | YES    | YES      | 4       |
+--------------------------+--------+----------+---------+
```

## Listing containers in a project

At this point lets reflect on how our project behaves in relation to the other projects by experimenting with ways we can list the containers within them.

Let’s switch back to the default project (this is the project that LXD uses normally).

```console
lxc project switch default
```

To prove that we can create a container of the same name as one in another project, lets create another `webserver` container:

```console
lxc launch ubuntu:18.04 webserver
```

Now lets list our containers in the current default project:

```console
lxc ls
```

You should expect to see a list of all of your existing containers, including the webserver we just created. Importantly, however, the list should not contain the two `client-website` containers we created earlier.

```console
+-----------+---------+----------------------+-----------------------------------------------+------------+-----------+
|   NAME    |  STATE  |         IPV4         |                     IPV6                      |    TYPE    | SNAPSHOTS |
+-----------+---------+----------------------+-----------------------------------------------+------------+-----------+
| tutorials | RUNNING | 172.17.0.1 (docker0) | fd42:bc8c:c2b1:32fa:216:3eff:fec4:1b34 (eth0) | PERSISTENT |           |
|           |         | 10.140.78.67 (eth0)  |                                               |            |           |
+-----------+---------+----------------------+-----------------------------------------------+------------+-----------+
| webserver | RUNNING | 10.140.78.34 (eth0)  | fd42:bc8c:c2b1:32fa:216:3eff:fea9:5ea (eth0)  | PERSISTENT |           |
+-----------+---------+----------------------+-----------------------------------------------+------------+-----------+
```

Now lets list the containers in our client-website project, but without switching into it.

```console
lxc ls --project client-website
```

This should give you the output:

```console
+-----------+---------+----------------------+-----------------------------------------------+------------+-----------+
|   NAME    |  STATE  |         IPV4         |                     IPV6                      |    TYPE    | SNAPSHOTS |
+-----------+---------+----------------------+-----------------------------------------------+------------+-----------+
| dbserver  | RUNNING | 10.140.78.128 (eth0) | fd42:bc8c:c2b1:32fa:216:3eff:feb8:2bec (eth0) | PERSISTENT |           |
+-----------+---------+----------------------+-----------------------------------------------+------------+-----------+
| webserver | RUNNING | 10.140.78.202 (eth0) | fd42:bc8c:c2b1:32fa:216:3eff:fea5:e8a9 (eth0) | PERSISTENT |           |
+-----------+---------+----------------------+-----------------------------------------------+------------+-----------+
```

You can see that we now have 2 containers called `webserver`, but with different IP addresses and inside different projects.

## Creating further isolated projects

So far we have created a project that allowed containers of the same name to be created in different projects.

However both the `default` and `client-website` projects shared the same profiles and image libraries.

This means that the the `default` profile in client-website project is the same as the `default` profile in the `default` project.

But there are scenarios where we may want to further isolate a project such that profile and image names are not shared beyond it.

### A new client project

Lets imagine we have now taken on a new client and we need to create a new project for them. However this client requires a different network configuration than the previous client required.

We could create a new global profile called `client2-default` with the required networking configuration and then assign that profile to the containers in this new project.

However this would mean that the `client2-default` profile would be accessible to all containers that shared the `default` project (including our previously created client-website project). This would be confusing.

Instead, lets create a new project called `client2-website`, but this time specify that it should not share the `default` project’s profiles and image library.

```console
lxc project create client2-website -c features.images=true -c features.profiles=true
```

This is equivalent to running just:

```console
lxc project create client2-website
```

Now lets create a container in the new project:

```console
lxc launch ubuntu:18.04 webserver --project client2-website
```

***Note**: We are using the --project flag on the lxc launch command to save switching into the new project.*

Oops! Something went wrong though, you will get this output:

```console
Creating webserver
Error: Failed container creation: Create container: Create LXC container: Invalid devices: Detect root disk device: No root device could be found
```

Continue to the next step to find out what is going wrong!