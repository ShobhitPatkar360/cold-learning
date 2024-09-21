# Installation Guidance

## How to install Ansible (script)

preferred link - [https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html#installing-ansible-on-ubuntu](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html#installing-ansible-on-ubuntu) 

```bash
# updation system
sudo apt update
sudo apt install software-properties-common
# adding apt repository
sudo add-apt-repository --yes --update ppa:ansible/ansible
# finally installing ansible
sudo apt install ansible -y

# you have to run the ansible via sudo user
```

## How to install Java

```bash
# apporach 1
sudo apt install openjdk-17-jre -y

# approach 2
sudo apt install default-jdk -y
sudo apt install default-jre -y

# approach 3
sudo apt update
sudo apt install openjdk-11-jre -y
sudo apt install default-jdk -y
```

Long approach for installing java

```bash
# approach 4 

# download your jdk zip file from your desired source
wget https://download.java.net/openjdk/jdk21/ri/openjdk-21+35_linux-x64_bin.tar.gz

# create a directory to store your extracted files
sudo mkdir -p /opt/jdk

# Extract file to your directory 
sudo tar -xvf openjdk-21+35_linux-x64_bin.tar.gz -C /opt/jdk

# Open this file to add the envoronment variable - we are adding environment variable as our user
vi .bashrc

# getting java path
update-alternatives --list java
which java

# changing the default java pickup version
sudo update-alternatives --config java

# the following lines to your .bashrc file and save it
export JAVA_HOME=/opt/jdk/jdk-21
export PATH=$JAVA_HOME/bin:$PATH

# Activate your environment variables
source ~/.bashrc

# check your java varsion
java -version
```

You can also change environment variable by following way

```bash
# set the envronment variable
sudo vi /etc/environment

# paste the command
JAVA_HOME=<your_java_path>

# refresh the environment file
source /etc/environment

# test java enviroment variable, You will get output as your java dirctory
echo $JAVA_HOME
```

## How to install Jenkins

preferred link - [https://www.jenkins.io/doc/book/installing/linux/#debianubuntu](https://www.jenkins.io/doc/book/installing/linux/#debianubuntu) 

```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y
sudo systemctl start jenkins
```

## How to install Docker

preferred link - [https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository) 

First set up kubernete cluster then docker otherwise it will create  container run time enviroment (cri) error while executing `sudo kubeadm init` 

```bash
# to executeh shell scritp in remote machine
# use the command -> ssh -i sav-kp.pem ubuntu@54.205.161.235 'bash -s' < ./setup1.sh 
# start your script with first line - 
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

# execute docker commands with sudo or root user
```
Don't forget to add the current user to docker group
```bash
sudo usermod -aG docker $USER
newgrp docker
```


## Kubernetes Installation
1. Use link to set up kubernetes cluster - https://github.com/ShobhitPatkar360/kubestarter/blob/main/kubeadm_installation.md
2. First install kubernetes then docker to your kubernetes controller otherwise you will face CRI error
3. Give host name to your system using command - sudo hostnamectl set-hostname <your-hostname>
4. Run all the commands ( to generate the master and slave node as normal user)
5. First create the master node, generate the token then start working on kubernete worker.
6. For deleting a node, use - https://stackoverflow.com/questions/35757620/how-to-gracefully-remove-a-node-from-kubernetes 
