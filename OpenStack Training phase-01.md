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
> sudo apt update && sudo apt upgrade

> sudo apt install git python3-dev libffi-dev gcc libssl-dev

### Instal dependencies menggunakan virtual environment:
##### membuat virtual environment.
> sudo apt install python3-venv

> python3 -m venv /venv
##### memberikan kepemilikan folder pada user saat ini:
> sudo chown $USER:$USER /venv
##### mengaktifkan virtual environment
> source /venv/bin/activate
##### Instal pip versi terbaru
> pip install -U pip
##### Untuk menginstal Ansible, Anda perlu menggunakan versi Ansible minimal 4 dan mendukung hingga versi 5. 
> pip install 'ansible>=4,<6'

### Instal Kolla-ansible:
##### Install kolla-ansible menggunakan virtual environtment
> pip install git+https://opendev.org/openstack/kolla-ansible@stable/zed
##### membuat kolla directory sebagai tempat utama installasi openstack dan berikan kepemilikan folder ke user saat ini:
> sudo mkdir -p /etc/kolla

> sudo chown $USER:$USER /etc/kolla
##### salin globals.yml and passwords.yml kedalam folder /etc/kolla
cp /venv/share/kolla-ansible/ansible/inventory/* /etc/kolla

### Instal Ansible Galaxy dependencies 
> kolla-ansible install-deps

### Konfigurasi ansible
Untuk hasil terbaik, konfigurasi Ansible sebaiknya disesuaikan dengan environment Anda. Sebagai contoh, tambahkan opsi-opsi berikut ke dalam file konfigurasi Ansible di /etc/ansible/ansible.cfg.
**NOTE**
[defaults]
host_key_checking=False
pipelining=True
forks=100

<div class="warning" style='padding:0.1em; background-color:#E9D8FD; color:#69337A'>
<span>
<p style='margin-top:1em; text-align:center'>
<b>On the importance of sentence length</b></p>
<p style='margin-left:1em;'>
This sentence has five words. Here are five more words. Five-word sentences are fine. But several together bocome monotonous. Listen to what is happening. The writing is getting boring. The sound of it drones. It's like a stuck record. The ear demands some variety.<br><br>
    Now listen. I vary the sentence length, and I create music. Music. The writing sings. It has a pleasent rhythm, a lilt, a harmony. I use short sentences. And I use sentences of medium length. And sometimes when I am certain the reader is rested, I will engage him with a sentence of considerable length, a sentence that burns with energy and builds with all the impetus of a crescendo, the roll of the drums, the crash of the cymbals -- sounds that say listen to this, it is important.
</p>
<p style='margin-bottom:1em; margin-right:1em; text-align:right; font-family:Georgia'> <b>- Gary Provost</b> <i>(100 Ways to Improve Your Writing, 1985)</i>
</p></span>
</div>
