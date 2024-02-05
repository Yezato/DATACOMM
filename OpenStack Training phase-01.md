# CARA INSTAL OPENSTACK MULTINODE MENGGUNAKAN KOLLA ANSIBLE
Dalam tutorial Phase-1 ini openstack akan di install kedalam 2 node server, 1 node controller dan 1 node compute. 

## LAB TOPOLOGY


### Tutorial ini menggunakan spesifikasi seperti dibawah ini: 
- OpenStack version: ZED
- OS : Ubuntu 22.04
- 2 network interfaces
- 2 node server (1 controller, 1 compute)
- 8GB main memory
- 50GB disk space (/dev/vdb)

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
#### Node Contoller
##### Edit Hostname
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
#### Node Compute
##### Edit Hostname
```lua
sudo hostnamectl set-hostname compute
```
##### Edit Hosts Pada Folder /etc/hosts
```lua
sudo nano /etc/hosts
```
```lua
192.168.101.206 controller
192.168.101.6 compute
```
#### Lakukan perintah dibawah ini pada tiap node server, untuk mengecek apakah konfigurasi telah berhasil:
```lua
ping -c 5 controller; ping -c 5 compute
```

### Konfigurasi VGS pada node Compute
Di tutorial ini, cinder akan diarahkan menggunakan LVM backend sebagai storage volume. dan cinder akan di install pada node compute: 
```lua
sudo pvcreate /dev/vdb
```
```lua
sudo vgcreate cinder-volumes /dev/vdb
```
```lua
sudo vgs
```
### Instal Dependencies Pada Tiap Node Server:
```lua
sudo apt update && sudo apt upgrade -y
```
```lua
sudo apt install git python3-dev libffi-dev gcc libssl-dev sshpass -y 
```
### Instal dependencies menggunakan virtual environment:
##### membuat virtual environment.
```lua
sudo apt install python3-venv -y
```
```lua
sudo python3 -m venv /venv
```
##### memberikan kepemilikan folder pada user saat ini:
```lua
sudo chown -R $USER:$USER /venv/
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
sudo chown -R $USER:$USER /etc/kolla
```
##### salin globals.yml and passwords.yml kedalam folder /etc/kolla
```lua
cp -r /venv/share/kolla-ansible/etc_examples/kolla/* /etc/kolla
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
sudo mkdir /etc/ansible
```
```lua
sudo nano /etc/ansible/ansible.cfg
```
```lua
[defaults]
host_key_checking=False
pipelining=True
forks=100
```
### Konfigurasi inventory
##### Edit multinode

```lua
nano /etc/kolla/multinode
```
- ansible_user Menunjukkan bahwa Ansible akan menggunakan pengguna 'ubuntu' saat berinteraksi dengan host tersebut.
- ansible_password Menunjukkan kata sandi yang digunakan untuk mengotentikasi ke host.
- ansible_become=true Menandakan bahwa Ansible akan menggunakan privilege escalation (menggantikan hak akses) saat menjalankan - perintah di host tersebut. Biasanya digunakan ketika perintah memerlukan izin lebih tinggi, seperti penginstalan paket atau konfigurasi sistem. 
```lua
[control]
controller ansible_user=ubuntu ansible_password=trootent ansible_become=true

[network]
controller ansible_user=ubuntu ansible_password=trootent ansible_become=true

[compute]
compute ansible_user=ubuntu ansible_password=trootent ansible_become=true

[monitoring]
controller ansible_user=ubuntu ansible_password=trootent ansible_become=true

[storage]
compute ansible_user=ubuntu ansible_password=trootent ansible_become=true

[deployment]
localhost ansible_connection=local ansible_become=true
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
nano /etc/kolla/globals.yml
```
```lua
kolla_base_distro: "ubuntu"
kolla_internal_vip_address: "192.198.101.254"
network_interface: "ens4"  # ip dari interface ini akan digunakan untuk komunikasi antar node (IP management)
neutron_external_interface: "ens3"  # interface ini akan didedikasikan untuk jaringan eksternal (atau publik) Neutron, bisa berupa vlan atau flat, tergantung pada bagaimana jaringan-jaringan tersebut dibuat. Antarmuka ini harus aktif tanpa alamat IP. Jika tidak, instance tidak akan dapat mengakses jaringan eksternal.

enable_glance: "{{ enable_openstack_core | bool }}"
enable_keystone: "{{ enable_openstack_core | bool }}"
enable_neutron: "{{ enable_openstack_core | bool }}"
enable_nova: "{{ enable_openstack_core | bool }}"
enable_aodh: "yes"
enable_ceilometer: "yes"
enable_cinder: "yes"
enable_cinder_backend_lvm: "yes"
enable_gnocchi: "yes"
enable_horizon: "{{ enable_openstack_core | bool }}"
enable_placement: "{{ enable_nova | bool or enable_zun | bool }}"
cinder_volume_group: "cinder-volumes"
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
##### Jalankan post-deploy untuk menghasilkan berkas admin-openrc.sh
```lua
kolla-ansible post-deploy
```

### Cara Menggunakan OpenStack
Ada dua cara umum untuk mengelola OpenStack, pertama menggunakan OpenStack CLI (Command Line Interface), yang dimana memungkinkan administrator untuk berinteraksi dengan OpenStack menggunakan baris perintah dari terminal atau shell. Kedua menggunakan Horizon sebagai antarmuka web berbasis GUI yang menyediakan tampilan dan kontrol visual atas sumber daya OpenStack. Ini memungkinkan pengguna dan administrator untuk mengakses dan mengelola proyek, instance, dan sumber daya lainnya melalui browser web.
#### Menggunakan OpenStack CLI
##### Instal openstack cli client
```lua
pip install python-openstackclient -c https://releases.openstack.org/constraints/upper/zed
```
##### Gunakan admin-openrc.sh untuk login openstack cli
```lua
source admin-openrc.sh
``` 
'admin-openrc.sh' adalah file skrip yang berisi variabel lingkungan (environment variables) yang diperlukan untuk mengakses API OpenStack menggunakan Command Line Interface (CLI) dari terminal atau shell. File ini biasanya digunakan untuk mengatur variabel lingkungan yang diperlukan untuk mengotentikasi pengguna dan menentukan endpoint untuk layanan OpenStack.
