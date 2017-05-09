# ClusterBoot

This is an Ansible playbook designed to start a cluster on our own infrastructure. It needs the version 2.1 of Ansible (which is not the official version provided by the centos package but is the version available on python-pip).

Working only with an installed cluster based on Centos7

This playbook install a spark cluster in an environement with or without internet access.

## Requirements

### Online

- CentOS7 installed on each of the servers.
- a DNS server that'll have all FQDN names.

### Offline
Same as Online but also :
- An http server that serves binaries :
  - Mirror of the classic CentOS repositories (Base, Extras, Updates)
  - Mirror of the EPEL repository
  - Mirror of CONDA repository (for both R and Python)
  - Binaries for Hadoop
  - Binaries for spark
  - Binaries for mesos (you have to compile it yourself)
  - Binary for anaconda

- A ntp server to synchronize the cluster

#### How to compile mesos
On Centos7 for the default installation path of mesos (/usr/local/lib/mesos):

```bash
wget http://www.apache.org/dist/mesos/1.1.0/mesos-1.1.0.tar.gz
tar -zxf mesos-1.1.0.tar.gz
sudo yum install -y epel-release
sudo yum groupinstall -y "Development Tools"
sudo yum install -y maven python-devel python-virtualenv java-1.8.0-openjdk-devel zlib-devel libcurl-devel openssl-devel cyrus-sasl-devel cyrus-sasl-md5 apr-devel subversion-devel apr-util-devel
cd mesos
mkdir build
cd build
../configure --prefix=/usr/local/lib/mesos-1.1.0
make
```

#### TODO

- remove bothering logging when using zkCli.sh
