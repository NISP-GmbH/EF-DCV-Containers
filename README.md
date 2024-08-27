# EF DCV Containers

## Overview

An easy way to deploy Enginframe integrated with Slurm and DCV Session Manager to manage DCV Sessions and other kind of Jobs.

Features:
* Last EnginFrame stable version ready to use
* EnginFrame integration with SSSD to use OpenLDAP and ActiveDirectory
* DCV Server ready to open DCV sessions
* Slurm ready to control DCV sessions and general jobs
* DCV Session Manager (Agent, Broker and Gateway) to manage DCV sessions
* Nginx as a proxy to DCV Server and EnginFrame

## Requirements

* Docker and docker compose plugin installed
* Clone the current git

### Minimal resources

- 2 CPU/vCPU
- 4GB RAM
- 20GB storage HDD

### Recommended resources

- 4 CPU/vCPU
- 8GB RAM
- 20GB storage SSD/SSD NVME

## How to startup the containers services

1. Clone this git repository
2. Edit the .env file and set at leat this variable (very simple, is not?):
```bash
EF_DOMAIN=myefcontainer.mydomain.com
```
3. Add into conf/ folder the license.ef (license for EnginFrame) and the license.dcv (license for DCV Server). You need to use those names.
4. Execute the docker compose plugin (you need to be inside of the git clone):
```bash
docker-compose up -d
```

**Important:** When you execute for the first time, depending of your CPU/Stroage resources, it can take from 1 to 3 minutes to be ready to use. You can eventually see some containers restarting; This can happen because they are waiting for other containers procedures. In that cases you will see Bad gateway if you try to access some web service. Just wait one extra minute and try again.

## How to access web services

* Enginframe: Just enter in https://theurlthatyouconfigured/
* DCV Server web viewer: https://theurlthatyouconfigured/dcv

## How to stop the containers

Notes:
* EF sessions/jobs will not be erased.
* Slurm DB will not be erased.

1. Enter in the git clone and execute:
```bash
docker-compose down
```

## How to completely remove the containers:
1. Stop the containers as explained before.
2. Execute
```bash
docker volume ls
```

and remove all volumes created. The volumes will have this suffix:
* _dcvsm_data
* _dcvsrv_data
* _enginframe_data
* _mysql_data 
* _shared-volume
* _slurmcluster_etc_munge
* _slurmcluster_etc_slurm
* _slurmcluster_slurm_jobdir 
* _slurmcluster_slurm_log

3. To remove, execute the command bellow. Supposing that the prefix is root, then the command will be:
```bash
docker volume rm root_dcvsm_data root_dcvsrv_data root_enginframe_data root_mysql_data root_shared-volume root_slurmcluster_etc_munge root_slurmcluster_etc_slurm root_slurmcluster_slurm_jobdir root_slurmcluster_slurm_log
```

## Containers infrastructure

* dcvserver-nisp : Will load DCV Server and the DCV Session Manager Agent; Will be also a Slurm node.
* dcvsm-nisp : Will load DCV Session Manager Broker and setup DCV CLI
* nginx-nisp : Will proxy EnginFrame to / and DCV Server web view to /dcv
* enginframe-nisp : Will load EnginFrame and integrate with DCV with DCV SM Cluster and Slurm; will be also a Slurm node.
* mysql-nisp : Will store Slurm persistent data
* slurmdbd-nisp : Will load the Slurm Database Daemon
* slurmctld-nisp : Will load the management daemon for Slurm
* slurm_c1-nisp and slurm_c2-nisp : Just additional Slurm nodes to you test Enginframe generic jobs

## Customizing the services

You can customize the containers services editing some .env variables. To ENABLE or DISABLE a feature, you need to set as true (ENABLED) or false (DISABLED). Some services will require additional configuration when you set true/false.


Here is the list of services that you can customize:
* **DCVSM_EXTERNAL_ENABLED :** EnginFrame can use external DCV SM Broker instead of local container. If you enable, you need to also configure DCVSM_CLUSTER_dcvsm_cluster1_ variables.
* **SSSD_SERVICE_ENABLED :** If enabled, you need to copy your sssd.conf file inside of conf/ folder. It will map this file to internal EnginFrame /etc/sssd/sssd.conf file and load the SSSD service.
* **LSF_ENABLED :** If enabled, it will enable LSF in EnginFrame config.
* **DEMO_SESSION_CREATE :** If true, a session will be created with DCV_DEMO_USER as owner. By default, the session will be created by EnginFrame ondemand.
* **SLURM_ENABLED :** If true, it will enable Slurm inside of EnginFrame.

## Frequent Asked Questions

1. Will these containers change anything in my server?
No. We just create docker volumes and user docker resources. It will not change anything in the server.

2. How can I have additional support if I found any problem or have any question?
You can create a git issue and also send an e-mail to support@ni-sp.com.

3. The DCV Server can be configured to be used with UDP/QUIC?
By default the DCV Server will listen the UDP/QUIC protocol. You do not need extra setup.
