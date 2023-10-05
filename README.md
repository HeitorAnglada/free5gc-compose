# free5GC compose

This repository is a docker compose version of [free5GC](https://github.com/free5gc/free5gc) for stage 3. It's inspired by [free5gc-docker-compose](https://github.com/calee0219/free5gc-docker-compose) and also reference to [docker-free5gc](https://github.com/abousselmi/docker-free5gc).

You can setup your own config in [config](./config) folder and [docker-compose.yaml](docker-compose.yaml)

## Prerequisites

- I use Ubuntu 20.04 LTS and Kernel 5.15.0
- [GTP5G kernel module](https://github.com/free5gc/gtp5g): needed to run the UPF
  
```bash
git clone https://github.com/free5gc/gtp5g.git && cd gtp5g
make clean && make
```

- [Docker Engine](https://docs.docker.com/engine/install): needed to run the Free5GC 

Run the following command to uninstall all conflicting packages:

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

Set up Docker's Apt repository.

```bash
# Add Docker's official GPG key:
sudo apt-get update
```
```bash
sudo apt-get install ca-certificates curl gnupg
```

```bash
sudo install -m 0755 -d /etc/apt/keyrings

```

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

```bash
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

```bash
# Add the repository to Apt sources:
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

Install
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
Verify docker installation
```bash
sudo docker run hello-world
```

## Start free5gc

Because we need to create tunnel interface, we need to use privileged container with root permission.


```bash
# Clone the project
git clone https://github.com/HeitorAnglada/free5gc-compose.git
cd free5gc-compose
```
# clone free5gc sources

Note:

Dangling images may be created during the build process. It is advised to remove them from time to time to free up disk space.

```bash
docker rmi $(docker images -f "dangling=true" -q)
```

### Run free5GC

You can create free5GC containers based on local images or docker hub images:

```bash
# use images from docker hub
docker compose up # add -d to run in background mode
```

Destroy the established container resource after testing:

```bash
# Remove established containers (remote images)
docker compose rm
```

## Troubleshooting

Sometimes, you need to drop data from DB:

```bash
docker exec -it mongodb mongo
> use free5gc
> db.dropDatabase()
> exit # (Or Ctrl-D)
```

You can see logs for each service using `docker logs` command. For example, to access the logs of the *SMF* you can use:

```console
docker logs smf
```

Please refer to the [wiki](https://github.com/free5gc/free5gc/wiki) for more troubleshooting information.

## Integration with external gNB/UE simulators

The integration with the [UERANSIM](https://github.com/aligungr/UERANSIM) eNB/UE simulator is documented [here](https://www.free5gc.org/installations/stage-3-sim-install/). 

You can also refer to this [issue](https://github.com/free5gc/free5gc-compose/issues/26) to find out how you can configure the UPF to forward traffic between the [UERANSIM](https://github.com/aligungr/UERANSIM) to the DN (eg. internet) in a docker environment.

This [issue](https://github.com/free5gc/free5gc-compose/issues/28) provides detailed steps that might be useful.

## Reference
- https://github.com/open5gs/nextepc/tree/master/docker
- https://github.com/abousselmi/docker-free5gc
