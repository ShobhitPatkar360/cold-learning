# Jenkins Practical

## Some useful links

[https://www.javatpoint.com/jenkins](https://www.javatpoint.com/jenkins) 

[https://www.tutorialspoint.com/jenkins/jenkins_git_setup.htm](https://www.tutorialspoint.com/jenkins/jenkins_git_setup.htm) 

[https://docs.stackhawk.com/continuous-integration/jenkins.html](https://docs.stackhawk.com/continuous-integration/jenkins.html) 

[https://www.jenkins.io/doc/](https://www.jenkins.io/doc/) 

## Give the detailed steps to set up Jenkins in AWS EC2 instance - Ubuntu

### Prerequisites

- An AWS account with necessary permissions.
- Basic understanding of Linux commands.

### Steps

### 1. Launch an EC2 Instance

- Log in to your AWS Management Console.
- Navigate to the EC2 service.
- Launch a new EC2 instance.
- Choose an appropriate Amazon Machine Image (AMI), such as Amazon Linux 2.
- Select an instance type based on your workload requirements.
- Configure network settings, security groups (allow inbound traffic on port 22 for SSH and 8080 for Jenkins), and key pair.

![Untitled](Jenkins%20Practical%203f97f9b7bf0c40f1826a7b3a71f8390b/Untitled.png)

### 2. SSH into the Instance

- Use an SSH client (like PuTTY) to connect to your EC2 instance using the private key associated with your key pair.
- 

![Untitled](Jenkins%20Practical%203f97f9b7bf0c40f1826a7b3a71f8390b/Untitled%201.png)

### 3. Update System Packages

- Update the package lists and install any necessary updates:
    
    Bash
    
    `sudo apt update -y`
    

### 4. Install Java

- Visit this website to download proper jdk zip file - https://jdk.java.net/22/
- Supported Java versions are: [11, 17, 21] ( following instruction do not work, sorry)

```yaml
# download your jdk zip file
wget https://download.java.net/openjdk/jdk21/ri/openjdk-21+35_linux-x64_bin.tar.gz

# create a directory to store your extracted files
sudo mkdir -p /opt/jdk

# Extract file to your directory 
sudo tar -xvf openjdk-21+35_linux-x64_bin.tar.gz -C /opt/jdk

# Open this file to add the envoronment variable - we are adding environment variable as our user
vi .bashrc

# the following lines to your .bashrc file and save it
export JAVA_HOME=/opt/jdk/jdk-21
export PATH=$JAVA_HOME/bin:$PATH

# Activate your environment variables
source ~/.bashrc

# check your java varsion
java -version
```

- Installing java and jenkins in ubuntu
- 

```yaml
sudo apt update
sudo apt install openjdk-11-jre -y
sudo apt install default-jdk -y

# setting java home
# get the path for your java, copy it
which java
update-alternatives --list java

# set the envronment variable
sudo vi /etc/environment

# paste the command
JAVA_HOME=<your_java_path>

# refresh the environment file
source /etc/environment

# test java enviroment variable, You will get output as your java dirctory
echo $JAVA_HOME
```

### 5. Install Jenkins

- Go to websit  - [https://pkg.jenkins.io/debian-stable/](https://pkg.jenkins.io/debian-stable/)
- 

```yaml
  sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
  
  
  echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null
    
  sudo apt-get update
  sudo apt-get install fontconfig -y
  sudo apt-get install jenkins -y
  
  
```

### 6. After jenkins installation

```yaml
# check jenkins installation
jenkins -version

# check jenkins service
sudo systemctl status jenkins

# start jenkins service , not required
sudo systemctl start jenkins

# enable jenkins service to start on boot
sudo systemctl enable jenkins

# go to webpage to access your jenkins dashboard
<your_public_ip>:8080
```

## Create a git project, based on the concept of webhook

### Step 1: Creating a repo and adding origin

Create a repo in your system

```yaml
ubuntu@ubuntujenkins:~$ mkdir test_repo_03
ubuntu@ubuntujenkins:~$ cd test_repo_03/
ubuntu@ubuntujenkins:~/test_repo_03$ git init
hint: Using 'master' as the name for the initial branch. This default branch name
hint: is subject to change. To configure the initial branch name to use in all
hint: of your new repositories, which will suppress this warning, call:
hint: 
hint:   git config --global init.defaultBranch <name>
hint: 
hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
hint: 'development'. The just-created branch can be renamed via this command:
hint: 
hint:   git branch -m <name>
Initialized empty Git repository in /home/ubuntu/test_repo_03/.git/
ubuntu@ubuntujenkins:~/test_repo_03$ echo "hello" > file1
ubuntu@ubuntujenkins:~/test_repo_03$ cat file1
hello
ubuntu@ubuntujenkins:~/test_repo_03$ 

```

Create a repo

![Untitled](Jenkins%20Practical%203f97f9b7bf0c40f1826a7b3a71f8390b/Untitled%202.png)

Add origin to your repo

```yaml
# add your 
git add .
#commit your changes
git commit -m "first commit"
# add origin (your repo) to your local repo
git remote add origin git@github.com:ShobhitPatkar360/test-repo-3.git
# rename your branch
git branch -M main
# push your changes
git push -u origin main
```

### Step 2 : Setting Webhook and Creating a job

 Configure webhook from jenkins, copy this payload, used later

![Untitled](Jenkins%20Practical%203f97f9b7bf0c40f1826a7b3a71f8390b/Untitled%203.png)

Add a create a webhook form repo (from github), here you will paste your payload

![Untitled](Jenkins%20Practical%203f97f9b7bf0c40f1826a7b3a71f8390b/Untitled%204.png)

Creating a job

![Untitled](Jenkins%20Practical%203f97f9b7bf0c40f1826a7b3a71f8390b/Untitled%205.png)

Give github link

![Untitled](Jenkins%20Practical%203f97f9b7bf0c40f1826a7b3a71f8390b/Untitled%206.png)

Set build trigger

![Untitled](Jenkins%20Practical%203f97f9b7bf0c40f1826a7b3a71f8390b/Untitled%207.png)

Give building step

![Untitled](Jenkins%20Practical%203f97f9b7bf0c40f1826a7b3a71f8390b/Untitled%208.png)

 and check your build, run it manually

### Step 3: Checking the working of webhook

Now push some changes

```yaml
ubuntu@ubuntujenkins:~/test_repo_03$ ls
file1
ubuntu@ubuntujenkins:~/test_repo_03$ echo "this is file2" > file2
ubuntu@ubuntujenkins:~/test_repo_03$ git add .
ubuntu@ubuntujenkins:~/test_repo_03$ git commit -m "file2 addded"
[main 1e79704] file2 addded
 1 file changed, 1 insertion(+)
 create mode 100644 file2
(failed reverse-i-search)`': sudo a^C install default-jdk -y
ubuntu@ubuntujenkins:~/test_repo_03$ git push --all
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 2 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 274 bytes | 274.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:ShobhitPatkar360/test-repo-3.git
   812c837..1e79704  main -> main
ubuntu@ubuntujenkins:~/test_repo_03$ 

```

your code builds successfully

```yaml
Started by GitHub push by ShobhitPatkar360
Running as SYSTEM
Building in workspace /var/lib/jenkins/workspace/job1
The recommended git tool is: NONE
No credentials specified
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/job1/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/ShobhitPatkar360/test-repo-3.git # timeout=10
Fetching upstream changes from https://github.com/ShobhitPatkar360/test-repo-3.git
 > git --version # timeout=10
 > git --version # 'git version 2.43.0'
 > git fetch --tags --force --progress -- https://github.com/ShobhitPatkar360/test-repo-3.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
Checking out Revision 1e79704ea1ab5796a8728316b418a506c9f50581 (refs/remotes/origin/main)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 1e79704ea1ab5796a8728316b418a506c9f50581 # timeout=10
Commit message: "file2 addded"
 > git rev-list --no-walk 812c8376aefb6b7f87b23499372dd21c613fdc3f # timeout=10
[job1] $ /bin/sh -xe /tmp/jenkins13483597466428250686.sh
+ echo your job run
your job run
+ ls
file1
file2
Finished: SUCCESS
```

# Working with scripts

## Create a scripted jenkins project to clone a repo from github

```yaml
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                // We need to give brancha and url
                git branch:'main' , url:'https://github.com/ShobhitPatkar360/project-3.git'
            }
        }
        stage('Print message') {
            steps {
                sh 'ls'
                sh " echo 'your git clone done' "
            }
        }
    }
}

```

## How to push an image in dockerHub (using docker pipeline plugin)

```yaml
        stage('Docker Push') {
          steps {
						withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
            sh 'echo "$DOCKERHUB_PASSOWORD" | docker login -u "$DOCKERHUB_USERNAME" --password-stdin'
            sh "docker push shubh360/my-web:latest"
            }
          }   
        }
