# Training deploy OpenStack menggunakan Kolla-Ansible dengan Environment OpenStack Sardina (OPENSTACK ON OPENSTACK)
Dalam tutorial Phase-1 ini openstack akan di install kedalam 2 VM, 1 VM controller dan 1 VM compute. Kedua VM tersebut dibuat melalui OpenStack.
## LAB TOPOLOGY
![OpenStack-phase01 drawio](https://github.com/Yezato/DATACOMM/assets/95903200/7cae54dd-4456-4f62-8e13-280562fb984f)
### Tutorial Ini Menggunakan Spesifikasi Seperti Dibawah Ini: 
```bash
- OpenStack version: ZED
- OS : Ubuntu 22.04
- 2 network interfaces
- 2 node server (1 controller, 1 compute)
- 8GB main memory di setiap node
- 50GB disk root di setiap node
- 50GB disk root & 50GB disk space untuk cinder
```
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
### Buat Network Public dan Internal
Masuk Kedalam dashboard horizon sardina lalu pilih network, klik Network, setalah daftar network muncul carilah tombol 'create network' lalu isi formulir tersebut untuk membuat public dan internal network
![image](https://github.com/Yezato/DATACOMM/assets/95903200/97e42ece-b3d5-46c0-8ea3-0bec3879e8b6)
##### IP Public
```lua
Network Name: ip-public-openstack
Availability Zone Hints: AZ_Public01_DC3
Subnet Name: ip-public-subnet-openstack
Network Address: 192.168.100.0/24
IP Version: IPv4
Gateway IP: 192.168.100.1
DNS Name Server: 8.8.8.8
```
##### IP internal
```lua
Network Name: ip-internal-openstack
Availability Zone Hints: AZ_Public01_DC3
Subnet Name: ip-internal-subnet-openstack
Network Address: 192.168.101.0/24
IP Version: IPv4
Gateway IP: 192.168.101.1
DNS Name Server: 8.8.8.8
```
Public network digunakan untuk akses publik ke layanan cloud seperti instance dan floating IPs. Internal network digunakan untuk komunikasi antara instance di dalam infrastruktur cloud dan meningkatkan keamanan dengan membatasi akses langsung dari internet.
### Buat Router
Masuk Kedalam dashboard horizon sardina lalu pilih network, Klik Router, setalah daftar router muncul carilah tombol 'create router' lalu isi formulir tersebut untuk membuat router
![image](https://github.com/Yezato/DATACOMM/assets/95903200/3170a4cd-1697-4532-8572-aa188dff7e4e)
##### Isi formulir router
```lua
Router name: router
External Network: Public_Subnet03_DC3
Availability Zone Hints: AZ_Public01_DC3
```
##### Tambahkan Public dan Internal network kedalam router
Klik nama router yang sudah dibuat sebelumnya, selanjutnya klik interface, dan tambahkan interface ip-public-openstack dan ip-internal-openstack yang sudah dibuat sebelumnya
![image](https://github.com/Yezato/DATACOMM/assets/95903200/2213b3d5-f9c2-4aef-a724-aa3822d8ac11)

### Buat 2 VM untuk controller dan compute
Masuk Kedalam dashboard horizon sardina lalu pilih Compute, Klik instance, setalah daftar instance muncul carilah tombol 'Launch Instance' 
![image](https://github.com/Yezato/DATACOMM/assets/95903200/b59f6678-6a20-4885-bdd6-25ca39446daa)
##### Isi formulir Instance
###### Details
```lua
Instance name: Node
Availability Zone: AZ_Public01_DC3
count: 2
```
###### Source
```lua
Select Boot Source: Image
Image: Ubuntu 22.04 LTS
```
###### Flavor
```lua
GP.2C8G
```
###### Network
```lua
ip-public-openstack
ip-internal-openstack
```
###### Security Group
```lua
allow-all
```
klik launch instance maka openstack sardina secara otomatis akan membuat 2 VM yang bernama Node-1 dan Node-2 sesuai dari perintah yang diberikan. 

### Disable security port
Agar tidak ada kendala saat instalasi openstack on openstack, matikan security grub pada kedua VM yang telah dibuat sebelumnya.
![image](https://github.com/Yezato/DATACOMM/assets/95903200/a556cd77-efa7-47f4-a484-d27548731eb1)
```lua
dashboard openstack > Compute > Instance > [pilih vm yang dibuat sebelumnya] > interface > edit port
```
Matikan Port Security seperti pada gambar diatas. Demi keamanan hidupkan kembali security port setalah selesai proses deployment openstack kedalam 2 VM tersebut.

### Tambahkan Disk 50GB untuk backend-LVM cinder
Masuk Kedalam dashboard horizon sardina lalu pilih Volumes, Klik Volumes, setalah daftar volume muncul carilah tombol 'Create Volume' 
![image](https://github.com/Yezato/DATACOMM/assets/95903200/4193a2fb-1313-4888-9843-eec2748178d3)
##### Isi Formulir
```lua
Volume Name: cinder-vol
Volume Source: no source, empty volume
Type: ceph
size(GiB): 50
Availability Zone: AZ_Public01_DC3
```
##### Attach volume ke instance compute
```Lua
Dashboard Horizon > Volumes > Volumes > [pilih instance, cari tombol 'edit volume' ] > Manage Attachments > Attach ke instance yang di jadikan VM compute
```




# TAHAP 2: Deploy OpenStack Menggunakan Kolla-Ansible.
### Ubah Hostname dan Mapping Hostname Pada Tiap Node
Masuk dan login kedalam 2 VM yang sudah dibuat sebelumnya, dan lakukan tahap instalasi openstack menggunakan kolla-ansible. 
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
#### Lakukan Perintah Dibawah Ini Pada Tiap Node Server, Untuk Mengecek Apakah Konfigurasi Telah Berhasil:
```lua
ping -c 5 controller; ping -c 5 compute
```

### Konfigurasi VGS Pada Node Compute
Di tutorial ini, cinder akan diarahkan menggunakan LVM backend sebagai storage volume dan cinder akan di install pada node compute: 
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
### Instal Dependencies Menggunakan Virtual Environment:
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
