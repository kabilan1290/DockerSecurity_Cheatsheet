# DockerSecurity_Cheatsheet

CGroups Examples
CGroups control how much resources a process can use. By adding restrictions, you can deliver a guaranteed Quality of Service to applications by ensuring they have enough space available.   

--cpu-shares
  --cpuset-cpus
  --memory-reservation
  --kernel-memory
  --blkio-weight (block  IO)
  --device-read-iops
  --device-write-iops
  
docker run -d --name mb100 --memory 100m alpine top - To limit the memory usage of the conatiner.

docker stats --no-stream - The memory usage limit of the containers can be listed with this command.

--cpu-shares parameter defines a share between 0-768. - The cpu-shares option allows you to specify the relative share of cpu a container will receive when there is contention for cpu.
  
Namespace Examples
Namespaces control what a process can see and access.

Cgroup      CLONE_NEWCGROUP   Cgroup root directory
  IPC         CLONE_NEWIPC      System V IPC, POSIX message queues
  Network     CLONE_NEWNET      Network devices, stacks, ports, etc.
  Mount       CLONE_NEWNS       Mount points
  PID         CLONE_NEWPID      Process IDs
  User        CLONE_NEWUSER     User and group IDs
  UTS         CLONE_NEWUTS      Hostname and NIS domain name
  
