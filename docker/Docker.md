# Docker Study Notes

## Core Concepts

### Containers vs VMs

- **Container**: kernel &rarr; runtime &rarr; container (glibc gives the container the identity of a specific OS)
- **VM**: host kernel &rarr; hypervisor &rarr; each VM has its own kernel
- Container runtime (`/usr/bin/containerd-shim-runc-v2`) runs on a host platform and establishes communication between the localhost kernel and the container, because containers don't have a kernel by themselves.
- In the Docker world, the **atomic unit of scheduling is a container**. In virtualization it's a VM. In Kubernetes it's a pod.
- VMs on a hypervisor virtualize the hardware resources. Containers virtualize OS resources (filesystem, network).

### OCI & Compatibility

- OCI standardization ensures compatibility between containers regardless of which environment they originally come from. For instance, images made for Docker work without modification in Red Hat Podman.

### What Is a Container?

- An isolated area of an OS with resource usage limits applied, built using **control groups** and **namespaces** (Docker Engine handles that).
- Contains: code, config, processes, networking, dependencies, and enough OS to run.
- A container is a thin writable layer on top of a read-only image.

---

## Docker Engine Architecture

Docker Engine is the heart of Docker, designed as a client-server application:

1. **Docker Daemon** &mdash; the Docker server itself, installed on the docker host (desktop, VM, or cloud).
2. **REST API** &mdash; defines the interface that other programs use to talk to the daemon.
3. **Docker CLI (client)** &mdash; commands you run to manipulate images and containers.

- The client sends commands to the daemon. It can be on the same host or remote.
- Client and daemon communicate over a socket.

### Engine History

- Docker originally was a daemon on top of AUFS and LXC (interfacing cgroups and namespaces).
- LXC changed constantly and kept breaking Docker, so Docker replaced it with **libcontainer**.
- The monolithic daemon was later broken into multiple components:
  - Client &rarr; REST API &rarr; Daemon &rarr; gRPC API &rarr; **containerd** (execution/lifecycle, starts a shim process for every container) &rarr; **OCI runtime** (interfacing with kernel; on Linux, **runc** is the OCI implementation).
- This decouples running containers from the daemon &mdash; you can stop/restart/upgrade the daemon without affecting running containers.

### Engine Modes

- **Single-engine mode**: work with each container independently.
- **Swarm mode**: work with multiple containers as a cluster.

---

## Images

- An image is a tar file with associated metadata &mdash; a **manifest** and a set of loosely coupled layers.
- Layers are managed by the **overlayfs** driver.
- Each layer contains OS files/objects, app files, and a manifest describing all layers.
- You can refer to an image by its **repository:tag** (name and version), or by **Image ID** if it has no name/version.
- Images and containers have their own separate sets of IDs.

### Pulling Images

- `docker image pull <name>` makes a request to the registry, which returns a **fat manifest** containing images available for each architecture, each with its own manifest.
- Layers are stored in `/var/lib/docker/<storage-driver>/`.
- Docker uses the kernel of the host OS but with the filesystem of the image OS.

### Content Addressable Storage

- Docker hashes the content of each layer and identifies it by that hash, ensuring you always get the correct layer.
- A layer has: (1) hash of its content on the host, (2) hash of the compressed version on the registry, (3) a random ID on the filesystem.

### Building Images

- `docker build -t <image-name> <path-to-Dockerfile>`
- Each line in the Dockerfile takes the image from the previous line and produces another image. The previous image is unchanged.
- When building, Docker packages the **entire build context directory** and sends it to the engine (not just the Dockerfile).

### Build Cache

- Each Dockerfile step is cached. Docker checks if the same combination (current image + next step) already exists; if so, it reuses it.
- The cache breaks if you add extra spaces or change parameter order (e.g. `apt-get install figlet` != `apt-get install  figlet`).
- `docker build --no-cache` to skip the cache.
- **Tip**: put the most frequently changing parts at the **end** of the Dockerfile to maximize cache hits.

### Multi-Stage Builds

Benefits of smaller images:
- Faster builds and deployments
- Less money spent on storage
- Less attack surface (minimum number of packages)

### Image Trust

- **Automated** builds are done by the container registry itself (verifiable) &mdash; more trust.
- A blank/manual build means someone built it on their machine and pushed it to the registry.

### Docker EE Registry

- Docker EE has **Docker Trusted Registry (DTR)** for on-premises use.

---

## Dockerfile Instructions

### RUN

