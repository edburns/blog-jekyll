---
layout: post
title:  Ray Tsang Docker Tips and Tricks
date:   2017-02-23 17:36 -0500
comments: true
---

Twitter [@saturnism](http://twitter.com/saturnism).

Started with his desert oasis story.  He wants to help you avoid the
four hour walk.

- - -

*Established the itch which Docker scratches.*

Package and deployment: old way: application.war and application.ear,
etc.  There was no other way to do it.  You also had to dead-eye the
resource allocations: procurement.  It's the pet's vs. cattle thing.

Machine
* app.war / app.ear
* App server
* Shell / CLI / Tools
* Kernel

And after all that, it probably doesn't run perfectly the first time.
Or you get environment sensitive issues: works in dev, not in prod.

- - -

Old way: shared machine: no isolation, shared filesystem.  Maybe use
chroot?  *Uses chroot to teach one aspect of containers.  Uses tar as a
poor man's Docker image.  Builds the case for what Docker is.*  

- - -

Old way: Virtual Machines: some isolation, expensive and inefficient,
still highly coupled to the guest os.  *Showed some problems with VM
approach*: need to use lots of packager stuff to get the machine ready
to use.  *Still establishing the itch.*

- - -

New way: finally introduces Docker.  Shows how it scratches the
established itches.  Simple Dockerfile example, that solves all the
"packager stuff" from the previous section.  Mentions the filesystem
layer created for each line.

Created a war live coding style with SpringBoot and Groovy.
@RestController.

    spring war helloworld.war

TIP: Use COPY instead of ADD in your Dockerfile.

TIP: If using apt-get in Dockerfile, make sure to use -y.

Then he does a docker build.

Quickly showed the -p option.

Showed how to optimize the size of the docker image.

TIP: Try to do as much as you can on a single line, as long as it makes
logical sense.  Also, remove `/var/lib/apt/lists/*`  Remove any
intermediate tar files.

TIP: Put the COPY command on the bottom because if you update the war,
then all the subsequent lines will cause the filesystem layer to be
re-built.  Talked about the caching in the docker server with respect to
the building of the docker images.  He suggest pinning to a specific
version of things.  

- - -

*TIP: He finally mentions the default "run as root" problem.*

He uses useradd or adduser in the Dockerfile.

    RUN useradd tomcat
    USER tomcat

TIP: Use `curl url | tar zxvf -`.  This removes the need for the removal
of the tar.x`

- - -

TIP: docker history command.  Shows the size of the filesystem layers.

- - -

Talks about using the image as a custom base image.

    FROM tomcat-base-image

Because of the layers, the base image does not consume more space the
more images are built from it.

TIP: Why am I getting out of space all the time?  When you run a
container, it is going to instantiate that image.  This includes a
filesystem layer for that image.  When you stop the image, the
filesystem layer for that image still sticks around.  You need to delete
that image when you are done.  `*docker run -ti --rm*`  This causes the
layer for the image to be removed when the container stops.

TIP: `docker system prune`.  Also checkout spotify's docker-gc.  If the
container is not running, let me delete it, because the image is stored
in a registry anyway.

- - -

What about logs?  The longer you run it, the more logs you are going to
have.  There is no automatic log rotator.

TIP: Whatever you do, don't store the logs in the container's
filesystem.  But what about when you are running multiple containers?
Recommendation: don't write the logs to the disk at all.  Use forwardd.
Send the logs to stdout and use the centralized logging system.

TIP: Don't chown in Dockerfile.  It doubles the container size.

- - -

Recommends the spotify docker maven plugin.

TIP: ENTRYPOINT should go on the top, since it doesn't change often.

TIP: buildnumber-maven-plugin, puts the git commit hash in your tag.

- - -

TIP: Know what is in that public container you get from Dockerhub.
Containers are not security boundaries.  Non-updated containers can have
vulnerabilities in their dependencies.

TIP: Try stacksmith.  Creates a Dockerfile, but shows vulnerabilities in
non updated dependency versions.

TIP: SecureRandom is slow.  `-Djava.security.egd=file:/dev/urandom`

TIP: How many CPUs.  Don't trust Runtime.availableProcessors().  See
[the RedHat script](https://github.com/fabric8io-images/run-java-sh).  java
-XX:+PrintFlagsFinal -version | grep HeapSize

[https://medium.com/google-cloud/my-slow-internet-vs-docker-7678ae1cae72#.w5on3gqzh](https://medium.com/google-cloud/my-slow-internet-vs-docker-7678ae1cae72#.w5on3gqzh)

