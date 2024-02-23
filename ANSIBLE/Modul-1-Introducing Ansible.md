# Introducing Ansible

## GOAL
Describe the fundamental Ansible concepts and how it is used, and install Red Hat Ansible Engine.


## OBJECTIVES
- Describe the motivation for automating Linux administration tasks with Ansible, fundamental Ansible concepts, and Ansible’s basic architecture.
- Install Ansible on a control node and describe the distinction between community Ansible and Red Hat Ansible Engine.
- Automating Linux Administration with Ansible (and Quiz).
- Installing Ansible (and Guided Exercise).

# Automation and Linux System Administration
For many years, most system administration and infrastructure management has relied on manual tasks performed through graphical or command-line user interfaces. System administrators often work from checklists, other documentation, or a memorized routine to perform standard tasks. This approach is error-prone. It is easy for a system administrator to skip a step or perform a step mistakenly. Often there is limited verification that the steps were performed properly or that they result in the expected outcome.

Furthermore, by managing each server manually and independently, it is very easy for many servers that are supposed to be identical in configuration to be different in minor (or major) ways. This can make maintenance more difficult and introduce errors or instability into the IT environment.

Automation can help avoid the problems caused by manual system administration and infrastructure management. As a system administrator, you can use it to ensure that all your systems are quickly and correctly deployed and configured. This allows you to automate the repetitive tasks in your daily schedule, freeing up your time and allowing you to focus on more critical things. For your organization, this means you can more quickly roll out the next version of an application or updates to a service.


## Infrastructure as Code (IaC)
A good automation system allows you to implement Infrastructure as Code practices. Infrastructure as Code means that you can use a machine-readable automation language to define and describe the state you want your IT infrastructure to be in. Ideally, this automation language should also be very easy for humans to read, because then you can easily understand what the state is and make changes to it. This code is then applied to your infrastructure to ensure that it is actually in that state.

If the automation language is represented as simple text files, it can easily be managed in a version control system like software code. The advantage of this is that every change can be checked into the version control system, so you have a history of the changes you make over time. If you want to revert to an earlier known-good configuration, you simply can check out that version of the code and apply it to your infrastructure.

This builds a foundation to help you follow best practices in DevOps. Developers can define their desired configuration in the automation language. Operators can review those changes more easily to provide feedback, and use that automation to reproducibly ensure that systems are in the state expected by the developers.

Mitigating Human Error by reducing the tasks performed manually on servers using automation of tasks and Infrastructure as Code practices, your servers will be in consistent configurations more often. This means that you need to become accustomed to making changes through updating your automation code, rather than manually applying them to your servers. Otherwise, you run the risk of losing manually-applied changes the next time you apply changes through your automation.

Automation allows you to use code review, peer review by multiple subject matter experts, and documentation of the procedure by the automation itself to reduce your operational risks.

Ultimately, you can enforce that changes to your IT infrastructure must be made through automation in order to mitigate human error.

# What is Ansible?
Ansible is an open source automation platform. It is a simple automation language that can perfectly describe an IT application infrastructure in Ansible Playbooks. It is also an automation engine that runs Ansible Playbooks.

Ansible can manage powerful automation tasks and can adapt to many different workflows and environments. At the same time, new users of Ansible can very quickly use it to become productive.


### Ansible is Simple
Ansible Playbooks provide human-readable automation. This means that playbooks are automation tools that are also easy for humans to read, comprehend, and change. No special coding skills are required to write them. Playbooks execute tasks in order. The simplicity of playbook design makes them usable by every team, which allows people new to Ansible to get productive quickly.


### Ansible is Powerful
You can use Ansible to deploy applications, for configuration management, for workflow automation, and for network automation. Ansible can be used to orchestrate the entire application life cycle.


### Ansible is Agentless
Ansible is built around an agentless architecture. Typically, Ansible connects to the hosts it manages using OpenSSH or WinRM and runs tasks, often (but not always) by pushing out small programs called Ansible modules to those hosts. These programs are used to put the system in a specific desired state. Any modules that are pushed are removed when Ansible is finished with its tasks. You can start using Ansible almost immediately because no special agents need to be approved for use and then deployed to the managed hosts. Because there are no agents and no additional custom security infrastructure, Ansible is more efficient and more secure than other alternatives.

### Ansible has a number of important strengths:
- `Cross platform support` : Ansible provides agentless support for Linux, Windows, UNIX, and network devices, in physical, virtual, cloud, and container environments.
- `Human-readable automation` : Ansible Playbooks, written as YAML text files, are easy to read and help ensure that everyone understands what they will do.
- `Perfect description of applications` : Every change can be made by Ansible Playbooks, and every aspect of your application environment can be described and documented.
- `Easy to manage in version control` : Ansible Playbooks and projects are plain text. They can be treated like source code and placed in your existing version control system.
- `Support for dynamic inventories` : The list of machines that Ansible manages can be dynamically updated from external sources in order to capture the correct, current list of all managed servers all the time, regardless of infrastructure or location.
- `Orchestration that integrates easily with other systems` : HP SA, Puppet, Jenkins, Red Hat Satellite, and other systems that exist in your environment can be leveraged and integrated into your Ansible workflow.

