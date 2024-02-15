# Training deploy OpenStack menggunakan Kolla-Ansible dengan Environment OpenStack Sardina (OPENSTACK ON OPENSTACK)
Dalam tutorial Phase-2, openstack akan di install kedalam 5 node server, 3 node controller dan 2 node compute. 3 node controller harus High avability(HA), jadi jika salah satu controller mati maka openstack akan tetap berjalan(No Downtime).

## LAB TOPOLOGY
![openstack-phase2 drawio](https://github.com/Yezato/DATACOMM/assets/95903200/9138451c-07e3-4cb8-868e-2deab586d59e)

### Tutorial Ini Menggunakan Spesifikasi Seperti Dibawah Ini: 
- OpenStack version: ZED
- OS : Ubuntu 22.04
- 2 network interfaces
- 5 node server (3 controller, 2 compute)
- 8GB main memory di setiap node
- 50GB disk root di setiap node
- 50GB disk root & 50GB disk space untuk cinder 
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

# TAHAP 1: Persiapan
##### Create Instance
1. Buatlah 5 node server yang terdiri dari 3 controller dan 2 node compute. setiap node server memiliki spesifikasi yang sama.
2. Tambahkan interface IP Public dan IP Internal yang sudah dibuat sebelumnya ke 5 node server.
3. buatlah volume dengan ukuran 50GB yang akan di attach di node compute-2




# TAHAP 2: Deploy OpenStack Menggunakan Kolla-Ansible.
### Ubah Hostname dan Mapping Hostname Pada Tiap Node
Masuk dan login kedalam 5 VM yang sudah dibuat sebelumnya, kemudian edit hostname tiap node agar memudahkan dalam mengidentifikasi tiap node server. kemudian lakukan sebagai berikut:
##### Edit Hostname (semua node)
```lua
sudo hostnamectl set-hostname controller
```
##### Edit Hosts Pada Folder /etc/hosts (hanya controller-1)
secara otomatis kolla-ansible akan menggenerate hosts sesuai dengan
```lua
sudo nano /etc/hosts
```
```lua
192.178.100.22 controller-1
192.178.100.66 controller-2
192.178.100.76 controller-3
192.178.100.162 compute-1
192.178.100.31 compute-2
```
#### Lakukan Perintah Dibawah Ini Pada Tiap Node Server, Untuk Mengecek Apakah Konfigurasi Telah Berhasil:
```lua
ping -c 5 controller; ping -c 5 compute
```
### Buat Keypair (controller-1)
```lua
ssh-keygen -t rsa
ssh-copy-id -i .ssh/id_rsa.pub openstack@controller-1
ssh-copy-id -i .ssh/id_rsa.pub openstack@controller-2
ssh-copy-id -i .ssh/id_rsa.pub openstack@controller-3
ssh-copy-id -i .ssh/id_rsa.pub openstack@compute-1
ssh-copy-id -i .ssh/id_rsa.pub openstack@compute-2
```

### Konfigurasi VGS (Compute-2)
Di tutorial ini, cinder akan diarahkan menggunakan LVM backend sebagai storage volume dan cinder akan di install pada node compute-2: 
```lua
sudo pvcreate /dev/vdb
```
```lua
sudo vgcreate cinder-volumes /dev/vdb
```
```lua
sudo vgs
```
### Instal Dependencies (Semua Node Server):
```lua
sudo apt update
```
```lua
sudo apt install git python3-dev libffi-dev gcc libssl-dev sshpass -y 
```
### Instal Dependencies Menggunakan Virtual Environment (hanya controller-1):
##### Membuat Virtual Environment.
```lua
sudo apt install python3-venv -y
```
```lua
sudo python3 -m venv /venv
```
##### Memberikan Kepemilikan Folder Pada User Saat Ini:
```lua
sudo chown -R $USER:$USER /venv/
```
##### Mengaktifkan Virtual Environment
```lua
source /venv/bin/activate
```
##### Instal pip Versi Terbaru
```lua
pip install -U pip
```
##### Untuk Menginstal Ansible, Anda Perlu Menggunakan Versi Ansible Minimal 4 Dan Mendukung Hingga Versi 5. 
```lua
pip install 'ansible>=4,<6'
```
### Instal Kolla-ansible:
##### Install Kolla-Ansible Menggunakan Virtual Environtment
```lua
pip install git+https://opendev.org/openstack/kolla-ansible@stable/zed
```
##### Membuat Kolla Directory Sebagai Tempat Utama Installasi Openstack Dan Berikan Kepemilikan Folder Ke User Saat Ini:
```lua
sudo mkdir -p /etc/kolla
```
```lua
sudo chown -R $USER:$USER /etc/kolla
```
##### Salin globals.yml dan passwords.yml Kedalam Folder /etc/kolla
```lua
cp -r /venv/share/kolla-ansible/etc_examples/kolla/* /etc/kolla
```
##### Salin Semua File Inventory "multinode" Ke Dalam Direktori Saat Ini..
```lua
cp /venv/share/kolla-ansible/ansible/inventory/multinode /etc/kolla
```
### Instal Ansible Galaxy Dependencies 
```lua
kolla-ansible install-deps
```
### Konfigurasi Ansible
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
- ansible_user menunjukkan bahwa ansible akan menggunakan pengguna 'ubuntu' saat berinteraksi dengan host tersebut.
- ansible_password menunjukkan kata sandi yang digunakan untuk mengotentikasi ke host.
- ansible_become=true menandakan bahwa ansible akan menggunakan privilege escalation (menggantikan hak akses) saat menjalankan - perintah di host tersebut. biasanya digunakan ketika perintah memerlukan izin lebih tinggi, seperti penginstalan paket atau konfigurasi sistem. 
```lua
[control]
controller-[1:3]

[network]
controller-[1:3]

[compute]
compute-[1:2]

[monitoring]
controller-[1:3] 

[storage]
compute-2

[deployment]
localhost ansible_connection=local
```


##### Periksa Apakah Konfigurasi Inventory Sudah Benar Atau Tidak, Jalankan:
```lua
ansible -i multinode all -m ping
```
### Kolla passwords
Kata sandi yang digunakan dalam deployment disimpan dalam file /etc/kolla/passwords.yml. Semua kata sandi kosong yang ada dalam file ini harus diisi baik secara manual atau dengan menjalankan generator kata sandi acak seperti dibawah ini:
```lua
kolla-genpwd
```
### Konfigurasi global.yml
```lua
nano /etc/kolla/globals.yml
```
```lua
kolla_base_distro: "ubuntu"
kolla_internal_vip_address: "192.198.101.254"
network_interface: "ens4"  # ip dari interface ini akan digunakan untuk komunikasi antar node (IP management)
neutron_external_interface: "ens3"  # interface ini akan didedikasikan untuk jaringan eksternal (Public) Neutron, bisa berupa vlan atau flat, tergantung pada bagaimana jaringan-jaringan tersebut dibuat. Antarmuka ini harus aktif tanpa alamat IP. Jika tidak, instance tidak akan dapat mengakses jaringan eksternal.
enable_glance: "{{ enable_openstack_core | bool }}"
enable_haproxy: "yes"
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
##### Bootstrap Server Dengan Dependensi Kolla
```lua
kolla-ansible -i ./multinode bootstrap-servers
```
##### Pengecekan Hosts Sebelum Menjalankan Deployment
```lua
kolla-ansible -i ./multinode prechecks
```
##### Menjalankan Deployment OpenStack Kolla-Ansible.
```lua
kolla-ansible -i ./multinode deploy
```
##### Jalankan Post-Deploy Untuk Menghasilkan Berkas admin-openrc.sh
```lua
kolla-ansible post-deploy
```

### Cara Menggunakan Openstack
Ada dua cara umum untuk mengelola OpenStack, pertama menggunakan OpenStack CLI (Command Line Interface), yang dimana memungkinkan administrator untuk berinteraksi dengan OpenStack menggunakan baris perintah dari terminal atau shell. Kedua menggunakan Horizon sebagai antarmuka web berbasis GUI yang menyediakan tampilan dan kontrol visual atas sumber daya OpenStack. Ini memungkinkan pengguna dan administrator untuk mengakses dan mengelola proyek, instance, dan sumber daya lainnya melalui browser web.
#### Menggunakan OpenStack CLI
##### Instal OpenStack CLI Client
```lua
pip install python-openstackclient -c https://releases.openstack.org/constraints/upper/zed
```
##### Gunakan admin-openrc.sh Untuk Login Openstack CLI
```lua
source admin-openrc.sh
``` 
'admin-openrc.sh' adalah file skrip yang berisi variabel lingkungan (environment variables) yang diperlukan untuk mengakses API OpenStack menggunakan Command Line Interface (CLI) dari terminal atau shell. File ini biasanya digunakan untuk mengatur variabel lingkungan yang diperlukan untuk mengotentikasi pengguna dan menentukan endpoint untuk layanan OpenStack.
##### Jalankan Perintah Dibawah Untuk Mengecek Service Yang Ter-install di Openstack
```lua
openstack service list
```
#### Akses Dashboard 
##### Untuk username dan password anda dapat temukan pada file 'admin-openrc.sh'
```lua
cat admin-openrc.sh | grep -e "OS_USERNAME" -e "OS_PASSWORD="
```
##### Anda dapat mengakses dashbord horizon menggunakan Virtual IP yang telah dibuat sebelumnya
```lua
http://192.198.101.254
```
