# Containers

RHEL9 container engine: CRI-o (used to be Docker in earlier versions)

Orchestration engine: OpenShift (not needed for this exam)  This is the RedHat's own implementation of Kubernetes
main benefit: it can run containers in user space: *rootless container*
They are strictly separated from other user's containers (from root as well)
This implies extra hassle: *rootless containers*
* can not bind privileged port
* can not have own IP

main command to manage containers: *podman* (used to be docker)

Other commands:
* *buildah*  builds custom container images
* *skopeo*    container image query, manage, handle (p.ex: inspect image w/out downloading)

## Basic supporting infrastructure elements 
* **namespaces** - separating namespaces is a key feature in containerization
  * *mount namespace*  -- kinda like *chroot*, it cannot escape the mounting dir
  * *process namespace* -- processes in the container are not aware of other processes (and vica versa)
  * *network namespace* -- like a VLAN, separating network traffic
  * *user namespace* -- there are independent users in the container - *id_map*-ed to the host system
  * *ipc namespace* 

* **control groups** - process resource management 
  * There is no default limitations, but can be. CPU and mem too

* *SELinux*  - authorization
  *  Ubuntu has *AppArmor*, probably they use that


## Softvare components

*container-tools* package has to be installed. This contains podman, skopeo, bildah an others.

## Configuration
* Root level
  * /etc/containers/registries.conf
  * /etc/containers/registries.conf.d - setting short names for containers, ...
  * /etc/containers/registries.d - defining registries
* nonprivileged user level
  * ~user/.local/share/containers - similar structure - can override settings from /etc/containers on a per user basis

## Running containers

Most basic version:
`podman run nginx` - this instructs podman to search something called *nginx* with *latest* tag, then download it as an image, then instantiate it and the run the instance. Most probably you will need to select from registries. 

More specific version:
`podman run docker.io/library/nginx` - this downloads image from the specified registry. You should specifí this way.

There are *entrypoint* and/or *CMD* parameters defined in the container image. These are the default actions on start. First is entrypoint and then CMD. 

**QUESTION: what is the difference between *entrypoint* and *CMD*?**

**QUESTION: what is a *layer* exactly?**

Both *entrypoint* and *cmd* parameters can be read from container images with `podman inspect <image_name>`. You can also use `skopeo`
These can be overridden on start. 
* `--entrypoint` option for *entrypoint*
* CMD can be specifien at the end of the command line `podman run -it nginx /bin/bash` - the bash will be run as CMD
**CLARIFICATION NEEDED**

### Run containers in detached mode
This is the usual mode for containers: running like daemons:
`podman run -d name_of_image`

### Run containers in interactive mode
You can have a shell inside the conatinar, and so on
`podman run -it name_of_image`
Also you can detach from a container and let it run in background - like in *tmux*. The key combo is: *CTRL-P - CTRL-Q*
You can reattach to such a container using `podman attach container_id`

### User id-s in running containers

Podman is superior of docker as it is *daemonless* and  can run containers in user mode: *rootless containers*. There is an UID mapping between host and container.


### Setting fs rights for shared directories

**TODO** 
 
## Querying images

`podman ps` gives a list of running container instances
`podman ps -a` includes all instances that stopped already

`podman images` lists all available container images in the *local storage*

## Deleting stuff

`podman rm container_name/container_id` removes a specific instance
`podman rmi image_name/image_id` removes an image from local storage. You can only remove images wich do dot have any instances (listed in podman ps -a)

# storage directories:
* /run/containers/storage -- root transient storage
* /var/lib/containers/storage -- root permanent
* /home/username/.local/share/containers/storage   -- normal user - this has to be under the user's home directory (for SELinux reasons) 

## Getting extra info
`podman info` 
Displays a lot of settings, including the list of defined registries

`podman search --compatible ...string... `
Searching for images with name matching to ...string...  
with `--comatible` also shows ratings (stars).  (compatible to docker registries)

## Building custom container images
Create a directory, switch to it ad create a file named `Containerfile` 

`podman build -t name_of_new_image:tag_of_new_image .`  - note the `.` at the end!
This processes the contents of *Containerfile* and save the result as a new image to the local storage. 
In the background `buildah` command is called.

## Example Containerfile
```
FROM registry.access.redhat.com/ubi9/ubi:latest
RUN dnf -y install httpd; dnf clean all; systemctl enable httpd;
RUN echo "Successful Web Server Test" | tee /var/www/html/index.html
RUN mkdir /etc/systemd/system/httpd.service.d/; echo -e '[Service]\nRestart=always' | tee /etc/systemd/system/httpd.service.d/httpd.conf
EXPOSE 80
CMD [ "/sbin/init" ]
```

## Running containers as a service
Podman has the option to generate *service unit* files for systemd. This can be done both root containers and rootles ones.  
For root containers you have to generate service file in the `/etc/systemd/user` directory and then reload systemd and enable the new service. 
 
For rootless containers, the process is longer:
* As root, turn on lingering for the user: `loginctl enable-linger cindy`
* **Log in with that user!** Su or sudo will not work! 
* Create your container as above, using `podman run --rm --name cindy-web ...`. Let's use name *cindy-web*. `--rm` should be added, to free up the name on shutdown. 
* Create a directory for systemd unit files: `mkdir -p ~/.config/systemd/user`
* **NOW the essence:** Create the service file with `podman generate systemd --new --name cindy-web > ~/.config/systemd/user/cindy-web.service`
* stop and delete cindy-web container
* Load the newly generated systemd user config: `systemctl --user enable --now cindy-web.service`
* Now you should see your container running, started by systemd. Reboot, and check that the container starts automatically. 


## Behind the scenes

Setting correct SELinux type for the mounted directories: **THIS IS NOT WORKING, use :Z at the end of directory mounts instead**
`semanage f_ -t container_file_t my_dir_to_be_mounted`
`restorecon -Rv my_dir_to_be_mounted`

Containers to be able to started by systemd this boolean should be enabled
`setsebool -P container_manage_cgroup on`