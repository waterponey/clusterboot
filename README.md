# ClusterBoot

This is an Ansible playbook designed to start a cluster on our own infrastructure.

Working only with an installed cluster based on Centos7

This playbook install a spark cluster in an environement with or without internet access.

## Requirements

### Online

- CentOS7 installed on each of the servers.
- a DNS server that'll have all FQDN names

### Offline
Same as Online but also :
- An http server that serves binaries
- Mirror of the classic CentOS repositories (Base, Extras, Updates, CentosPlus)
- Mirror of the EPEL repository
- Mirror of Mesosphere repository
- Mirror of Cloudera repository
- Mirror of Hortonworks repository
- Mirror of CONDA repository (for both R and Python)
- Mirror of the SBT repository
- Binaries for scala
- Binaries for spark
- Binary for anaconda 
