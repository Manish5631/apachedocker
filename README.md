# apachedocker
Automated Container deployment and Administration
Introduction 

In this assessment, our objective is to showcase how we can leverage Ansible, a powerful automation tool, to orchestrate the deployment of a Docker container running an Apache service. The Apache service will host a static web page, which will be accessible over the network. We will ensure that the Docker container is configured to allow network communication, and we'll verify its accessibility from the host machine.
Network Diagram

 
About the tools
•	Ansible: It is a free IT tool that automates various operations like setting up systems, managing configurations, deploying applications, coordinating actions, and more. With Ansible, you can: Install software, Automate routine tasks, Prepare hardware and network equipment, Enhance security and adherence to standards, Update systems, Organize intricate procedures.
•	Apache Server: Apache is like a digital gatekeeper for your website. It takes requests from people visiting your site, such as requests for specific pages or images. Then, Apache sends back the appropriate data, which displays the content you want visitors to see. In other words, Apache makes it possible for people to see your website by providing a connection between their devices and the server where your site is stored. Without software like Apache, your website wouldn't be accessible, and its speed and reliability would be greatly affected.
•	Anisble Playbook: Ansible Playbooks are automated sets of instructions that run on target hosts you specify. Playbooks contain plays, which are ordered lists of tasks that are applied to specific hosts. Tasks are executed sequentially within a play. Each playbook can have multiple plays and can also incorporate Ansible Roles. Roles are packages of tasks and automation assets that can be used in multiple plays or shared across different playbooks.

Execution

1.	Create two VMs with Ubuntu OS. One will be your host and other will be your target
 
2.	Install ansible on the host and create a playbook with inventory.ini file.
 - name: Install Apache, Docker, create Docker image, and start container
  hosts: 192.168.0.243
  remote_user: docker
  become: true
  become_method: sudo
  vars:
    ansible_ssh_private_key_file: /root/.ssh/id_rsa
    ansible_become_pass: docker
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present

    - name: Install Docker
      apt:
        name: docker.io
        state: present

    - name: Copy Apache configuration files to the VM
      copy:
        src: /etc/apache2/
        dest: /etc/apache2/
        owner: root
        group: root
        mode: '0644'
      notify: Restart Apache

    - name: Build Docker image
      command: docker build -t ansibleTest /home --quiet
      args:
        chdir: /home

    - name: Start Docker container
      command: docker run -d --name container -p 80:80 ansibleTest
      notify: Restart Apache

  handlers:
    - name: Restart Apache
      service:
        name: apache2
        state: restarted


3.	Generate a SSH key in the host and copy the public key on to the target directory.
 
4.	After successful configuration of the SSH keys and playbook directories, run the ansible playbook
 
5.	Finally, the server is up and running.

