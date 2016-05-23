# Packaging to Docker

Cowardly borrowed from https://docs.google.com/presentation/d/1pviQPpwCdQ0vp74B2p7B2Ax-VpzT_mvj-O2n0uNZhBY/edit#slide=id.gd8f0e74b8_1_567

## Proper Linux Process

Containers should behave as a proper Linux process 
and be nice to their init process.

React to signals:
* React on e.g. SIGINT, SIGTERM, etc.
* Don’t daemonize your processes
* Make your process foreground (e.g. use exec)
 
Return proper exit codes:
* 0 (OK), 1 (General error) …

Use stdin, stdout, stderr:
* Log to stdout. Don’t concern with routing and storage

### PID 1 problem

https://blog.phusion.nl/2015/01/20/docker-and-the-pid-1-zombie-reaping-problem/

## Explicit interfaces

Dependencies to other containers should be made explicit by defining its interfaces.

* CLI arguments
    * Use a lib for parsing / validating
* Environment variables
    * Set defaults in the image
    * Overwrite with `docker -e` 
* Network / Ports
    * Expose port via EXPOSE in Dockerfile, because docker store it in metadata (useful with `-P` flag).

## Disposable Containers

Containers should be treated as disposable artefacts. The application shouldn’t rely on a particular container instance to be running.

Pets vs. Cattle: 
Treat your container as part of a cattle. You number them and when get sick you shoot them.

* Only keep ephemeral state
    * Don’t assume this state between two requests
* Robust against sudden death
    * If the container gets interrupted pass on your current job.
* Minimal setup
    * If more setup needed let the scheduler know 

Acknowledge the fallacies of distributed computing

## Immutable

Once a container image is build it shouldn’t be changed. State should be extracted and changes to the container should be applied by rebuilding.

* Strive for dev / prod parity
* Extract runtime state in volumes
* Anti-pattern: docker exec

## Self-contained

The container should only rely on the Linux kernel. All other dependencies should be made explicit and added dynamically.

* Strive for zero-config deployment
* Add dependencies at build time
    * Build Uber-Jar and include webserver
* Generate dynamic config files on the fly
* Anti-Patterns: 
    * Put config into a volume
    * Put code into a volume *

## Small

A container should have the least amount of code possible to fulfill its job.

* Build from scratch 
* Use small base-image
    * busybox, alpine
* Reuse custom base image
* Anti-Pattern: VM Container

## Docker compose

```
my-app:
  restart: always
```

Don't use `restart: always` policy for development, it's crucial to know when
your application if died.


## Links

* http://blog.james-carr.org/2013/09/04/parameterized-docker-containers/
* https://docs.docker.com/articles/dockerfile_best-practices/
* http://tldp.org/LDP/abs/html/exitcodes.html (Exit Codes for “Proper Linux Process”)
* http://www.theregister.co.uk/2013/03/18/servers_pets_or_cattle_cern/ (Pets vs Cattle)
* http://www.projectatomic.io/docs/docker-image-author-guidance/ (Dockerfile)
* http://www.hokstad.com/docker/patterns (Dev patterns)
* http://blog.kubernetes.io/2015/06/the-distributed-system-toolkit-patterns.html (Composite Patterns)
* http://static.googleusercontent.com/media/research.google.com/de//pubs/archive/43438.pdf (Borg by Google inspiration for Kubernetes / Pods)
* http://techblog.netflix.com/2014/11/prana-sidecar-for-your-netflix-paas.html (Sidecar Netflix)

