# ScyllaDB

ScyllaDB uses [[CQL]] ([[Apache]] [[Cassandra]] Query Language)

[Best practices](https://docs.scylladb.com/operating-scylla/procedures/tips/)

## Getting started

### [System requirements](https://docs.scylladb.com/getting-started/system-requirements/)

#### Memory requirements

The more memory available, the better Scylla performs, as Scylla uses all of the available memory for caching. The wider the rows are in the schema, the more memory will be required. 64 GB-256 GB is the recommended range for a medium to high workload. Memory requirements are calculated based on the number of `logical core`s (`lcore`s) you are using in your system.

Recommended size: 16 GB or 2GB per `lcore` (whichever is higher)

Maximum: 1 TiB per `lcore`, up to 256 `lcore`s

Minimum:
- For test environments: 1 GB or 256 MiB per `lcore` (whichever is higher)
- For production environments: 4 GB or 0.5 GB per `lcore` (whichever is higher)

#### Disk requirements

We highly recommend [[SSD]] and local disks. Scylla is built for a large volume of data and large storage per node. The rule of thumb is using 30:1 Disk/RAM ratio; for example, 30 TB of storage requires 1 TB of RAM. When there are multiple drives, we recommend a RAID-0 setup and a replication factor of 3 within the local datacenter (RF=3).

#### Network requirements

A network speed of 10 Gbps or more is recommended, especially for large nodes. To tune the interrupts and their queues, run the Scylla setup scripts.

### [OS Support by Platform and Version](https://docs.scylladb.com/getting-started/os-support/)

Scylla requires a fix to the XFS append introduced in kernel 3.15 (back-ported to 3.10 in RHEL/CentOS). Scylla will not run with earlier kernel versions

### [Scylla in a Shared Environment](https://docs.scylladb.com/getting-started/scylla-in-a-shared-environment/)

#### Memory

The most critical resource that Scylla consumes is memory. By default, when Scylla starts up, it inspects the node’s hardware configuration and claims all memory to itself, leaving some reserve for the operating system (OS). This is in contrast to most open-source databases that leave most memory for the OS, but is similar to most commercial databases.

In a shared environment, particularly on a desktop or laptop, gobbling up all the machine’s memory can reduce the user experience, so Scylla allows reducing its memory usage to a given quantity.

On Ubuntu, open a terminal and edit `/etc/default/scylla-server`, and add `--memory 2G to` restrict Scylla to 2 gigabytes of RAM.

On Red Hat / CentOS, open a terminal and edit `/etc/sysconfig/scylla-server`, and add `--memory 2G to` restrict Scylla to 2 gigabytes of RAM.

If starting Scylla from the command line, simply append `--memory 2G to` your command line.

#### CPU

By default, Scylla will utilize all of your processors (in some configurations, particularly on Amazon AWS, it may leave a core for the operating system)

Scylla offers two options to restrict its CPU utilization:

- `--smp N` restricts Scylla to N logical cores; for example with `--smp 2` Scylla will not utilize more than two logical cores

- `--overprovisioned` tells Scylla that the machine it is running on is used by other processes; so Scylla will not pin its threads or memory, and will reduce the amount of polling it does to a minimum.

On Ubuntu edit `/etc/default/scylla-server`, and add `--smp 2 --overprovisioned` to restrict Scylla to 2 logical cores.

On Red Hat / CentOS edit `/etc/sysconfig/scylla-server`, and add `--smp 2 --overprovisioned` to restrict Scylla to 2 logical cores.

If starting Scylla from the command line, simply append `--smp 2 --overprovisioned` to your command line.

### Scylla configuration

| Installed location                                                                   | Description                    |
| ------------------------------------------------------------------------------------ | ------------------------------ |
| `/etc/default/scylla-server` (Ubuntu/Debian) `/etc/sysconfig/scylla-server` (others) | Server startup options         |
| `/etc/scylla/scylla.yaml`                                                            | Main Scylla configuration file |
| `/etc/scylla/cassandra-rackdc.properties`                                            | Rack & dc configuration file   |
