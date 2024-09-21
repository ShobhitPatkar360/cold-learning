# Jenkins Practical Info Part 2

## Required information for connection Agent.

```yaml
# public ip of worker node
44.195.26.68

# private ssh key of worker and paste with username to jenkins console
-----BEGIN RSA PRIVATE KEY-----
......youe key text..........
-----END RSA PRIVATE KEY-----
# jenkins directory
/var/jenkins

# java path
/usr/lib/jvm/java-21-openjdk-amd64/bin/java

# public ssh key of jenkins controler user to be paste on auth file of worker node
ssh-rsa ...your key text..... jenkins@kali

```
### Some Suggestions
1. Use AWS Provided key as credentials
2. host verifiction strategy (known host or non verifying) both are all right
3. use command "update-alternatives --list java" to get java path
4. don't forget to paste the public key of jenkins contrller to Agent auth file.
5. Set proper user for /var and /var/jenkins directory
