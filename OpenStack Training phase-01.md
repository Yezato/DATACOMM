# CARA INSTAL OPENSTACK MULTINODE MENGGUNAKAN KOLLA ANSIBLE
Dalam tutorial Phase-1 ini openstack akan di install kedalam 2 node server, 1 node controller dan 1 node compute. 

## LAB TOPOLOGY


### Tutorial ini menggunakan spesifikasi seperti dibawah ini: 
- OpenStack version: ZED
- OS : Ubuntu 22.04
- 2 network interfaces
- 2 node server (1 controller, 1 compute)
- 8GB main memory
- 50GB disk space

### Service OpenStack:
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

### Ubah Hostname dan Mapping Hostname Pada Tiap Node
##### Node Contoller
```lua
sudo hostnamectl set-hostname controller
```
##### Edit Hosts Pada Folder /etc/hosts
```lua
sudo nano /etc/hosts
```
```lua
192.168.101.206 controller
192.168.101.6 compute
```
Node Compute
```lua
sudo hostnamectl set-hostname compute
```
edit hosts pada folder
```lua
sudo nano /etc/hosts
```
```lua
192.168.101.206 controller
192.168.101.6 compute
```
### Instal dependencies:
```lua
sudo apt update && sudo apt upgrade
sudo apt install git python3-dev libffi-dev gcc libssl-dev
```
### Instal dependencies menggunakan virtual environment:
##### membuat virtual environment.
```lua
sudo apt install python3-venv
```
```lua
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
```
```lua
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
### Konfigurasi inventory
##### Edit multinode
```lua
[control]
10.0.0.[10:12] ansible_user=ubuntu ansible_password=foobar ansible_become=true
# Ansible supports syntax like [10:12] - that means 10, 11 and 12.
# Become clause means "use sudo".

[network:children]
control
# when you specify group_name:children, it will use contents of group specified.

[compute]
10.0.0.[13:14] ansible_user=ubuntu ansible_password=foobar ansible_become=true

[monitoring]
10.0.0.10
# This group is for monitoring node.
# Fill it with one of the controllers' IP address or some others.

[storage:children]
compute

[deployment]
localhost       ansible_connection=local become=true
# use localhost and sudo
```


##### Periksa apakah konfigurasi inventory sudah benar atau tidak, jalankan:
```lua
ansible -i multinode all -m ping
```
### Kolla passwords
Kata sandi yang digunakan dalam deployment disimpan dalam file /etc/kolla/passwords.yml. Semua kata sandi kosong yang ada dalam file ini harus diisi baik secara manual atau dengan menjalankan generator kata sandi acak seperti dibawah ini:
```lua
kolla-genpwd
```
### konfigurasi global.yml
```lua
kolla_base_distro: "rocky"
network_interface: "eth0"
neutron_external_interface: "eth1"
kolla_internal_vip_address: "10.1.0.250"
enable_cinder: "yes"
```
### Deployment 
##### Bootstrap server dengan dependensi kolla
```lua
kolla-ansible -i ./multinode bootstrap-servers
```
##### Pengecekan hosts sebelum menjalankan deployment
```lua
kolla-ansible -i ./multinode prechecks
```
##### Terakhir, lanjutkan ke implementasi.
```lua
kolla-ansible -i ./multinode deploy
```


### Cara Menggunakan OpenStack

###### Instal openstack cli client
```lua
pip install python-openstackclient -c https://releases.openstack.org/constraints/upper/zed
```
```lua
kolla-ansible post-deploy
```
