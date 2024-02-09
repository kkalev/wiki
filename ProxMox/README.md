# References
* YouTube [tutorial](https://www.youtube.com/watch?v=5j0Zb6x_hOk&list=PLT98CRl2KxKHnlbYhtABg6cF50bYa8Ulo&index=1)
* ProxMox Admin [guide](https://pve.proxmox.com/pve-docs/pve-admin-guide.pdf)
* ProxMox vs VMWare Storage Performance [tests](https://kb.blockbridge.com/technote/proxmox-vs-vmware-nvmetcp/)
* iSCSI target with NAS [reference](https://www.virtualizationhowto.com/2022/01/proxmox-iscsi-target-to-synology-nas/)

# Admin Guide
## Introduction

* Main virtualization technologies used:
  - Kernel based Virtual Machines (KVM)
  - Container based virtualization (LXC)
* Proxmox VE uses a bridged networking model. All VMs can share one bridge as if virtual network cables from each guest were all plugged into the same switch. For connecting VMs to the outside world, bridges are attached to physical network cards and assigned a TCP/IP configuration.
* To get an overview of the CPU and hard disk performance on an installed Proxmox VE system, run the included `pveperf` tool.
## Host System Administration
### Network Configuration
* Proxmox VE server in a private LAN, using an external gateway to reach the internet 
  - The `Bridged` model makes the most sense in this case, and this is also the default mode on new Proxmox VE installations. Each of your Guest system will have a virtual interface attached to the Proxmox VE bridge. This is similar in effect to having the Guest network card directly connected to a new switch on your LAN, the Proxmox VE host playing the role of the switch.
* Proxmox VE server at hosting provider, with public IP ranges for Guests
  - For this setup, you can use either a `Bridged` or `Routed` model, depending on what your provider allows
#### Bridged Configuration

* Bridges are like physical network switches implemented in software. All virtual guests can share a single bridge, or you can create multiple bridges to separate network domains. Each host can have up to 4094 bridges.
* The installation program creates a single bridge named `vmbr0`, which is connected to the first Ethernet card. 
* Virtual machines behave as if they were directly connected to the physical network. The network, in turn, sees each virtual machine as **having its own MAC**, even though there is only one network cable connecting all of these VMs to the network.
#### Routed Configuration
* Most hosting providers do not support the above setup. For security reasons, they disable networking as soon as they detect multiple MAC addresses on a single interface.
* You can avoid the problem by “routing” all traffic via a single interface. This makes sure that all network packets use the same MAC address.

* A common scenario is that you have a public IP (assume `198.51.100.5` for this example), and an additional IP block for your VMs (`203.0.113.16/28`).
## Cluster Manager
* The Proxmox VE cluster stack requires a reliable network with latencies under 5 milliseconds (LAN performance) between all nodes to operate stably. While on setups with a small node count a network with higher latencies may work, this is not guaranteed and gets rather unlikely with more than three nodes and latencies above around 10 ms.
* When creating a cluster without any parameters, the corosync cluster network is generally shared with the web interface and the VMs’ network. Depending on your setup, even storage traffic may get sent over the same network. It’s recommended to change that, as corosync is a time-critical, real-time application.
## Storage
### Storage Types
* There are basically two different classes of storage types:
  - `File level storage`: File level based storage technologies allow access to a fully featured (POSIX) file system. They are in general more flexible than any Block level storage and allow you to store content of any type. ZFS is probably the most advanced system, and it has full support for snapshots and clones.
  - `Block level storage`: Allows to store large raw images. It is usually not possible to store other files (ISO, backups, ..) on such storage types.

* A number of storages, and the QEMU image format qcow2, support thin provisioning. With thin provisioning activated, only the blocks that the guest system actually use will be written to the storage.
* All storage types which have the **Snapshots** feature also support thin provisioning.
### LVM
* LVM is a light software layer on top of hard disks and partitions. It can be used to split available disk space into smaller logical volumes. LVM is widely used on Linux and makes managing hard drives easier.
* Another use case is to put LVM on top of a big iSCSI LUN. That way you can easily manage space on that iSCSI LUN, which would not be possible otherwise, because the iSCSI specification does not define a management interface for space allocation.
* LVM is a typical block storage, but this backend does not support snapshots and clones.
* One big advantage is that you can use it on top of a shared storage, for example, an iSCSI LUN. The backend itself implements proper cluster-wide locking.
### LVM thin backend
* LVM normally allocates blocks when you create a volume. LVM thin pools instead allocates blocks when they are written. This behaviour is called thin-provisioning, because volumes can be much larger than physically available space.
* LVM thin is a block storage, but fully supports snapshots and clones efficiently. New volumes are automatically initialized with zero.
* It must be mentioned that LVM thin pools **cannot be shared across multiple nodes**, so you can only use them as local storage.
### Open-iSCSI initiator
* To use this backend, you need to install the Open-iSCSI package. It is not installed by default to save resources: `# apt-get install open-iscsi`
* The iSCSI protocol does not define an interface to allocate or delete data. Instead, that needs to be done on the target side and is vendor specific. The target simply exports them as numbered LUNs.
## Hyper-Converged Ceph Cluster
* Ceph is a distributed object store and file system designed to provide excellent performance, reliability and scalability.
* Features
  - Thin provisioning
  - Snapshot support
  - Self healing
  - Provides block, file system, and object storage
  - Setup pools with different performance and redundancy characteristics
  - Data is replicated, making it fault tolerant
  - Runs on commodity hardware
  - No need for hardware RAID controllers
* For small to medium-sized deployments, it is possible to install a Ceph server for using `RADOS Block Devices (RBD)` or `CephFS` directly on your Proxmox VE cluster nodes
* Ceph consists of multiple Daemons, for use as an RBD storage:
  - Ceph Monitor (ceph-mon, or MON)
  - Ceph Manager (ceph-mgr, or MGS)
  - Ceph Metadata Service (ceph-mds, or MDS)
  - Ceph Object Storage Daemon (ceph-osd, or OSD)
* To build a hyper-converged Proxmox + Ceph Cluster, you must use at least three (preferably) identical servers for the setup.
### CPU
* As a simple rule of thumb, you should assign **at least one CPU core (or thread) to each Ceph service** to provide the minimum resources required for stable and durable Ceph performance.
* For example, if you plan to run a Ceph monitor, a Ceph manager and 6 Ceph OSDs services on a node you should reserve 8 CPU cores purely for Ceph when targeting basic and stable performance.
### Memory
* As a rule of thumb, **for roughly 1 TiB of data, 1 GiB of memory will be used by an OSD**.
* The OSD service itself will use additional memory. The Ceph BlueStore backend of the daemon requires by default **3-5 GiB of memory** (adjustable).
### Network
* We recommend a network bandwidth of **at least 10 Gbps, or more, to be used exclusively for Ceph traffic**.
* The volume of traffic, especially during recovery, will interfere with other services on the same network, especially the latency sensitive Proxmox VE corosync cluster stack can be affected, resulting in possible loss of cluster quorum. Moving the Ceph traffic to dedicated and physical separated networks will avoid such interference, not only for corosync, but also for the networking services provided by any virtual guests.
* While a single HDD might not saturate a 1 Gb link, multiple HDD OSDs per node can already saturate 10 Gbps too. If modern NVMe-attached SSDs are used, a single one can already saturate 10 Gbps of bandwidth, or more. For such high-performance setups we recommend at least a 25 Gpbs, while even 40 Gbps or 100+ Gbps might be required to utilize the full performance potential of the underlying disks.
* If unsure, we recommend using **three (physical) separate networks for high-performance setups**:
  - One very high bandwidth (`25+ Gbps`) network for Ceph (internal) cluster traffic.
  - One high bandwidth (`10+ Gpbs`) network for Ceph (public) traffic between the ceph server and ceph client storage traffic.
  - One medium bandwidth (`1 Gbps`) exclusive for the latency sensitive corosync cluster communication.
### Disks
* It is recommended that you use SSDs instead of HDDs in small setups to reduce recovery time, minimizing the likelihood of a subsequent failure event during recovery.
* Ceph performs best with an evenly sized, and an evenly distributed amount of disks per node. For example, 4 x 500 GB disks within each node is better than a mixed setup with a single 1 TB and three 250 GB disk.
### Configuration
* The configuration step includes the following settings:
  - `Public Network`: This network will be used for public storage communication (e.g., for virtual machines using a Ceph RBD backed disk, or a CephFS mount), and communication between the different Ceph services.
  - `Cluster Network`: Specify to separate the OSD replication and heartbeat traffic as well. This setting is optional. Using a physically separated network is recommended, as it will relieve the Ceph public and the virtual guests network, while also providing a significant Ceph performance improvements.
* The Ceph Monitor (MON) maintains a master copy of the cluster map. For high availability, **you need at least 3 monitors**. One monitor will already be installed if you used the installation wizard.
* On each node where you want to place a monitor (three monitors are recommended), create one by using the Ceph → Monitor tab in the GUI.
* The Manager daemon runs alongside the monitors. It provides an interface to monitor the cluster. Since the release of Ceph luminous, at least one `ceph-mgr` daemon is required. **It is recommended to install the Ceph Manager on the monitor nodes**. For high availability install more then one manager.
* Ceph `Object Storage Daemons` store objects for Ceph over the network. It is recommended to **use one OSD per physical disk** (which also means one CPU core/thread per physical disk).
* You can create an OSD either via the Proxmox VE web interface or via the CLI
* We recommend a Ceph cluster with **at least three nodes and at least 12 OSDs, evenly distributed among the nodes**.
* A `pool` is a logical group for storing objects. It holds a collection of objects, known as `Placement Groups (PG, pg_num)`.
* You can create and edit pools from the command line or the web interface of any Proxmox VE host under Ceph → Pools.
### CephFS
* Ceph also provides a filesystem, which runs on top of the same object storage as RADOS block devices do. A `Metadata Server (MDS)` is used to map the RADOS backed objects to files and directories, allowing Ceph to provide a POSIX-compliant, replicated filesystem.
* CephFS needs **at least one Metadata Server to be configured and running**, in order to function. You can create an MDS through the Proxmox VE web GUI’s Node -> CephFS panel.
* Multiple metadata servers can be created in a cluster, but with the default settings, **only one can be active at a time**.
* If an MDS or its node becomes unresponsive (or crashes), another `standby` MDS will get promoted to `active`.
* You can speed up the handover between the active and standby MDS by using the `hotstandby` parameter option on creation.

## Storage Replication
* Storage replication brings redundancy for guests using local storage and reduces migration time.
* It replicates guest volumes to another node so that all data is available without using shared storage. Replication uses snapshots to minimize traffic sent over the network. Therefore, new data is sent only incrementally after the initial full sync. In the case of a node failure, your guest data is still available on the replicated node.
* The replication is done automatically in configurable intervals. The minimum replication interval is one minute, and the maximal interval once a week.
* Only changes since the last replication (so-called deltas) need to be transferred if the guest is migrated to a node to which it already is replicated. This reduces the time needed significantly. The replication direction automatically switches if you migrate a guest to the replication target node.
* **Only available for ZFS storage**. It [seems](https://forum.proxmox.com/threads/coming-feature-replication-for-lvm-storage.84877/) that **there are no current plans** to implement this feature for LVM storage.

## Firewall
* The Proxmox VE firewall groups the network into the following logical zones:
  - `Host`: Traffic from/to a cluster node
  - `VM`: Traffic from/to a specific VM
* For each zone, you can define firewall rules for incoming and/or outgoing traffic
* All firewall related configuration is stored on the proxmox cluster file system. So those files are automatically distributed to all cluster nodes, and the `pve-firewall` service updates the underlying `iptables` rules automatically on changes.
* A security group is a collection of rules, defined at cluster level, which can be used in all VMs’ rules. For example you can define a group named “webserver” with rules to open the http and https ports.
## User Management
* The system’s root user can always log in via the Linux PAM realm and is an unconfined administrator. This user cannot be deleted, but attributes can still be changed.
* Each user can be a member of several groups. Groups are the preferred way to organize access permissions. You should always grant permissions to groups instead of individual users.
* A resource pool is a set of virtual machines, containers, and storage devices. It is useful for permission handling in cases where certain users should have controlled access to a specific set of resources, as it allows for a single permission to be applied to a set of elements, rather than having to manage this on a per-resource basis. Resource pools are often used in tandem with groups, so that the members of a group have permissions on a set of machines and storage.
### Authentication Realms
* The following realms (authentication methods) are available:
  - `Linux PAM Standard Authentication`: These users are created on the host system with commands such as `adduser`.
  - `Proxmox VE Authentication Server``: This is the most convenient realm for small-scale (or even mid-scale) installations, where users do not need access to anything outside of Proxmox VE.
  - `LDAP`
  - `OpenID Connect`
### Two-Factor Authentication
* When a realm has TFA enabled, it becomes a requirement, and only users with configured TFA will be able to log in.
* Currently there are two methods available:
  - `Time-based OATH (TOTP)`: This uses the standard HMAC-SHA1 algorithm, where the current time is hashed with the user’s configured key. The time step and password length parameters are configurable.
* For TOTP, 8 failed attempts will disable the user’s TOTP factors. They are unlocked when logging in with a recovery key.
* An admin can unlock a user’s Two-Factor Authentication at any time via the user list in the UI or the command line: `pveum user tfa unlock joe@pve`

# Notes
* PAM User is used if you want ssh. Otherwise you can create users in the PVE itself
* Firewalling: Rules on datacenter level apply to all nodes. Rules on node-level apply only to the node and supersede datacenter-level rules. Rules on VM level apply only to the VM and are unaffected by the datacenter and node rules (VMs are like their own separate entities).
* Helpful commands:
  - `qm list`: List VMs and VM templates
  - `qm start <VM ID>`: Start a VM
  - `qm shutdown <VM ID>`: Shutdown a VM
  - `qm reboot <VM ID>`
  - `qm config <VM ID>`: Show options for a VM
* Containers
  - ProxMox supports *LXC* containers
  - Containers cannot be live migrated. They are shutdown first.
* SAN
  - It seems we need to enable multipath, edit files and only LVM is available (not LVM thin). See [reference](https://forum.proxmox.com/threads/best-practice-for-proxmox-server-with-iscsi-san-storage.103877/)
