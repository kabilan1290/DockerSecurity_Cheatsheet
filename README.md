# DockerSecurity_Cheatsheet

CGroups Examples

CGroups control how much resources a process can use. By adding restrictions, you can deliver a guaranteed Quality of Service to applications by ensuring they have enough space available.   
```
--cpu-shares
  --cpuset-cpus
  --memory-reservation
  --kernel-memory
  --blkio-weight (block  IO)
  --device-read-iops
  --device-write-iops
```  
docker run -d --name mb100 --memory 100m alpine top - To limit the memory usage of the conatiner.

docker stats --no-stream - The memory usage limit of the containers can be listed with this command.

--cpu-shares parameter defines a share between 0-768. - The cpu-shares option allows you to specify the relative share of cpu a container will receive when there is contention for cpu.
  
Namespace Examples

Namespaces control what a process can see and access.
```
Cgroup      CLONE_NEWCGROUP   Cgroup root directory
  IPC         CLONE_NEWIPC      System V IPC, POSIX message queues
  Network     CLONE_NEWNET      Network devices, stacks, ports, etc.
  Mount       CLONE_NEWNS       Mount points
  PID         CLONE_NEWPID      Process IDs
  User        CLONE_NEWUSER     User and group IDs
  UTS         CLONE_NEWUTS      Hostname and NIS domain name
 ``` 
CoreOS Clair:

Clair is an Open Source project from CoreOS, designed to scan Docker Images for Security Vulnerabilities.

Setup:

curl -LO https://raw.githubusercontent.com/coreos/clair/05cbf328aa6b00a167124dbdbec229e348d97c04/contrib/compose/docker-compose.yml

mkdir clair_config && curl -L https://raw.githubusercontent.com/coreos/clair/master/config.yaml.sample -o clair_config/config.yaml

sed 's/clair-git:latest/clair:v2.0.1/' -i docker-compose.yml && \
  sed 's/host=localhost/host=postgres password=password/' -i clair_config/config.yaml
  
docker-compose up -d postgres - Starting the database.

curl -LO https://gist.githubusercontent.com/BenHall/34ae4e6129d81f871e353c63b6a869a7/raw/5818fba954b0b00352d07771fabab6b9daba5510/clair.sql
docker run -it \
    -v $(pwd):/sql/ \
    --network "${USER}_default" \
    --link clair_postgres:clair_postgres \
    postgres:latest \
        bash -c "PGPASSWORD=password psql -h clair_postgres -U postgres < /sql/clair.sql"  
        
 Downloading CVE details for clair to load and use for scanning the images.
 
 docker-compose up -d clair - To start the clair service.
 
 Klar is a simple tool to analyze images stored in a private or public Docker registry for security vulnerabilities using Clair.
 
 curl -L https://github.com/optiopay/klar/releases/download/v1.5/klar-1.5-linux-amd64 -o /usr/local/bin/klar && chmod +x $_ - To download latest klar.
 
 CLAIR_ADDR=http://localhost:6060 CLAIR_OUTPUT=Low CLAIR_THRESHOLD=10 \
  klar quay.io/coreos/clair:v2.0.1   - Using klar, we can now point it at images and see what vulnerabilities they contain, for example quay.io/coreos/clair:v2.0.1.
  
SecComp:
SecComp defines which system calls should and should not be allowed to be executed by a container.

docker run --rm -it --security-opt seccomp:<policy file.json> <image> - Runs the image with Seccomp policy(Secure computing),restrict system calls.

Restrict additional Privilage:
docker run --security-opt=no-new-privileges <image>  -> prevents the container from Additional privilage.
  
User namespaces:
```
Edit /etc/docker/daemon.json. Assuming the file was previously empty, the following entry enables userns-remap using user and group called testuser. 

You can also start the docker service with  - dockerd --userns-remap="testuser:testuser"

--userns=host - We can use this flag to disable user namespace.
```

AppArmor security profiles:
```
apparmor_parser -r -W /path/to/your_profile - To load a new profile into AppArmor for use with containers.

docker run --rm -it --security-opt apparmor=your_profile hello-world - To run apparmor custom profile with --security-opt.

aa-status  - To check the apparmor status
```
