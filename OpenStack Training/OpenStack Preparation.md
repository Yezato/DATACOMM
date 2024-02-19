# Persiapan
sebelum masuk kedalam training anda harus menyiapkan VM/Instance ....

### Training Ini Menggunakan Spesifikasi Seperti Dibawah Ini: 
- OpenStack version: ZED
- OS : Ubuntu 22.04
- 2 network interfaces
- 2 node server (1 controller, 1 compute)
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

# TAHAP 1: Buat Network Public dan Internal
Masuk Kedalam dashboard horizon sardina lalu pilih network, klik Network, setalah daftar network muncul carilah tombol 'create network' lalu isi formulir tersebut untuk membuat public dan internal network
![image](https://github.com/Yezato/DATACOMM/assets/95903200/97e42ece-b3d5-46c0-8ea3-0bec3879e8b6)
##### IP Public
```lua
Network Name: ip-public-openstack
Availability Zone Hints: AZ_Public01_DC2
Subnet Name: ip-public-subnet-openstack
Network Address: 192.178.200.0/24
IP Version: IPv4
Gateway IP: 192.168.200.1
DNS Name Server: 8.8.8.8
```
##### IP internal
```lua
Network Name: ip-internal-openstack
Availability Zone Hints: AZ_Public01_DC2
Subnet Name: ip-internal-subnet-openstack
Network Address: 192.178.100.0/24
IP Version: IPv4
Gateway IP: 192.178.100.1
DNS Name Server: 8.8.8.8
```
Public network digunakan untuk akses publik ke layanan cloud seperti instance dan floating IPs. Internal network digunakan untuk komunikasi antara instance di dalam infrastruktur cloud dan meningkatkan keamanan dengan membatasi akses langsung dari internet.
# Buat Router
Masuk Kedalam dashboard horizon sardina lalu pilih network, Klik Router, setalah daftar router muncul carilah tombol 'create router' lalu isi formulir tersebut untuk membuat router
![image](https://github.com/Yezato/DATACOMM/assets/95903200/3170a4cd-1697-4532-8572-aa188dff7e4e)
##### Isi formulir router
```lua
Router name: router
External Network: Public_Subnet03_DC2
Availability Zone Hints: AZ_Public01_DC2
```
##### Tambahkan Public dan Internal network kedalam router
Klik nama router yang sudah dibuat sebelumnya, selanjutnya klik interface, dan tambahkan interface ip-public-openstack dan ip-internal-openstack yang sudah dibuat sebelumnya
![image](https://github.com/Yezato/DATACOMM/assets/95903200/2213b3d5-f9c2-4aef-a724-aa3822d8ac11)

### cara buat Instance 
Masuk Kedalam dashboard horizon sardina lalu pilih Compute, Klik instance, setalah daftar instance muncul carilah tombol 'Launch Instance' 
![image](https://github.com/Yezato/DATACOMM/assets/95903200/b59f6678-6a20-4885-bdd6-25ca39446daa)
##### Isi formulir Instance
###### Details
```lua
Instance name: Node
Availability Zone: AZ_Public01_DC2
count: 1
```
###### Source
```lua
Select Boot Source: Image
Image: Ubuntu 22.04 LTS
```
###### Flavor
Harap diperhatikan saat memilih flavor, pilih resource sesuai kebutuhan
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
###### Configuration
pada langkah ini vm akan secara otomatis mengkonfigurasi credential berupa usernama "OpenStack" dan password "D4t4c0mm"

```lua
#cloud-config
ssh_pwauth: true
users:
  - name: openstack
    passwd: $6$EjrxuFyC1jH.koaB$aJZOWT4GxcCVvBtCI31nt6JRnzpgySRYL.Gja7XXZE2/O4Ejx36xUL/.vllUGe.kpBLtv08aZQ6Q2q2LJhA9s.
    lock_passwd: false
    sudo: ["ALL=(ALL) NOPASSWD:ALL"]
    shell: /bin/bash
```
klik launch instance maka openstack sardina secara otomatis akan membuat Instancesesuai dari konfigurasi yang diberikan. 

### Disable security port
Agar tidak ada kendala saat instalasi openstack on openstack, matikan security grub pada kedua VM yang telah dibuat sebelumnya.
![image](https://github.com/Yezato/DATACOMM/assets/95903200/a556cd77-efa7-47f4-a484-d27548731eb1)
```lua
dashboard openstack > Compute > Instance > [pilih vm yang dibuat sebelumnya] > interface > edit port
```
Matikan Port Security seperti pada gambar diatas. Demi keamanan hidupkan kembali security port setalah selesai proses deployment openstack kedalam 2 VM tersebut.

# CARA BUAT VOLUME
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


