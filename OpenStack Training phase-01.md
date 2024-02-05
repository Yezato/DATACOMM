# Cara Instal OpenStack Multinode Menggunakan Kolla Ansible
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



### Instal dependencies:
```lua
sudo apt update && sudo apt upgrade
sudo apt install git python3-dev libffi-dev gcc libssl-dev
```
### Instal dependencies menggunakan virtual environment:
##### membuat virtual environment.
```lua
sudo apt install python3-venv
python3 -m venv /venv
```
##### memberikan kepemilikan folder pada user saat ini:
```lua
sudo chown $USER:$USER /venv
```
##### mengaktifkan virtual environment
```lua
source /venv/bin/activate
```
##### Instal pip versi terbaru
```lua
pip install -U pip
```
##### Untuk menginstal Ansible, Anda perlu menggunakan versi Ansible minimal 4 dan mendukung hingga versi 5. 
```lua
pip install 'ansible>=4,<6'
```
### Instal Kolla-ansible:
##### Install kolla-ansible menggunakan virtual environtment
```lua
pip install git+https://opendev.org/openstack/kolla-ansible@stable/zed
```
##### membuat kolla directory sebagai tempat utama installasi openstack dan berikan kepemilikan folder ke user saat ini:
```lua
sudo mkdir -p /etc/kolla
sudo chown $USER:$USER /etc/kolla
```
##### salin globals.yml and passwords.yml kedalam folder /etc/kolla
```lua
cp -r /path/to/venv/share/kolla-ansible/etc_examples/kolla/* /etc/kolla
```
##### Salin semua file inventory "multinode" ke dalam direktori saat ini..
```lua
cp /venv/share/kolla-ansible/ansible/inventory/multinode /etc/kolla
```
### Instal Ansible Galaxy dependencies 
```lua
kolla-ansible install-deps
```
### Konfigurasi ansible
Untuk hasil terbaik, konfigurasi Ansible sebaiknya disesuaikan dengan environment Anda. Sebagai contoh, tambahkan opsi-opsi berikut ke dalam file konfigurasi Ansible di /etc/ansible/ansible.cfg.
```lua
[defaults]
host_key_checking=False
pipelining=True
forks=100
```