- Executes a command and records changes to the filesystem. Great for installing libraries, packages, and files.
- Does **not** record state of processes or automatically start daemons (use `CMD` or `ENTRYPOINT` for that).
- Uses `/bin/sh -c` by default:
  ```
  RUN apt-get update
  # equivalent to: exec("sh", ("sh", "-c", "apt-get update"))
  ```
- Use exec form if your image has no shell:
  ```
  RUN ["apt-get", "install", "figlet"]
  ```
- Chain multiple commands with `&&` (not `;`) to catch errors:
  ```
  RUN apt-get update && apt-get install -y figlet
  ```
- Avoid large files spanning multiple lines or the image will be huge.

### CMD

- Runs after the image starts if no other command is passed at runtime.
- Runtime arguments **override** `CMD` instructions.

### ENTRYPOINT

- The first program run in the container. Makes containers look like normal programs.
- Can be overridden at runtime with `--entrypoint <command>`.
- Runtime arguments are **appended** to `ENTRYPOINT` as arguments.
- If both `ENTRYPOINT` and `CMD` are used, `CMD` acts as default arguments for `ENTRYPOINT`.
- The entrypoint process **must run in foreground** or the container will exit immediately. For example, nginx daemonizes itself by default &mdash; when it forks to background, the entrypoint process exits and the container stops.

### COPY

- Cannot refer to a file outside the build context.
- `COPY /temp/ /` does **not** refer to the root filesystem &mdash; it refers to `temp/` inside the build context.

### ADD

