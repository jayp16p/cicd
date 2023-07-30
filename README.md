# Auto CI/CD w/Ansible, Docker, Github Webhooks on AWS
![image](https://github.com/jayp16p/cicd/assets/106398902/db511281-9c82-4ecf-8262-43c03cd18d4d)

### Download a sample template and push it to this repo
Any basic template that has HTML will do (https://www.free-css.com/free-css-templates/page293/hostit)
Make sure in your repo you have a "Dockerfile" otherwise building might fail "("Cannot locate specified Dockerfile: Dockerfile")"
Create Dockerfile with below contents
```
FROM nginx:latest
COPY . /usr/share/nginx/html"
```

### Create EC2 instances in AWS
Running two of these(using Ubuntu) - One for Jenkins and the other one that runs Docker
* Doing t2.micro for these, if slow bump up
![image](https://github.com/jayp16p/cicd/assets/106398902/770fb60d-8eec-43e0-95e0-a24ab1427c62)

SSH into the Jenkins Instance
run "sudo hostnamectl set-hostname jenkins" - sets hostname to jenkins instead of some confusing long names, keeping things tidy!
#### install Jenkins on this host
* Use https://www.jenkins.io/doc/book/installing/linux/#debianubuntu
* needs some form of JDK, so I'm installing openjdk-17-jre for this lab

* Allow traffic from/to port 8080 for Jenkins in your SG
![image](https://github.com/jayp16p/cicd/assets/106398902/c4fba3e8-2213-4f35-b2cc-392b673fbbcd)

Verify Jenkins is able to be accessesd on port 8080 and setup Jenkins as usual.


### Moving to the Docker instance
* run "sudo hostnamectl set-hostname docker" to change hostname for some tidiness, ran /bin/bash to reload shell and effect new hostname

#### Install Docker
* https://docs.docker.com/engine/install/ubuntu/
* run "sudo usermod -aG docker ubuntu" to add docker to ubuntu group, otherwise have to sudo all the time
* run "newgrp docker" to reload - to refresh the group, now docker should run without root


#### Install Ansible on the Jenkins Instance since this is where the playbooks will run automation to push to Docker Host
* check https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html#installing-ansible-on-ubuntu

* Generate SSH key on docker instance - run as root "ssh-keygen"
* Install dependences on Jenkins Instance to run docker commands - sudo apt install python3-pip && pip install docker

* On the Jenkins instance, go to /etc/ansible and add to the hosts file the group and public IP of the dockerinstance
![image](https://github.com/jayp16p/cicd/assets/106398902/6ffbee4f-03ff-47f6-9620-f6cdc3be7874)
* Become root on jenkins, switch to jenkins user ( su jenkins ) and cd to its home ( cd ~ ), create a directory called playbooks and cd into it..
* run "ssh-keygen" from jenkins host, copy its public file content -> PASTE INTO DOCKER INSTANCE's AUTHORIZED KEYS FILE .ssh/authorized_keys (SO FROM JENKINS USER ON jenkins instance to Docker's authorized keys)
![image](https://github.com/jayp16p/cicd/assets/106398902/d08b920b-d3aa-4d54-80fe-7d05b5eab9fc)
* You should now be able to get into the Docker instance from Jenkins if all worked correctly.

* create a pipeline in Jenkins, settings similar to & Run the pipeline:
![image](https://github.com/jayp16p/cicd/assets/106398902/58d6fb2d-ddb5-4803-a1ad-db7f24c7366a)
![image](https://github.com/jayp16p/cicd/assets/106398902/1e9e712c-1085-4ff0-bdbd-dceaf5b0f922)

* before you run the deployment file from jenkins ( cd /var/lib/jenkins/playbooks/deployment.yaml - make sure you have projects dir in docker instance in the home path )
* to run the playbook its "ansible-playbook deployment.yaml"
![image](https://github.com/jayp16p/cicd/assets/106398902/0ac360b8-88a0-4e0b-8f3c-9f920f6482ea)
* don't worry about the error for the docker image, at this phase copy is working, its getting fixed as we go along

### Enable Webhooks to trigger builds automatically
Ensure that "GitHub hook trigger for GITScm polling" is checked in pipeline
![image](https://github.com/jayp16p/cicd/assets/106398902/2e6d14bf-c138-44ac-a478-1b44dffd84e0)
* In Github
* Make sure you add <IP Address for Jenkins:Port>/github-webhook/ otherwise without "/github-webhook/" it won't work
![image](https://github.com/jayp16p/cicd/assets/106398902/114947de-f3d6-4ab9-8539-6543655585c5)
![image](https://github.com/jayp16p/cicd/assets/106398902/919e915f-2c2e-42c0-a433-5c7c121e2595)

#### Now changes in the Repo will trigger the pipeline!
I just made a test file in repo to trigger the pipeline
![image](https://github.com/jayp16p/cicd/assets/106398902/66aff0e8-5020-4338-9419-3e4fa7fd22e2)


#### Buld the docker image
- Ensure you have a Dockerfile in the path as mention in the starting of this lab
![image](https://github.com/jayp16p/cicd/assets/106398902/132506c8-d325-4e76-9a27-d6ec60c6604b)
* Docker image built!
![image](https://github.com/jayp16p/cicd/assets/106398902/2b5353ad-4140-4d0e-ae5d-a4c4b5d1ae92)
* Site is running, but we need to automate everything... follow along
![image](https://github.com/jayp16p/cicd/assets/106398902/7aecfaef-1c79-4c8e-942b-ecca3900f874)


#### Add Execute shell in build-step
* This is so that everytime there is a change in the git repo, this command "ansible-playbook deployment.yaml" is run to automate everything, instead of running manually!
![image](https://github.com/jayp16p/cicd/assets/106398902/75925e89-5f16-4448-a023-2388f73e5da2)
OR
![image](https://github.com/jayp16p/cicd/assets/106398902/2d679b22-4e72-4842-861b-f8b4ac8d631c)

Checking if all is working..
![image](https://github.com/jayp16p/cicd/assets/106398902/b342cdd7-eb5c-476a-bcac-85a7cf898fa6)

### FINAL TEST.. EDIT the "Call : +01 123455678990" on the homepage or anything else..
### if everything is fine, the build should trigger, and changes should show up..

![image](https://github.com/jayp16p/cicd/assets/106398902/a63cb940-857d-4c7b-be8b-cd01c19bd9ab)
![image](https://github.com/jayp16p/cicd/assets/106398902/f03a1c1e-eda5-4bd2-90ef-836d3df8ada3)
![image](https://github.com/jayp16p/cicd/assets/106398902/8ee46530-a7ed-45d8-aa19-bbfcbc2449df)
![image](https://github.com/jayp16p/cicd/assets/106398902/937174d2-cc0c-40d0-87ca-6b940b4cf358)

VOILA, works!
So.. we have automated the whole process by using Ansible playbooks to Create, Build, Delete the Docker Images and containers everytime there is a change in the repo, and all this is automatically handled
in the background! Webhooks are used to share any changes from the repo to Jenkins which triggers the pipeline, Nginx is used here as the webserver to host the site!

![image](https://github.com/jayp16p/cicd/assets/106398902/6a61e040-d927-4718-983b-a610cdbcd04b)














