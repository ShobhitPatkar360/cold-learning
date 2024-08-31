# Ansible Practical

# Important Questions

## How to Create an Ansible Cluster

### Step 1

Create 3 aws instances - Master, Slave1 and Slave2

![Untitled](Ansible%20Practical%2076c24ff7744247fc81d832793fda14b0/Untitled.png)

### Step 2

Get ssh to all the instances

```yaml
# use the following command to give the hostname to machines
sudo hostnamectl set-hostname <host-name>
# reconnect your instance
```

![Untitled](Ansible%20Practical%2076c24ff7744247fc81d832793fda14b0/Untitled%201.png)

### Step 3

Download the ansible in master node - link 

```yaml
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt update
sudo apt install ansible -y
```

### Step 4

Create an ssh-keygen

```yaml
ubuntu@master:~$ ssh-keygen
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/ubuntu/.ssh/id_ed25519): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/ubuntu/.ssh/id_ed25519
Your public key has been saved in /home/ubuntu/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:qIKHvs9vKftZAG0kc/+quo60+CXvtuTTt1zrSHgVgIg ubuntu@master
The key's randomart image is:
+--[ED25519 256]--+
|   + + ..        |
|  E B o  .       |
|   . o .  .      |
|    o  ..  .     |
|     .. S..      |
| o   .....       |
|o.+ +.ooo .      |
|+.+Ooo=+.o .     |
|.*=X&B .+oo      |
+----[SHA256]-----+
ubuntu@master:~$ 
```

Copy the public key to slave machine and try to get ssh to your slave machines

![Untitled](Ansible%20Practical%2076c24ff7744247fc81d832793fda14b0/Untitled%202.png)

![Untitled](Ansible%20Practical%2076c24ff7744247fc81d832793fda14b0/Untitled%203.png)

### Step 5

Add the following lines to the file - /etc/ansible/hosts

```yaml
# Give proper ip address of your EC2 instances
slave1 ansible_host=10.0.0.208
slave2 ansible_host=10.0.0.78
```

### Step 6

Test your connection

```yaml
ubuntu@master:/etc/ansible$ ansible all -m ping

# you will get output as following
slave1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
slave2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

## DESCRIBE THE RULES TO WRITE A PLAY IN ANSIBLE PLAYBOOK

To write a play in an Ansible playbook, you follow a structured format and adhere to certain rules. Here are the key rules to keep in mind when writing a play in an Ansible playbook:

1. **Define the Play**: Each play in an Ansible playbook is defined using the `--` separator followed by a YAML dictionary that specifies the attributes of the play. The dictionary includes key-value pairs that define the hosts to be targeted, the tasks to be executed, and any other relevant settings for the play.
    
    Example:
    
    ```yaml
    ---
    - name: Example Play
      hosts: servers
      become: yes
      tasks:
        - name: Task 1
          <task_definition>
    
    ```
    
2. **Specify the Play Name**: Provide a descriptive name for the play using the `name` attribute. This helps to identify the purpose of the play and makes the playbook more readable.
    
    Example:
    
    ```yaml
    - name: Install and configure web server
      hosts: webservers
      tasks:
        - name: Task 1
          <task_definition>
    
    ```
    
3. **Define the Hosts**: Specify the target hosts or groups of hosts for the play using the `hosts` attribute. Hosts can be defined explicitly by their hostnames or IP addresses, or they can be specified using inventory groups defined in the Ansible inventory file.
    
    Example:
    
    ```yaml
    - name: Configure database server
      hosts: database_servers
      tasks:
        - name: Task 1
          <task_definition>
    
    ```
    
4. **Define Connection and Privilege Escalation Options**: Optionally, specify connection-related settings such as the connection type (`connection`) and privilege escalation settings (`become`, `become_user`, `become_method`, etc.). These settings determine how Ansible connects to the target hosts and whether privilege escalation is required to execute tasks.
    
    Example:
    
    ```yaml
    - name: Update system packages
      hosts: all
      become: yes
      become_method: sudo
      tasks:
        - name: Task 1
          <task_definition>
    
    ```
    
5. **Define Tasks**: Inside the play, define the tasks to be executed using the `tasks` attribute. Tasks consist of one or more Ansible modules along with their associated parameters. Each task should have a unique name to describe its purpose.
    
    Example:
    
    ```yaml
    - name: Install Apache web server
      hosts: webservers
      tasks:
        - name: Install Apache
          apt:
            name: apache2
            state: present
    
    ```
    
6. **Include Additional Attributes**: Optionally, include additional attributes such as `vars`, `vars_files`, `roles`, `tags`, `environment`, etc., to customize the behavior of the play or provide additional context for the tasks.
    
    Example:
    
    ```yaml
    - name: Deploy application
      hosts: webservers
      vars:
        app_version: "1.0"
      tasks:
        - name: Deploy application files
          copy:
            src: "{{ app_version }}/files"
            dest: "/var/www/html"
    
    ```
    
7. **Use Hyphens for List Items**: In YAML syntax, lists are represented by hyphens (``). Each item in a list should be preceded by a hyphen followed by a space. In Ansible playbooks, plays and tasks are defined as lists of dictionaries, with each dictionary representing a play or task. Therefore, each play or task in a playbook should be preceded by a hyphen.
    
    Example:
    
    ```yaml
    ---
    - name: Install and configure web server
      hosts: webservers
      tasks:
        - name: Task 1
          <task_definition>
    
    ```
    
8. **Maintain Proper Indentation**: YAML syntax relies on indentation to define the structure of data. In Ansible playbooks, indentation is crucial for delineating nested elements such as plays, tasks, and task attributes. Each level of indentation should consist of two spaces. Nested elements should be indented further than their parent elements.
    
    Example:
    
    ```yaml
    - name: Configure database server
      hosts: database_servers
      tasks:
        - name: Task 1
          <task_definition>
    
    ```
    

## Describe an ansible playbook to run a script file

Just understand the pattern to create a playbook and execute the script in the nodes

```yaml
---
- name: Installing tool in master node
  hosts: master
  become: true
  tasks: 
  - name: Installing docker in master node
    script: docker_install.sh
    
    
    
 # now your script looks like following
 # file - docker_install.sh
 