- If used to download a file, Docker re-downloads it every build (Docker doesn't use ETags or If-Modified-Since). The cache is used for subsequent layers only if the downloaded file hasn't changed.

### ENV

- Set environment variables in the image:
  ```
  ENV DB_HOST=db.ex.com
  ```

### WORKDIR

- Like `cd` &mdash; sets the working directory for subsequent instructions.

### EXPOSE

- Equivalent to `-p` when running the container.
- Avoid defining shared folders in Dockerfile &mdash; if you do, you can only run the image on your machine.

---

## Container Lifecycle

### Running Containers

```
docker run <image>                                    # run an image
docker run --name <name> <image>                      # run with a custom name
docker run --rm <image>                               # auto-remove container on exit
docker run -d <image>                                 # run in background (detached)
docker run --memory <max-memory> <image> <command>    # set memory limit
docker run --cpu-shares <relative-shares> <image>     # set CPU shares relative to other containers
```

- `-d` stands for DAEMON (Disk Asynchronous Execution MONitor).
- Ending a Linux command with `&` runs it in the background.

### (Image) &rarr; `docker run` &rarr; (Running Container)

### Attaching / Detaching

- Detach from a running container: `Ctrl+P` then `Ctrl+Q`
- Re-attach: `docker attach <container-name>`

### Executing in a Running Container

```
docker exec <container-name> <command>
```

### Stopping & Removing

```
docker stop <container-name>    # sends SIGTERM, waits 10s, then SIGKILL
docker kill <container-name>    # sends SIGKILL immediately
docker rm <container-name>      # remove a stopped container
docker rm $(docker ps -aq)      # remove all stopped containers
```

### Committing a Container to an Image

```
docker commit <container-name> <new-image-name>
```

### Inspecting Containers

```
docker inspect <container-name>
docker inspect --format='{{.State.Pid}}' <container-name>               # get parent PID
docker inspect --format='{{json .NetworkSettings.IPAddress}}' <container-name>  # get IP
```

### Logs

```
docker logs <container-id>                  # print container stdout
docker logs <container-id> -f               # follow logs
docker logs <container-id> -f --tail <n>    # follow starting from last n lines
```

### Resource Usage

```
docker container stats <name>          # live resource usage
docker ps -s                           # show read-write layer size per container
```

### Export & Copy

```
docker export <container-id>                              # export entire filesystem
docker diff <container-id>                                # show file changes vs image
docker cp <container-id>:<path> <local-path>              # copy a file out of a container
```

### Port Info

```
docker port <container-name>      # show port mappings
docker run -P <image>             # expose all ports declared in the image
```

---

## Networking

- Containers are isolated from the internet by default.
- You can group containers into private networks and explicitly choose who connects to whom.
- To refer to the host machine from inside a container: `host.docker.internal`

### Network Drivers

Selected with `docker run --net <driver>`:

| Driver | Scope | Description |
|--------|-------|-------------|
| **bridge** (default) | Single host | Containers on the same machine |
| **overlay** | Multi-host | Distributed (swarm) |
| **macvlan** | Multi-host | Each container gets its own IP and MAC (requires promiscuous mode) |
| **host** | Single host | Full access to host network stack |
| **none** | Single host | No networking at all |
| **container** | Single host | Shares network stack of another container |

### Bridge (Default)

- Container gets a virtual `eth0` interface + its own loopback, provided by a **veth pair**.
- Connected to the Docker bridge (`docker0`, configurable with `--bridge`).
- Addresses allocated on a private internal subnet (default `172.17.0.0/16`, configurable with `--bip`).
- Outbound traffic: `iptables MASQUERADE` rule (NAT).
- Inbound traffic: `iptables DNAT` rule.
- Docker does **not** use DNS in the default bridge network.
- Legacy linking only operates in one direction.

### Host

- Container sees and accesses host network interfaces directly.
- Can bind any address, any port.
- Traffic doesn't go through NAT, bridge, or veth &mdash; **Performance = native**.
- Use cases: performance-sensitive apps (10+ Gbps), VOIP, gaming, streaming, peer discovery.

### None

```
docker run --net none <image>
```
- Only gets the `lo` loopback. No `eth0`.
- Can't send or receive traffic.
- Useful for isolated/untrusted workloads.

### Container

```
docker run --net container:<id> <image>
```
- Reuses the network stack of the specified container (same interfaces, IP addresses, routes, iptables).
- Containers can communicate over `lo` (one can bind `127.0.0.1`, the other can connect to it).

### DNS & Service Discovery

- Names and aliases can be resolved via an embedded DNS server.
- `--net-alias` sets a name in the DNS registry.
- To communicate between two separate Docker networks, you can only expose ports.

### Host Network Flag

```
docker run --net=host <image>   # full access to host network stack, no protection
```

### Ambassador Pattern

- Ambassador containers proxy for another service, abstracting connection details.
- Helps with: discovery, migration, failover, load balancing, authentication.

---

## Volumes & Storage

### Types

- **Persistent**: data remains on the host even after the container exits.
- **Ephemeral**: exists only as long as a container is using it; evaporates when no container uses it.
- Volumes never become part of images.

### Usage

```
docker run -ti -v <host-path>:<container-path> <image>                # bind mount
docker run -ti -v <path> --name <name> <image>                        # named volume
docker run -ti --volumes-from <container-name> <image>                # share volumes
```

### Behavior

- Mount an **empty** volume to a non-empty container path &rarr; Docker copies files from the container into the volume.
- Mount a **non-empty** volume &rarr; the container uses the volume as-is from the host.

### Storage Internals

- **Non-persistent (graph storage)**: stored in `/var/lib/docker/`, tied to the container lifecycle.
- **Persistent (volumes)**: exist outside the container but mounted into it.
- Device mapper (one of Docker's storage backends) has a default limit of 10 GB.
- Writing a file that's part of the image triggers a **copy-up**: the file is copied from the image to the read-write layer, then modified.

---

## Copy-on-Write (CoW)

- If a file exists in a lower image layer and another layer only needs read access, it uses the existing file.
- The first time a layer needs to **modify** the file (during build or runtime), the file is copied into that layer and modified there.
- Minimizes I/O and layer sizes.

---

## Logging & Monitoring

### Daemon Logs

- **systemd**: `journalctl -u docker.service`
- **non-systemd**: check `/var/log/messages`

### Container Logs

- Containers output to `STDOUT` and `STDERR`.
- Docker EE supports logging drivers that integrate with external solutions (syslog, gelf, Splunk, Fluentd).
- Configure in `daemon.json`, or override per container:
  ```
  docker run --log-driver <driver> --log-opts <opts> <image>
  ```

### Monitoring

```
docker container stats <name>
```
- Also consider **cAdvisor** for more detailed monitoring.

---

## Docker Compose

- `depends_on` is a dependency on the **container level** &mdash; it only waits for the container to start, not for the service inside to be ready.
- Config file: `~/.docker/config.json`

---

## Docker Swarm

### Secure Cluster

- A cluster of Docker nodes: **Managers** and **Workers**.
- **Mutual TLS**: managers and workers mutually authenticate; connections are encrypted.
- **Cluster store (etcd)** is encrypted and accessible by all managers.
- Two types of workloads: Swarm or Kubernetes (K8s for EE).

### Leadership & Certificates

- The first manager node is elected as **leader** and becomes the **root CA** of the swarm.
- Use an external CA with `docker swarm init --external-ca ...`
- Default certificate rotation policy is provided.
- Two cryptographic join tokens are created: one for managers, one for workers.
- Every node gets a client certificate (used for mutual auth, role authorization, and transport encryption).
- Sending a command to a follower manager &rarr; it proxies to the leader.
- Each worker has API access to all managers.

### Swarm Networking

- **Bridge**: single host.
- **Overlay**: multi-host.
- **Macvlan**: each container gets its own IP and MAC (requires promiscuous mode).

### Swarm Network Services

- **Service discovery**: every service gets a name registered with the swarm DNS; every service uses swarm DNS.
- **Ingress load balancing**.

### Registry

- Docker registry usually listens on port **5000**.
- `--restart=always` &mdash; if this container dies, restart it immediately.

---

## Kernel Internals

### Namespaces (Isolation)

Take the OS and partition it into multiple isolated virtual OSes:

| Namespace | Purpose |
|-----------|---------|
| **pid** | Process ID isolation |
| **net** | Network isolation |
| **mnt** | Filesystem/mount isolation |
| **ipc** | Inter-process communication (shared memory within container only) |
| **uts** | Gives each container its own hostname |
| **user** | Maps container users to host users (root in container &rarr; unprivileged on host) |

- Namespaces allow splitting the networking stack &mdash; one set per container.
- Container processes are attached to virtual network devices, which are attached to bridges.
- Each container has its own networking stack.
- `ls /proc/<pid>/ns` &mdash; shows the namespace pseudo-files; this is how the kernel differentiates between host and container processes.

### Control Groups (cgroups)

- Group processes together and set resource limits.
- Docker uses cgroups to group processes inside a container.

---

## BuildKit

```
export DOCKER_BUILDKIT=1
```
- Built to increase build performance.
- Parses the Dockerfile and determines what can run in **parallel** (can parallelize multiple stages simultaneously).
- Removes all intermediate images, leaving only the final one.
- You don't need Docker to use BuildKit.

---

## Troubleshooting

- `strace` &mdash; see system call stack for a process.
- Container process PIDs are visible from the host machine.
- `docker export <container-id>` to export the entire filesystem for exploration.
- `docker diff <container-id>` to see file changes vs the original image.
- `docker cp <container-id>:<path> <local-path>` to extract a specific file.
- `lsb_release` to detect Ubuntu version. Ubuntu version schema: `year.month` (e.g. `20.04` = April 2020).
- Docker resource limiting is useful when running Docker directly in production, but typically orchestration is handled by Kubernetes.
- Disk usage: device mapper (one of Docker's storage backends) has a default limit of 10 GB.

### gosu vs sudo

```
# sudo: PID 1 is sudo, PID N is the actual command
sudo command
# gosu: PID 1 is the actual command (even if you're not root)
gosu root command
```

---

## Best Practices

- **Include dependencies in the image** using fetch inside the container itself. Don't let containers fetch dependencies at startup.
- **If you depend on software to build your image**, check it into the image.
- **Tag builds with the git hash** of the code that built them.
- **Don't leave important data in unnamed stopped containers** &mdash; you may delete them by mistake.
- **Avoid golden images**.
- **Avoid defining shared folders in Dockerfile** &mdash; it limits portability.
- **Bind `/var/run/docker.sock`** to a Jenkins image to let CI/CD build images.

---

## Docker with Java

### JAR vs Dockerfile

- JVM and Docker solve different problems.
- An application is not just bytecode and assets &mdash; it also involves setting permissions, creating scripts, and configuring the JVM with specific parameters.
- A container creates a **complete execution environment** and packages a fixed version of it.
- You can have the same environment used in production; new team members can have a development environment in minutes.
- Downside of layered Java images: you have to build the classpath and know the name of the main method.

---

## Modernizing Traditional Apps

Existing App &rarr; Convert to container &rarr; Modern infra (on-premise, cloud, or hybrid) &rarr; Modern methodologies (CI/CD and automation) &rarr; Modern microservices

### Why Containers?

- Many decoupled services written in different languages.
- Multiple environments: production, pre-production, multiple staging, and multiple development environments.
- Without containers: a "matrix from hell" of services x environments x dependencies.
- Containers solve the **dependency hell** problem.
- Reliable CI &mdash; spin up a new environment in minutes, run tests, tear down, repeat.
- All app versions bundled with their dependencies for easy comparison.
- Decouple plumbing (load balancing, service discovery) from application logic.