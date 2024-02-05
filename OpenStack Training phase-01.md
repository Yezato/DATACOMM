# Cara Install OpenStack Multinode Menggunakan Kolla Ansible
Dalam tutorial Phase-1 ini openstack akan di install kedalam 2 node server, 1 node controller dan 1 node compute. 
#### Tutorial ini menggunakan spesifikasi seperti dibawah ini: 
- OpenStack version: ZED
- OS : Ubuntu 22.04
- 2 network interfaces
- 2 node server (1 controller, 1 compute)
- 8GB main memory
- 50GB disk space

#### Service OpenStack:
- Placement (placement)
- Neutron (network)
- Nova (compute)
- Cinder (volume)
- Glance (image)
- Keystone (identity)
- Gnocchi (metric)
- Aodh (alarming)
- Ceilometer (metering)
- Horizon (Dashboard)

### Install dependencies:
> sudo apt update && sudo apt upgrade

> sudo apt install git python3-dev libffi-dev gcc libssl-dev

### Install dependencies using a virtual environment:
##### Install the virtual environment dependencies.
> sudo apt install python3-venv
##### Create virtual environment 
> python3 -m venv /venv
##### memberikan kepemilikan folder pada user saat ini:
> sudo chown $USER:$USER /venv
##### Activate virtual environment
> source /venv/bin/activate
##### Install pip latest version
> pip install -U pip
##### Install Ansible. Kolla Ansible requires at least Ansible 4 and supports up to 5.
> pip install 'ansible>=4,<6'

### Install Kolla-ansible:
##### Install kolla-ansible menggunakan virtual environtment
> pip install git+https://opendev.org/openstack/kolla-ansible@stable/zed
##### membuat kolla directory sebagai tempat utama installasi openstack dan berikan kepemilikan folder ke user saat ini:
> sudo mkdir -p /etc/kolla

> sudo chown $USER:$USER /etc/kolla
##### Copy globals.yml and passwords.yml to /etc/kolla directory.
cp /venv/share/kolla-ansible/ansible/inventory/* /etc/kolla

### Install Ansible Galaxy dependencies 
> kolla-ansible install-deps

### Konfigurasi ansible
Untuk hasil terbaik, konfigurasi Ansible sebaiknya disesuaikan dengan lingkungan Anda. Sebagai contoh, tambahkan opsi-opsi berikut ke dalam file konfigurasi Ansible di /etc/ansible/ansible.cfg.