# Step 1
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# Step 2
# Installng  Docker Package
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# for testing docker
sudo docker run hello-world
```

## describe an ansible playbook for copying the file

```yaml
---
- name: File Module
  hosts: all

  tasks:
    - name: Ensure the file does not exist
      file:
        path: /tmp/newfile.txt
        state: absent

    - name: Ensure the directory does not exist
      file:
        path: /tmp/myfolder
        state: absent

```

This playbook can be executed with the `ansible-playbook` command, specifying the playbook file:

```
ansible-playbook playbook.yml

```

The playbook will then be applied to all hosts specified in the inventory file, ensuring that the specified file (`/tmp/newfile.txt`) and directory (`/tmp/myfolder`) do not exist. If they exist, Ansible will remove them.

## Describe a yaml file to Disabling the Cron Job

```yaml
# Playbook to disable a cron job
- name: Playbook to run a script
  hosts: demo
  become: true  # Allows tasks to run with escalated privileges

  tasks:
    - name: Task to disable cron job
      cron:  # Ansible module for managing cron jobs
        name: cronjob to run a script  # Name of the cron job
        minute: 50  # Specifies the minute when the cron job should run (50th minute)
        hour: 06    # Specifies the hour when the cron job should run (6 AM)
        day: "*"    # Specifies the day of the month, "*" means every day
        month: "*"  # Specifies the month, "*" means every month
        weekday: "*"  # Specifies the day of the week, "*" means every day of the week
        user: ubuntu  # Specifies the user under which the cron job should run
        job: /tmp/scripts/test.sh  # Specifies the script or command to be executed by the cron job
        disabled: yes  # Disables the cron job

```

## Describe a  Playbook for Creating a New User

```yaml
# Playbook for managing user creation
- name: Playbook for user management
  hosts: all  # Specifies the target hosts where the playbook will be applied
  become: true  # Allows tasks to run with escalated privileges

  tasks:
    - name: Task for creating a new user
      user:  # Ansible module for managing user accounts
        name: nick  # Specifies the username of the new user to be created
        comment: new user adding for QA Team  # Specifies a descriptive comment for the user
        shell: /bin/bash  # Specifies the default shell for the user account

```

## Describe a Playbook for Adding User to Multiple Groups

```yaml
# Playbook for managing user groups
- name: Playbook for user management
  hosts: all  # Specifies the target hosts where the playbook will be applied
  become: true  # Allows tasks to run with escalated privileges

  tasks:
    - name: Task for creating a new user
      user:  # Ansible module for managing user accounts
        name: nick  # Specifies the username of the new user to be created
        comment: new user adding for QA Team  # Specifies a descriptive comment for the user
        shell: /bin/bash  # Specifies the default shell for the user account
        groups: QA,testing  # Specifies the groups the user should be added to

```

## Create a Playbook for Deleting User and Data

```yaml
# Playbook for managing user deletion
- name: Playbook for user management
  hosts: all  # Specifies the target hosts where the playbook will be applied
  become: true  # Allows tasks to run with escalated privileges

  tasks:
    - name: Task for deleting a user and its data
      user:  # Ansible module for managing user accounts
        name: nick  # Specifies the username of the user to be deleted
        comment: new user adding for QA Team  # Specifies a descriptive comment for the user
        shell: /bin/bash  # Specifies the default shell for the user account
        groups: QA,testing  # Specifies the groups the user belongs to
        state: absent  # Ensures the user is removed from the system
        remove: yes  # Removes the user's home directory and mail spool if they exist

```

## Create a Playbook for Adding Password for User

```yaml
# Playbook for configuring user-related settings
- name: Playbook for user related configuration
  hosts: demo  # Specifies the target hosts where the playbook will be applied
  become: true  # Allows tasks to run with escalated privileges

  tasks:
    - name: Setting password for user
      user:  # Ansible module for managing user accounts
        name: rajni  # Specifies the username of the user
        password: '$6$MyRtA6WlnGPIgSSB$yG5e5BzQfcWQ4mxpKxllYVoX14mzceuu0X0Du7Kxvn14LrRlTdwxUcbRLKMG0rPbokSzUq9dypzws1fZuZw3W/'
        # Specifies the hashed password for the user

```

## Create a Playbook for Finding, Deleting, and Restarting Nginx Process

```yaml
# Playbook for finding the Nginx process, stopping it, and restarting the service
- name: Playbook for finding the process, deleting it and restarting it - nginx
  hosts: demo  # Specifies the target hosts where the playbook will be applied
  become: true  # Allows tasks to run with escalated privileges

  tasks:
    - name: Task to kill the process
      shell: "pgrep nginx | xargs kill"  # Finds the Nginx process and stops it
      ignore_errors: yes  # Ignores errors if the process is not running

    - name: Restarting the Nginx service
      service:  # Ansible module for managing system services
        name: nginx  # Specifies the name of the service to be managed (Nginx)
        state: started  # Ensures the service is started

```

## Create a Playbook for Downloading a File from a Download Link

```yaml
# Playbook for downloading a file from a download link
- name: Playbook for file
  hosts: all  # Specifies the target hosts where the playbook will be applied
  become: yes  # Allows tasks to run with escalated privileges

  tasks:
    - name: Tasks to download a file
      get_url:  # Ansible module for downloading files from URLs
        url: https://github.com/pbatard/rufus/releases/download/v4.4/rufus-4.4p.exe  # URL of the file to download
        dest: /home/ubuntu  # Destination path where the file will be saved
        owner: ubuntu  # Specifies the owner of the downloaded file
        group: ubuntu  # Specifies the group of the downloaded file
        mode: 0775  # Specifies the permissions (mode) of the downloaded file