```

## How to create kubernete deployment and service via jenkins pipeline

```yaml
        stage('Creating Deployment and Service') {
            steps {
                sh 'echo "deleting old deployment"'
                sh 'kubectl delete deploy my-deploy || true'
                sh 'echo "creating deployment and service"'
                sh 'kubectl apply -f my-deploy.yml'
                sh 'kubectl apply -f node-port.yml'
                sh 'kubectl get deploy && kubectl get svc'
                
                echo 'you have to execute  following commoand manually' 
                echo 'minikube service my-nodeport-service --url'
            }
```

## How to clone multiple git repositories

```bash
    stages {
        stage('first repo') {
            steps {
                sh 'git clone https://github.com/ShobhitPatkar360/project-3.git'
            }
        }
        
        stage('second repo') {
            steps {
                sh 'git clone https://github.com/ShobhitPatkar360/test-repo-3.git'
                
            }
        }
        
        stage('listing items') {
            steps {
                sh '''echo "listing file of project-3"
                      ls project-3
                      echo "listing file of test-repo-3"
                      ls test-repo-3
                      '''
            }
        }
     }
```

## How to clean the workspace before execution of a stage

```bash
 stages {
        stage('cleaning workspace') {
            steps {
                cleanWs()
            }
        }

        stage('first repo') {
            steps {
                sh 'git clone https://github.com/ShobhitPatkar360/project-3.git'
            }
        }
  }
```