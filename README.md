# Auto CI/CD w/Ansible, Docker, Github Webhooks on AWS
![image](https://github.com/jayp16p/cicd/assets/106398902/db511281-9c82-4ecf-8262-43c03cd18d4d)

### Download a sample template and push it to this repo
Any basic template that has HTML will do (https://www.free-css.com/free-css-templates/page293/hostit)

### Create EC2 instances in AWS
Running two of these(using Ubuntu) - One for Jenkinks and the other one that runs Docker
Doing t2.micro for these, if slow bump up
![image](https://github.com/jayp16p/cicd/assets/106398902/770fb60d-8eec-43e0-95e0-a24ab1427c62)

SSH into the Jenkins Instance
run "sudo hostnamectl set-hostname jenkins" - sets hostname to jenkins instead of some confusing long names, keeping things tidy!
#### install Jenkins on this host
* Use https://www.jenkins.io/doc/book/installing/linux/#debianubuntu
* needs some form of JDK, so I'm installing openjdk-17-jre for this lab

* Allow traffic from/to port 8080 for Jenkins in your SG
![image](https://github.com/jayp16p/cicd/assets/106398902/c4fba3e8-2213-4f35-b2cc-392b673fbbcd)

Verify Jenkins is able to be accessesd on port 8080 and setup Jenkins as usual.