```

## describe the use of ask-become-pass while executing a playbook as a sudo user , with an example

Using `ask-become-pass` while executing a playbook as a sudo user is a security measure that prompts the user running the playbook for their password, allowing Ansible to escalate privileges when necessary. This option is useful when you want to run tasks that require root or elevated permissions on the target hosts.

Here's an example scenario:

Let's say you have an Ansible playbook named `install_packages.yml` that installs some packages on your target hosts. Some of these package installations require root privileges.

```yaml
# install_packages.yml

---
- name: Install necessary packages
  hosts: servers
  become: yes  # Enables privilege escalation
  tasks:
    - name: Install package1
      yum:
        name: package1
        state: present

    - name: Install package2
      apt:
        name: package2
        state: present

```

When you run this playbook, you can use `ask-become-pass` to prompt Ansible to ask for your sudo password:

```bash
ansible-playbook install_packages.yml --ask-become-pass

```

After executing the command, Ansible will prompt you for your sudo password. Once you provide it, Ansible will use this password to escalate privileges and execute the tasks that require root access (`become: yes`). This ensures that only authorized users can perform privileged operations on the target hosts.

Using `ask-become-pass` adds an extra layer of security by preventing unauthorized users from gaining root access to the target hosts without providing the necessary credentials. It's particularly useful in environments where strict security policies are enforced.

## describe with example some popular adhoc commands

Certainly! Ad-hoc commands are powerful one-liners used in Ansible for quick tasks or checks without the need for writing full playbooks. Here are some popular ad-hoc commands with examples:

1. **Ping all hosts**:
This command checks if Ansible can communicate with all hosts.
    
    ```bash
    ansible all -m ping
    
    ```
    
2. **Check available memory on all hosts**:
This command fetches the memory information from all hosts.
    
    ```bash
    ansible all -m shell -a "free -m"
    
    ```
    
3. **Install a package**:
Installs a package on all hosts.
    
    ```bash
    ansible all -m yum -a "name=<package_name> state=present"  # For CentOS/RHEL
    ansible all -m apt -a "name=<package_name> state=present"  # For Debian/Ubuntu
    
    ```
    
4. **Copy a file to all hosts**:
Copies a file from the local machine to all hosts.
    
    ```bash
    ansible all -m copy -a "src=/path/to/local/file dest=/path/on/remote"
    
    ```
    
5. **Check disk usage**:
Checks disk usage on all hosts.
    
    ```bash
    ansible all -m shell -a "df -h"
    
    ```
    
6. **Restart a service**:
Restarts a service on all hosts.
    
    ```bash
    ansible all -m service -a "name=<service_name> state=restarted"
    
    ```
    
7. **Check network connectivity**:
Checks network connectivity by pinging a specific host.
    
    ```bash
    ansible all -m shell -a "ping -c 4 <hostname_or_ip>"
    
    ```
    
8. **Run a command**:
Runs a command on all hosts.
    
    ```bash
    ansible all -m shell -a "<command>"
    
    ```
    
9. **Fetch a file from remote hosts**:
Fetches a file from remote hosts to the local machine.
    
    ```bash
    ansible all -m fetch -a "src=/path/on/remote dest=/path/on/local flat=yes"
    
    ```
    
10. **Check system information**:
Fetches system information from all hosts.
    
    ```bash
    ansible all -m setup
    
    ```
    

## how to run multiple shell commands as adhoc command

To run multiple shell commands as an ad-hoc command in Ansible, you can use the `shell` module with the `-a` option followed by the commands enclosed within quotes and separated by semicolons (;) or double ampersands (&&). Here's how you can do it:

```bash
ansible all -m shell -a "command1; command2; command3"

```

or

```bash
ansible all -m shell -a "command1 && command2 && command3"

```

For example, let's say you want to run multiple shell commands to check the disk usage, available memory, and system uptime on all hosts:

```bash
ansible all -m shell -a "df -h; free -m; uptime"

```

This command will execute `df -h`, `free -m`, and `uptime` sequentially on all hosts specified in the inventory file, providing you with disk usage, memory information, and system uptime for each host.

## arrange the following in proper precedence
-a  --ask-become-pass -m -b

The proper order of precedence for the given options is as follows:

1. `a` (or `-args`) - Specifies the module arguments or command to execute.
2. `m` (or `-module-name`) - Specifies the module to use for the command execution.
3. `b` (or `-become`) - Specifies that the command should be executed with escalated privileges (i.e., as sudo).
4. `-ask-become-pass` - Specifies that Ansible should prompt for the sudo password.

So, the correct sequence to use these options would be:

```bash
ansible <host_pattern> -m <module_name> -a <module_arguments> -b --ask-become-pass

```

For example:

```bash
ansible all -m command -a "ls" -b --ask-become-pass

```

This command runs the `ls` command on all hosts, with escalated privileges, prompting for the sudo password.

## Create a Playbook for installing and starting Nginx with tags

```yaml
# Playbook for installing and starting Nginx with tags
- name: Installing and starting Nginx
  hosts: demo  # Specifies the target hosts where the playbook will be applied
  become: yes  # Allows tasks to run with escalated privileges

  tasks:
    - name: Installing Nginx
      apt:  # Ansible module for package management on Debian-based systems
        name: nginx  # Specifies the name of the package to install (Nginx)
        state: present  # Ensures that the package is present
      tags: i-nginx  # Adds a tag 'i-nginx' to this task for grouping and selective execution

    - name: Starting the service
      service:  # Ansible module for managing system services
        name: nginx  # Specifies the name of the service to manage (Nginx)
        state: restarted  # Ensures that the service is restarted
        enabled: true  # Ensures that the service is enabled to start on boot
      tags: s-nginx  # Adds a tag 's-nginx' to this task for grouping and selective execution

