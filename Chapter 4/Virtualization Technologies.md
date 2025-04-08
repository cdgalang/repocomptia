# Virtualization Technologies
---

* Virtualization are essential for enabling cloud computing and optimizing resource utilization. 
* Also allows multiple OS and applications to run on a single physical machine, improves efficiency, flexibility and scalability

* **Hypervisor-based virtualization**: most common type of virtualization
    * a software layer that sits between the hardware and the OS
    * **Type 1**: Bare metal Hypervisors run directly on the hardware and offers high performance and security
    * **Type 2**: Hosted Hypervisors run on top of OS and easierto install and manage, howerver it has lower performance than Type 1. Ex: VMWare and VirualBox

* **Containerization**: isolates applications in containers, which share the host operating system kernel
    * lightweight so containers are smaller and faster than VMs
    * Portablility as it can run on any platform that supports the container such as Docker or Kubernetes
    * Provides Isolation between applications, preventing them from interfering with each other
    * Efficient resource utilization

* **OS level Virtualization**: provides virtualization at the OS level, allowing multiple isolated instances and provide higher level of isolation and resource management. Ex: LXC and OpenVZ

* **Hardware Virtualization**: relies on hardware features to improve virtualization performance and security

* **Network Virtualization**: allowing creation of virtual networks enables features such as VLANs, VPNs and SDN 

* **Storage Virtualization**: allowing creating of virtual storage pools that can be allocated to VMs and enables feature such as snapshots, and storage replication

Benefits of Virtualizations
* Improved resource utilization
* Increased flexibility and agility
* Reduced Costs
* Improved disaster recovery
* Enhanced security