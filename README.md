# kubernetes-config-map-as-file inside a pod

- In this experiment, we're going to request kubernetes to mount a volume with some files.
- The files will have the content of the config map

# Items

- configmap-app002.yaml
- app002-pod.yaml

# configmap-app002.yaml

It contains non-sensitive information, that a microservice may need for initialization or proper working. It may hold the information about where the microservice is deployed.

# app002-pod.yaml

- It contains a containerized application
- It has a ssh server
- As a proof, we can use kubernetes port-forwarding, connect via ssh, and explore the directories of the pod
- We'll observe the files

# Steps

## Prepare the container, test locally

1. Use the command, to start a local docker container with ssh. 
> docker run --rm -itp 2222:2222 --name=ssh --hostname=ssh -e SUDO_ACCESS=true -e PASSWORD_ACCESS=true -e USER_PASSWORD=dummy -e USER_NAME=dummy linuxserver/openssh-server sh

2. Now in the localhost machine, try connecting the docker container instance using any SSH client
3. Tested successfully with "moba" SSH client

## Create a pod in kubernetes cluster, test with kubernetes port-forwarding

1. Use the yaml "configmap-app002.yaml" inside the "Deploy" folder to create a configmap object inside kubernetes cluster
> kubectl create -f configmap-app002.yaml

2. Check and confirm the presence of config map in kubernetes cluster
> kubectl get configmaps
> kubectl describe configmaps/configmap-app002

3. Create the pod with the docker container using the "app002-pod.yaml" yaml file and confirm its presence
> kubectl create -f app002-pod.yaml
> kubectl get pods

4. Start the kubernetes port-forwarding
> kubectl port-forward pods/mypod 2222:2222

5. Connect the pod with openssh-server endpoint at localhost:2222 using any SSH client with the credentials dummy:dummy
6. Explore the files inside the directory /etc/foo
> cd /etc/foo
> ls
> cat game.properties
> cat player_initial_lives
> cat user-interface.properties

# References

- https://kubernetes.io/docs/concepts/configuration/configmap/
- https://github.com/linuxserver/docker-openssh-server