```

## Describe the use of following commands with example with reference of playbook described in above response

ansible-playbook app_start.yml  --list-tags
ansible-playbook app_start.yml  -t i-nginx
ansible-playbook app_start.yml  --skip-tags s-nginx

Sure! Let's use the playbook described in the previous response as an example.

1. `ansible-playbook app_start.yml --list-tags`:
    - This command lists all the tags defined in the playbook without actually executing any tasks.
    - It helps in identifying the available tags that can be used for selective execution.
    - Here's how you would use it:
        
        ```bash
        ansible-playbook app_start.yml --list-tags
        
        ```
        
    - Output:
        
        ```
        available tags:
          [i-nginx, s-nginx]
        
        ```
        
2. `ansible-playbook app_start.yml -t i-nginx`:
    - This command executes only the tasks with the specified tag (`i-nginx` in this case) and skips the tasks without that tag.
    - It allows you to selectively execute tasks based on the defined tags.
    - Here's how you would use it:
        
        ```bash
        ansible-playbook app_start.yml -t i-nginx
        
        ```
        
    - Output:
        
        ```
        (...)
        TASK [Installing Nginx] ***************************************
        (... output of the task ...)
        
        ```
        
3. `ansible-playbook app_start.yml --skip-tags s-nginx`:
    - This command executes all tasks except the ones with the specified tag (`s-nginx` in this case).
    - It allows you to skip certain tasks based on the defined tags.
    - Here's how you would use it:
        
        ```bash
        ansible-playbook app_start.yml --skip-tags s-nginx
        
        ```
        
    - Output:
        
        ```
        (...)
        TASK [Starting the service] ***************************************
        (... output of the task ...)
        
        ```
        

## Create a Playbook for Implementing Variables

```yaml
# Playbook for implementing the concept of variables
- name: Playbook for implementing the concept of variables
  hosts: demo  # Specifies the target hosts where the playbook will be applied
  become: true  # Allows tasks to run with escalated privileges
  vars:  # Defines variables that can be used in tasks
    app: apache2  # Specifies the name of the application (e.g., Apache2)

  tasks:
    - name: Installing "{{ app }}"  # Task to install the application defined by the variable
      apt:  # Ansible module for package management on Debian-based systems
        name: "{{ app }}"  # Specifies the name of the package to install (uses the variable)
        state: present  # Ensures that the package is present

    - name: Starting the service of "{{ app }}"  # Task to start the service of the application
      service:  # Ansible module for managing system services
        name: "{{ app }}"  # Specifies the name of the service to manage (uses the variable)
        state: restarted  # Ensures that the service is restarted
        enabled: true  # Ensures that the service is enabled to start on boot

```

## how to provide host name as variable in inventory file in ansible

```yaml
<variable_name_for_host> ansible_host=<ip_address_of_remote_host>

```

In this format:

- `<variable_name_for_host>` is the variable name you choose to represent the host.
- `ansible_host` is a special variable recognized by Ansible that specifies the IP address or hostname of the remote host.
- `<ip_address_of_remote_host>` is the IP address or hostname of the remote host.

## how to list all the connected nodes from ansible terminal

```yaml
ansible-inventory --list

```

## how to change owner for a file from root to other user in linux terminal

```yaml
sudo chown <new_owner>:<new_group> <file_path>

```

## Create a Playbook for Creating a File

```yaml
# Playbook for creating a file with custom content
- name: playbook for adding text in file  # Descriptive name for the playbook
  hosts: all  # Specifies the target hosts where the playbook will be applied
  become: true  # Allows tasks to run with escalated privileges

  tasks:
    - name: adding text in file  # Descriptive name for the task
      copy:  # Ansible module for copying files and setting attributes
        dest: /tmp/1.txt  # Specifies the destination path for the file
        content: |  # Specifies the content of the file using a multi-line string
          This text has been added by custom script  # Custom text to be added to the file

```

## how to create a new role using ansible galaxy

```yaml
ansible-galaxy init <role_name>

# Suppose you typed following command
ansible-galaxy init my_role

# following directory will be formed
my_role/
├── defaults/
│   └── main.yml
├── files/
├── handlers/
│   └── main.yml
├── meta/
│   └── main.yml
├── tasks/
│   └── main.yml
├── templates/
├── tests/
│   ├── inventory
│   └── test.yml
└── vars/
    └── main.yml

```

## how to write a task of installing apache on ubuntu using ansible roles

To write a task for installing Apache on Ubuntu using Ansible roles, you would typically create a new role, then define the task to install Apache within that role. Here's a step-by-step guide:

1. **Create a New Ansible Role:**
First, use `ansible-galaxy` to create a new role. Open your terminal and run:
    
    ```bash
    ansible-galaxy init apache_install
    
    ```
    
    This command will create a new directory structure for your role named `apache_install`.
    
2. **Write the Task to Install Apache:**
Navigate to the `tasks` directory within your role (`apache_install/tasks`) and open the `main.yml` file.
    
    Add the following task to the file:
    
    ```yaml
    # apache_install/tasks/main.yml
    
    - name: Install Apache
      apt:
        name: apache2
        state: present
    
    ```
    
    This task uses the `apt` module to install Apache (`apache2`) on Ubuntu. The `state: present` parameter ensures that Apache will be installed if it is not already installed.
    
3. **Include the Role in Your Playbook:**
Now, you can include the `apache_install` role in your playbook. Create a playbook (e.g., `site.yml`) and add the following content:
    
    ```yaml
    # site.yml
    
    - name: Playbook to install Apache on Ubuntu
      hosts: your_ubuntu_hosts
      become: yes
      roles:
        - apache_install
    
    ```
    
    Replace `your_ubuntu_hosts` with the appropriate host group or host name where you want to install Apache.
    
4. **Run the Playbook:**
Finally, run your playbook to install Apache on the specified hosts:
    
    ```bash
    ansible-playbook site.yml
    
    ```
    
    Ansible will execute the playbook, which will in turn execute the `apache_install` role and install Apache on the specified Ubuntu hosts.
    

## create node specific task for ansibel. Installing nginx on slave1 and apache2 on slave2

```yaml
# install_services.yml