Ansible: The Language of Devops

![image](https://github.com/Yezato/DATACOMM/assets/95903200/d5646037-75dc-48ae-a842-c5e0f90f0220)

Image : Ansible Across The Application Life Cycle (Images ©Redhat.)

Communication is the key to DevOps. Ansible is the first automation language that can be read and written across IT. It is also the only automation engine that can automate the application life cycle and continuous delivery pipeline from start to finish.

# Ansible Concepts and Architecture
![image](https://github.com/Yezato/DATACOMM/assets/95903200/a310a631-f20a-4c68-aef3-1503c43a3c67)

Image : Ansible Architecture (Images ©Redhat.)
There are two types of machines in the Ansible architecture: control nodes and managed hosts. Ansible is installed and run from a control node, and this machine also has copies of your Ansible project files. A control node could be an administrator's laptop, a system shared by a number of administrators, or a server running Red Hat Ansible Tower. The following is architecture-related information:

- `Inventory` : Inventory is lists of nodes or hosts having their IP addresses, databases, servers, etc. which are need to be managed.
- `API's` : The Ansible API's works as the transport for the public or private cloud services.
- `Modules` : Ansible connected the nodes and spread out the Ansible modules programs. Ansible executes the modules and removed after finished. These modules can reside on any machine; no database or servers are required here. You can work with the chose text editor or a terminal or version control system to keep track of the changes in the content.
- `Plugins` : Plugins is a piece of code that expends the core functionality of Ansible. There are many useful plugins, and you also can write your own.
- `Playbooks` : Playbooks consist of your written code, and they are written in YAML format, which describes the tasks and executes through the Ansible. Also, you can launch the tasks synchronously and asynchronously with playbooks.
- `Hosts` : In the Ansible architecture, hosts are the node systems, which are automated by Ansible, and any machine such as RedHat, Linux, Windows, etc.
- `Networking` : Ansible is used to automate different networks, and it uses the simple, secure, and powerful agentless automation framework for IT operations and development. It uses a type of data model which separated from the Ansible automation engine that spans the different hardware quite easily.
- `Cloud` : A cloud is a network of remote servers on which you can store, manage, and process the data. These servers are hosted on the internet and storing the data remotely rather than the local server. It just launches the resources and instances on the cloud, connect them to the servers, and you have good knowledge of operating your tasks remotely.
- `CMDB` : CMDB is a type of repository which acts as a data warehouse for the IT installations.

# The Ansible Way

#### Complexity Kills Productivity
Simpler is better. Ansible is designed so that its tools are simple to use and automation is simple to write and read. You should take advantage of this to strive for simplification in how you create your automation.


#### Optimize For Readability
The Ansible automation language is built around simple, declarative, text-based files that are easy for humans to read. Written properly, Ansible Playbooks can clearly document your workflow automation.


#### Think Declaratively

![image](https://github.com/Yezato/DATACOMM/assets/95903200/a47732e7-b5f3-46e9-acb2-309d4abd2ad3)

Image : Ansible Provides Complete Automation (Images ©Redhat.)

Ansible is a desired-state engine. It approaches the problem of how to automate IT deployments by expressing them in terms of the state that you want your systems to be in. Ansible's goal is to put your systems into the desired state, only making changes that are necessary. Trying to treat Ansible like a scripting language is not the right approach.

# Use Cases
Unlike some other tools, Ansible combines and unites orchestration with configuration management, provisioning, and application deployment in one easy-to-use platform. Some use cases for Ansible include:


#### Configuration Management
Centralizing configuration file management and deployment is a common use case for Ansible, and it is how many power users are first introduced to the Ansible automation platform.


#### Application Deployment
When you define your application with Ansible, and manage the deployment with Red Hat Ansible Tower, teams can effectively manage the entire application life cycle from development to production.


#### Provisioning
Applications have to be deployed or installed on systems. Ansible and Red Hat Ansible Tower can help streamline the process of provisioning systems, whether you are PXE booting and kickstarting bare-metal servers or virtual machines, or creating virtual machines or cloud instances from templates. Applications have to be deployed or installed on systems.


#### Continuous Delivery
Creating a CI/CD pipeline requires coordination and buy-in from numerous teams. You cannot do it without a simple automation platform that everyone in your organization can use. Ansible Playbooks keep your applications properly deployed (and managed) throughout their entire life cycle.


#### Security and Compliance
When your security policy is defined in Ansible Playbooks, scanning and remediation of site- wide security policies can be integrated into other automated processes. Instead of being an afterthought, it is an integral part of everything that is deployed.


#### Orchestration
Configurations alone do not define your environment. You need to define how multiple configurations interact, and ensure the disparate pieces can be managed as a whole.

