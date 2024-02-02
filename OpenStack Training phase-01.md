# How to Install OpenStack Using Kolla Ansible
#### This Tutorial Utilizes The Specifications Below:
- OpenStack version: ZED
- OS : Ubuntu 22.04
- 2 network interfaces
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

## Install dependencies:
> sudo apt update && sudo apt upgrade

> sudo apt install git python3-dev libffi-dev gcc libssl-dev

## Install dependencies using a virtual environment:
##### Install the virtual environment dependencies.
> sudo apt install python3-venv
##### Create virtual environment 
> python3 -m venv /venv
##### Activate virtual environment
> source /venv/bin/activate
##### Install pip latest version
> pip install -U pip
##### Install Ansible. Kolla Ansible requires at least Ansible 4 and supports up to 5.
> pip install 'ansible>=4,<6'