- name: Install Nginx on slave1 and Apache2 on slave2
  hosts: all
  become: true
  tasks:
    - name: Install Nginx on slave1
      apt:
        name: nginx
        state: present
      when: inventory_hostname == 'slave1'

    - name: Install Apache2 on slave2
      apt:
        name: apache2
        state: present
      when: inventory_hostname == 'slave2'

```

## how to copy a file from file folder in roles

To copy a file from a folder within an Ansible role, you can use the `copy` module within a task defined in the role's tasks directory. Here's how you can do it:

1. **Create the Role Directory Structure:**
First, make sure you have a role directory structure created. If you haven't created a role yet, you can use the following command to create a new role named `my_role`:
    
    ```bash
    ansible-galaxy init my_role
    
    ```
    
2. **Place the File in the Role's Files Directory:**
Put the file you want to copy into the role's `files` directory. If the `files` directory doesn't exist within your role directory, you can create it.
    
    For example, let's say you have a file named `example.txt` located at `/path/to/example.txt`. Move this file into the `files` directory of your role:
    
    ```
    my_role/
    ├── defaults
    ├── files
    │   └── example.txt
    ├── handlers
    ├── meta
    ├── README.md
    ├── tasks
    ├── templates
    ├── tests
    └── vars
    
    ```
    
3. **Write the Task to Copy the File:**
Navigate to the `tasks` directory within your role (`my_role/tasks`) and open the `main.yml` file. Add the following task to the file:
    
    ```yaml
    # my_role/tasks/main.yml
    
    - name: Copy file from role's files directory
      copy:
        src: example.txt
        dest: /path/to/destination/example.txt
    
    ```
    
    Replace `/path/to/destination/example.txt` with the path where you want to copy the file on the target host.
    
4. **Include the Role in Your Playbook:**
Now, include the `my_role` role in your playbook where you want to execute it. For example:
    
    ```yaml
    # playbook.yml
    
    - name: Playbook to copy file using Ansible role
      hosts: your_hosts
      become: true
      roles:
        - my_role
    
    ```
    
5. **Run the Playbook:**
Finally, run your playbook to execute the role and copy the file:
    
    ```bash
    ansible-playbook playbook.yml
    
    ```
    

## Write a yaml file to Providing Environment Variable to Cron Environment

```yaml
# Playbook to create an environment variable for the cron environment
- name: Playbook to create an env var for cron env
  hosts: demo  # Specifies the target hosts where the playbook will be applied

  tasks:
    - name: Task to create an environment variable
      cron:  # Ansible module for managing cron jobs
        name: PATH  # Name of the environment variable to be set
        env: yes    # Indicates that the environment variable should be set
        job: /tmp/scripts  # Specifies the command or script associated with the cron job
        user: ubuntu  # Specifies the user under which the cron job should run

```

## Write a yaml file to Providing Environment Variable to Cron Environment at a Specific Position

```yaml
# Playbook to create an environment variable for the cron environment
- name: Playbook to create an env var for cron env
  hosts: demo  # Specifies the target hosts where the playbook will be applied

  tasks:
    - name: Task to create an environment variable
      cron:  # Ansible module for managing cron jobs
        name: VAR  # Name of the environment variable to be set
        env: yes    # Indicates that the environment variable should be set
        job: /tmp/scripts/test.sh  # Specifies the command or script associated with the cron job
        user: ubuntu  # Specifies the user under which the cron job should run
        insertbefore: PATH  # Specifies the position in the cron environment where the variable should be inserted

```

## Write a yaml file to Deleting an Environment Variable from Cron Environment

```yaml
# Playbook to delete an environment variable from the cron environment
- name: Playbook to create an env var for cron env
  hosts: demo  # Specifies the target hosts where the playbook will be applied

  tasks:
    - name: Task to delete an environment variable
      cron:  # Ansible module for managing cron jobs
        name: VAR  # Name of the environment variable to be deleted
        env: yes    # Indicates that the environment variable should be manipulated
        job: /tmp/scripts/test.sh  # Specifies the command or script associated with the cron job
        user: ubuntu  # Specifies the user under which the cron job should run
        insertbefore: PATH  # Specifies the position in the cron environment where the variable was inserted
        state: absent  # Removes the specified environment variable from the cron environment

```

## HOW TO ADD THE SYSTEM VARABLES IN UBUNTU

In Ubuntu, you can add system-wide environment variables by modifying the `/etc/environment` file. Here's how you can do it:

1. Open a terminal on your Ubuntu system.
2. Use a text editor such as `nano` or `vim` to open the `/etc/environment` file. You'll need sudo privileges to edit this file.
    
    ```
    sudo nano /etc/environment
    
    ```
    
3. Add your environment variable assignments in the following format:
    
    ```
    VARIABLE_NAME="value"
    
    ```
    
    Replace `VARIABLE_NAME` with the name of your environment variable and `"value"` with the desired value. Ensure there are no spaces around the `=` sign.
    
    For example:
    
    ```
    PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"
    
    ```
    
4. Save the changes and exit the text editor.
5. After modifying the `/etc/environment` file, you need to reboot your system for the changes to take effect:
    
    ```
    sudo reboot
    
    ```
    

After rebooting, the system-wide environment variables you added will be available to all users and processes on your Ubuntu system. You can verify that the variables have been set correctly by opening a terminal and running:

```
printenv VARIABLE_NAME

```

Replace `VARIABLE_NAME` with the name of the environment variable you added. If everything was configured correctly, the command should print out the value you assigned to the variable.

## what is the use of following command
ansible-playbook main.yml --become-password-file=sudo_password.txt

The `ansible-playbook` command you provided is used to run an Ansible playbook (`main.yml`) and specifies the location of a file (`sudo_password.txt`) containing the password for privilege escalation. Here's a breakdown of the command:

- **`ansible-playbook`:** This is the Ansible command to execute a playbook.
- **`main.yml`:** This is the name of the playbook file (assuming it's named "main.yml"). Playbooks in Ansible are YAML files that define a set of tasks to be executed on remote hosts.
- **`-become-password-file=sudo_password.txt`:** This option is used to specify the location of a file containing the password for privilege escalation. When Ansible needs to execute tasks with elevated privileges (using `sudo` or `become`), it may prompt for a password. Providing the password in a file allows for automation without manual intervention.
    - `-become-password-file`: Specifies the file containing the privilege escalation password.
    - `sudo_password.txt`: The actual file containing the password.

**Example Usage:**

Suppose you have a playbook (`main.yml`) that includes tasks requiring elevated privileges. Running the playbook with the `--become-password-file` option allows Ansible to use the specified password file for privilege escalation:

```bash
ansible-playbook main.yml --become-password-file=sudo_password.txt

```

In this example, `sudo_password.txt` should contain the password required for becoming a privileged user on the remote hosts. This is useful in automation scenarios where you don't want to enter the password manually during playbook execution. However, note that storing passwords in plain text files should be done with caution, and it's advisable to use more secure methods for managing secrets, such as Ansible Vault or other credential management solutions.

## create a ansible playbook for installing apache2 ubuntu ansible nodes

Certainly! Below is an example Ansible playbook that installs Apache2 on Ubuntu nodes. Save this content in a file, let's say `install_apache.yml`, and run it using the `ansible-playbook` command.

```yaml
---
- name: Install Apache2 on Ubuntu
  hosts: ubuntu_servers
  become: true  # Run tasks with elevated privileges (sudo)

  tasks:
    - name: Update package cache
      apt:
        update_cache: yes

    - name: Install Apache2
      apt:
        name: apache2
        state: present

```

In this playbook:

- `name`: Descriptive name for the playbook.
- `hosts`: Specifies the target hosts or groups (`ubuntu_servers` in this case).
- `become`: Enables privilege escalation, allowing tasks to run with elevated privileges (`sudo`).

Tasks:

1. `Update package cache`: Ensures that the package cache is updated using the `apt` module.
2. `Install Apache2`: Uses the `apt` module to install Apache2 on Ubuntu.

To run this playbook, use the following command:

```bash
ansible-playbook install_apache.yml

```

Make sure to replace `ubuntu_servers` with the appropriate group or hostname of your Ubuntu nodes in your inventory file. Adjust the playbook as needed for your specific requirements.

## create a ansible playbook for uninstalling apache2 ubuntu ansible nodes

Certainly! Below is an example Ansible playbook that uninstalls Apache2 on Ubuntu nodes. Save this content in a file, let's say `uninstall_apache.yml`, and run it using the `ansible-playbook` command.

```yaml
---
- name: Uninstall Apache2 on Ubuntu
  hosts: ubuntu_servers
  become: true  # Run tasks with elevated privileges (sudo)

  tasks:
    - name: Stop Apache2 service
      service:
        name: apache2
        state: stopped
      ignore_errors: true  # Ignore errors if the service is not running

    - name: Remove Apache2
      apt:
        name: apache2
        state: absent

    - name: Autoremove dependencies
      apt:
        autoremove: yes

```

In this playbook:

- `name`: Descriptive name for the playbook.
- `hosts`: Specifies the target hosts or groups (`ubuntu_servers` in this case).
- `become`: Enables privilege escalation, allowing tasks to run with elevated privileges (`sudo`).

Tasks:

1. `Stop Apache2 service`: Stops the Apache2 service. `ignore_errors: true` is used to prevent playbook failure if the service is not running.
2. `Remove Apache2`: Uses the `apt` module to uninstall Apache2 on Ubuntu.
3. `Autoremove dependencies`: Runs `apt autoremove` to remove any unnecessary dependencies.

To run this playbook, use the following command:

```bash
ansible-playbook uninstall_apache.yml

```

Make sure to replace `ubuntu_servers` with the appropriate group or hostname of your Ubuntu nodes in your inventory file. Adjust the playbook as needed for your specific requirements.

# Some Other Questions

## DESCRIBE THE DETAILED STEPS TO CONFIGER THE CONTROL NODE FOR ANSIBLE

Configuring the control node for Ansible involves setting up Ansible on a designated server or workstation and configuring it to manage the infrastructure. Here are the detailed steps to configure the control node:

1. **Choose a Control Node**:
    - Select a server or workstation to serve as the Ansible control node.
    - The control node should have network connectivity to the managed nodes and meet the system requirements for running Ansible.
2. **Install Ansible**:
    - Update the package repository on the control node.
        
        ```
        sudo apt update   # For Debian/Ubuntu
        sudo yum update   # For CentOS/RHEL
        
        ```
        
    - Install Ansible using the package manager.
        
        ```
        sudo apt install ansible   # For Debian/Ubuntu
        sudo yum install ansible   # For CentOS/RHEL
        
        ```
        
    - Alternatively, you can install Ansible using Python's package manager, pip.
        
        ```
        sudo pip install ansible
        
        ```
        
3. **Verify Ansible Installation**:
    - Check that Ansible has been installed successfully by running the following command:
        
        ```
        ansible --version
        
        ```
        
4. **Configure SSH Keys**:
    - If SSH keys are not already set up, generate a new SSH key pair on the control node.
        
        ```
        ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
        
        ```
        
    - Press Enter to accept the default file location and passphrase (or set a passphrase if desired).
    - Copy the public SSH key to the managed nodes to enable passwordless SSH authentication.
        
        ```
        ssh-copy-id user@managed_node_ip
        
        ```
        
    - Repeat this step for each managed node, replacing `user` with the appropriate username and `managed_node_ip` with the IP address of each managed node.
5. **Create an Inventory File**:
    - Create an inventory file to define the managed nodes that Ansible will manage.
        
        ```
        sudo nano /etc/ansible/hosts
        
        ```
        
    - Add the IP addresses or hostnames of the managed nodes to the inventory file, separated by commas.
        
        ```
        [web_servers]
        192.168.1.10
        192.168.1.11
        
        [db_servers]
        192.168.1.20
        
        ```
        
    - Save and close the inventory file.
6. **Test Ansible Connectivity**:
    - Verify that Ansible can connect to the managed nodes using SSH.
        
        ```
        ansible all -m ping
        
        ```
        
    - This command will send a ping to all hosts defined in the inventory file and return a success message if the hosts respond.
7. **(Optional) Configure Ansible Settings**:
    - Customize Ansible settings by editing the `ansible.cfg` configuration file.
        
        ```
        sudo nano /etc/ansible/ansible.cfg
        
        ```
        
    - Modify settings such as the default inventory location, SSH user, connection settings, etc.
    - Save and close the `ansible.cfg` file.
8. **Test Ansible Playbooks**:
    - Create a simple Ansible playbook or use existing playbooks to test the Ansible configuration.
    - Run the playbook on the control node and verify that it executes tasks on the managed nodes as expected.
9. **Documentation and Maintenance**:
    - Document the Ansible configuration, including inventory structure, SSH settings, and playbook usage.
    - Regularly update Ansible and its dependencies to ensure security and compatibility.
    - Monitor the performance and health of the Ansible control node to ensure smooth operation.

By following these steps, you can configure the control node for Ansible and begin automating tasks across your infrastructure efficiently.

## HOW TO PROVIDE SSH KEYS FROM INVENTORY FILE FOR ANSIBLE HOSTS

To provide SSH keys from an inventory file for Ansible hosts, you typically need to ensure that the SSH keys are properly configured on both the Ansible control node and the managed hosts. Here are the steps to achieve this:

1. **Generate SSH Keys on the Control Node**:
    - If SSH keys are not already generated on the control node, you can generate them using the `ssh-keygen` command:
        
        ```
        ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
        
        ```
        
    - Follow the prompts to generate the SSH key pair. You can choose the default location or specify a custom location.
2. **Copy the Public SSH Key to Managed Hosts**:
    - Once the SSH keys are generated, you need to copy the public key to the `.ssh/authorized_keys` file on each managed host.
        
        ```
        ssh-copy-id user@managed_host
        
        ```
        
    - Replace `user` with the username used to log in to the managed host, and `managed_host` with the IP address or hostname of the managed host.
    - You may need to provide the password for the specified user on the managed host to complete the process.
3. **Verify SSH Key Authentication**:
    - After copying the SSH key to the managed hosts, verify that SSH key authentication is working properly by SSHing into the managed host without requiring a password:
        
        ```
        ssh user@managed_host
        
        ```
        
    - You should be able to log in to the managed host without being prompted for a password.
4. **Configure Ansible Inventory**:
    - In your Ansible inventory file (e.g., `/etc/ansible/hosts`), define the managed hosts with their respective IP addresses or hostnames.
    - Make sure the inventory file is properly structured, with host groups if necessary.
5. **Specify SSH Keys in Inventory File**:
    - In the inventory file, you can specify the SSH key to be used for each host using the `ansible_ssh_private_key_file` parameter.
        
        ```
        [web_servers]
        192.168.1.10 ansible_ssh_private_key_file=~/.ssh/id_rsa_web_server
        
        ```
        
6. **Run Ansible Playbooks**:
    - Once the SSH keys are specified in the inventory file, you can run Ansible playbooks as usual.
    - Ansible will use the specified SSH key to authenticate with the managed hosts during playbook execution.

By following these steps, you can provide SSH keys from an inventory file for Ansible hosts, enabling secure authentication and communication between the Ansible control node and managed hosts.

## HOW TO PING ANSIBLE MANAGED HOST FROM  ANSIBLE CONTROL NODE

To ping an Ansible managed host from the Ansible control node, you can use the `ping` module. Here's how to do it:

1. **SSH Connectivity**:
Ensure that your Ansible control node has SSH connectivity to the managed host. You can test this by SSHing manually to the managed host from the control node:
    
    ```
    ssh user@managed_host
    
    ```
    
    Replace `user` with the username used to log in to the managed host, and `managed_host` with the IP address or hostname of the managed host.
    
2. **Ping Command**:
Use the `ansible` command-line tool to ping the managed host. The `ping` module sends ICMP echo requests to the host and waits for a response. It's a basic connectivity check.
    
    ```
    ansible all -m ping
    
    ```
    
    This command pings all hosts defined in the Ansible inventory file. If you want to ping a specific host group or individual host, you can specify it instead of `all`.
    
    ```
    ansible web_servers -m ping
    
    ```
    
3. **Check the Output**:
After running the `ping` command, Ansible will attempt to ping each managed host and display the results. If a managed host responds, you will see a success message indicating that the host is reachable.
    
    Example output for a successful ping:
    
    ```
    192.168.1.10 | SUCCESS => {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python"
        },
        "changed": false,
        "ping": "pong"
    }
    
    ```
    
    If a host is unreachable, you will see a failure message indicating that the host did not respond to the ping request.
    
    Example output for an unreachable host:
    
    ```
    192.168.1.20 | UNREACHABLE! => {
        "changed": false,
        "msg": "Failed to connect to the host via ssh: ssh: connect to host 192.168.1.20 port 22: Connection timed out",
        "unreachable": true
    }
    
    ```
    

By following these steps, you can easily ping Ansible managed hosts from the Ansible control node to verify connectivity and troubleshoot any network-related issues.

## FOR ABOVE RESPONSE, ADD THE CODE FOR PROVIDING ADDITIONAL PATH FOR INVENTORY FILE AND  SSH KEY

To provide additional paths for the inventory file and SSH key when pinging Ansible managed hosts from the control node, you can use the `-i` option to specify the path to the inventory file and the `--private-key` option to specify the path to the SSH key. Here's how you can modify the command:

```bash
ansible all -i /path/to/inventory_file -m ping --private-key=/path/to/ssh_key

```

Replace `/path/to/inventory_file` with the actual path to your inventory file and `/path/to/ssh_key` with the actual path to your SSH private key file.

Here's an example of the modified command:

```bash
ansible all -i /etc/ansible/hosts -m ping --private-key=/home/user/.ssh/id_rsa

```

This command will ping all hosts defined in the `/etc/ansible/hosts` inventory file using the SSH key located at `/home/user/.ssh/id_rsa`. Adjust the paths accordingly based on your environment and configuration.

## MY INVENTORY FILE IS NOT RECOGNIZED IN ANSIBLE, HOW TO SET UP SO THAT ANSIBLE CAN AUTOMATICALLY USE MY  INVENTORY FILE

If your inventory file is not being recognized by Ansible automatically, you may need to specify the location of the inventory file explicitly. Here are a few steps to ensure Ansible can automatically use your inventory file:

1. **Default Inventory File**:
By default, Ansible looks for the inventory file at `/etc/ansible/hosts`. Ensure that your inventory file is located at this path if you want Ansible to automatically use it.
2. **Check ansible.cfg**:
Ansible's behavior can be customized using a configuration file called `ansible.cfg`. Check if you have an `ansible.cfg` file in your Ansible configuration directory (usually `/etc/ansible/` or `~/.ansible/` for user-specific configurations). Open this file and look for the following section:
    
    ```
    [defaults]
    inventory = /etc/ansible/hosts
    
    ```
    
    Ensure that the `inventory` parameter points to the correct path where your inventory file is located. If it's different, update it accordingly.
    
3. **Specify Inventory File**:
Alternatively, you can specify the location of the inventory file using the `i` option when running Ansible commands. For example:
    
    ```
    ansible-playbook -i /path/to/inventory_file playbook.yml
    
    ```
    
4. **Environment Variable**:
You can also set an environment variable called `ANSIBLE_INVENTORY` to specify the location of the inventory file. For example:
    
    ```
    export ANSIBLE_INVENTORY=/path/to/inventory_file
    
    ```
    
5. **Test the Setup**:
After making any changes, test whether Ansible can recognize your inventory file by running a simple command, such as:
    
    ```
    ansible all -m ping
    
    ```
    

By following these steps, you can ensure that Ansible can automatically use your inventory file without explicitly specifying its location every time you run Ansible commands.

## THERE IS NO CONFIGURATION  FILE PRESENT IN MY ANSIBLE  CONTROLLER NODE, HOW TO INITIALISE THE ANSIBLE CONFIGURATION FILE

If there is no configuration file present in your Ansible controller node, you can create one to customize Ansible's behavior according to your requirements. Here's how you can initialize an Ansible configuration file:

1. **Create the Configuration File**:
Use a text editor (such as `nano` or `vim`) to create a new file named `ansible.cfg` in the appropriate directory. Typically, this directory would be `/etc/ansible/` for system-wide configurations or `~/.ansible/` for user-specific configurations.
    
    ```
    sudo nano /etc/ansible/ansible.cfg
    
    ```
    
    or
    
    ```
    nano ~/.ansible/ansible.cfg
    
    ```
    
2. **Add Configuration Settings**:
Inside the `ansible.cfg` file, you can specify various configuration settings under different sections, such as `[defaults]`, `[privilege_escalation]`, `[ssh_connection]`, etc. Here are some common settings you may want to include:
    
    ```
    [defaults]
    inventory = /etc/ansible/hosts
    remote_user = your_username
    private_key_file = /path/to/your/private/key
    
    [ssh_connection]
    ssh_args = -o ForwardAgent=yes
    
    ```
    
    - `inventory`: Specifies the path to the inventory file.
    - `remote_user`: Specifies the default SSH username to use when connecting to managed hosts.
    - `private_key_file`: Specifies the path to the SSH private key file.
    - `ssh_args`: Specifies additional SSH options.
    
    You can customize these settings according to your environment and requirements.
    
3. **Save and Close the File**:
Once you've added the desired configuration settings, save the `ansible.cfg` file and exit the text editor.
4. **Test the Configuration**:
After creating the configuration file, you can test whether Ansible recognizes it and applies the settings correctly by running Ansible commands, such as:
    
    ```
    ansible all -m ping
    
    ```
    
    or
    
    ```
    ansible-playbook your_playbook.yml
    
    ```
    

By following these steps, you can initialize an Ansible configuration file and customize Ansible's behavior on your controller node. This allows you to tailor Ansible to suit your specific needs and preferences.

## WHAT IS THE USE OF FOLLOWING COMMAND
$ ansible-config init --disabled > ansible.cfg

The command `ansible-config init --disabled > ansible.cfg` is used to generate a basic Ansible configuration file named `ansible.cfg` with all the configuration options disabled.

Here's what each part of the command does:

- `ansible-config init`: This command initializes a new Ansible configuration. By default, it generates a configuration file with all options enabled. However, when used with the `-disabled` option, it generates a configuration file with all options disabled.
- `-disabled`: This option tells Ansible to generate a configuration file with all options disabled. This means that the configuration file will contain all possible configuration options, but they will be commented out (disabled) by default.
- `> ansible.cfg`: This part of the command redirects the output of the `ansible-config init` command (which is the generated configuration file content) to a file named `ansible.cfg`.

So, when you run the command `ansible-config init --disabled > ansible.cfg`, it creates a new file named `ansible.cfg` containing a template of all available configuration options in Ansible, but they are all commented out, indicating they are disabled by default. You can then uncomment and customize the configuration options as needed for your environment.