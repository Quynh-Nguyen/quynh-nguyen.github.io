---
layout: page
title: Continuous-Integration, Continuous-Deployment và hành trình với Jenkins.
date: 2018-08-12
author: whyn4
categories: [ CI/CD, Jenkins ]
image: devops/jenkins/Jenkins%20Workflow.png
featured: true
hidden: false
---

Bài viết này sẽ bỏ qua các phần lý thuyết và định nghĩa dài dòng, thay vào đó chúng ta đặt ra một bài toán, và đi giải nó - bằng **Jenkins** kết hợp cùng với Rocketeer hoặc Capistrano.

## 1. Demo:

## 2. Đặt vấn đề:

> Là một Scrum Master có thiên hướng về Technical hay là một Teachnical Lead. Một ngày nào đó bạn bắt đầu cảm thấy bản thân phải quản lý quá nhiều Server, team phát triển đông và source code liên tục được thêm vào hệ thống.
>Bạn sẽ cần đến **Continuous-Integration, Continuous-Deployment**.

<!--more-->

## 3. Giải pháp:

![CI/CD Workflow](https://quynh-nguyen.github.io/devops/jenkins/Jenkins%20Workflow.png)

## 4. Xây dựng:

### 4.1. Cài đặt Jenkins:

Chúng ta sẽ cài đặt Jenkins Core và một số Plugins cũng như Dependencies cho Jenkins.

```shell
wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install -y jenkins ant git-core curl unzip
##Please notice shutdown all others service running with port 8080
##Check by this command:
sudo netstat -anp | grep 8080
```

Khi thấy kết quả: _jenkins is already the newest version (x.x.x)_. Có nghĩa là bạn đã cài đặt thành công.

### 4.2. Start Jenkins Service:

```shell
sudo service jenkins start
```

Truy cập [http://localhost:8080/](http://localhost:8080/) bằng Webbrowser để kiểm tra Jenkins WebService đã hoạt động chưa.

<img src="https://quynh-nguyen.github.io/devops/jenkins/JenkinsHome.png" title="CI/CD Workflow" width="300">

Lấy password generated bởi quá trình cài đặt Jenkins Service:

```shell
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Switch command user sang `jenkins`. Từ thời điểm này chúng ta sẽ phải chạy toàn bộ _command line_ bằng user này.

```shell
sudo su jenkins
```

Chọn một số Jenkins plugin cơ bản mà bạn muốn sử dụng. Anh em cũng có thể bỏ qua bước này, vì có thể cài thêm plugins bất cứ lúc nào.

<img src="https://quynh-nguyen.github.io/devops/jenkins/JenkinsPlugins.png" title="Jenkins Plugins" width="300">

Tạo Administrator user và truy cập vào Jenkins Dashboard.

<img src="https://quynh-nguyen.github.io/devops/jenkins/JenkinsDashboard.png" title="Jenkins Dashboard" width="300">

### 4.2 Cài đặt bảo mật cho Jenkins:

Để phân quyền và bảo vệ các `job` của Jenkins.
Chúng ta sẽ cần cài đặt 1 Plugin: [Role-based Authorization Strategy](http://wiki.jenkins-ci.org/display/JENKINS/Role+Strategy+Plugin)

1. Jenkins > Manage Jenkins > Manage Plugins > Chọn tab Available > Tìm Role-based Authorization và cài đặt.
2. Jenkins > Manage Jenkins > Configure Global Security >

* Tạo global roles, ví dụ như admin, job creator, anonymous, etc., và thiết lập các quyền: Overall, Slave, Job, Run, View
* Tạo project roles, allowing to set only Job and Run permissions on a project basis.
* Tạo slave roles, allowing to set node-related permissions.
* Assign roles cho những User.

3. Jenkins > Manage Jenkins > Manage Roles

<img src="https://quynh-nguyen.github.io/devops/jenkins/JenkinsRoles.png" title="Jenkins Dashboard" width="500">

4. Jenkins > Manage Jenkins > Assign Roles

<img src="https://quynh-nguyen.github.io/devops/jenkins/JenkinsAssignRole.png" title="Jenkins Dashboard" width="500">

5. Disable sign up users by Jenkins > Manage Jenkins > Configure Global Security 

<img src="https://quynh-nguyen.github.io/devops/jenkins/JenkinsConfigureAuthorization.png" title="Jenkins Dashboard" width="500">

### 4.3 Cài đặt một số plugins hỗ trợ cho Jenkins:

```shell
cd /tmp
wget http://localhost:8080/jnlpJars/jenkins-cli.jar
java -jar jenkins-cli.jar -s http://localhost:8080 install-plugin chatwork --username administrator --password test
##Notice: administrator / test is username and password when create new users step.
##Notice: chatwork mean Jenkins plugin name
java -jar jenkins-cli.jar -s http://localhost:8080 safe-restart --username administrator --password test
```

Một số plugins cần thiết:

1. Bitbucket - https://wiki.jenkins-ci.org/display/JENKINS/BitBucket+Plugin
2. Build Pipeline - https://wiki.jenkins-ci.org/display/JENKINS/Build+Pipeline+Plugin
3. Chatwork - https://wiki.jenkins-ci.org/display/JENKINS/ChatWork+Plugin
4. Role-based Authorization Strategy (cái này cài lúc set security rồi) - http://wiki.jenkins-ci.org/display/JENKINS/Role+Strategy+Plugin
5. Checkstyle - http://wiki.jenkins-ci.org/display/JENKINS/Checkstyle+Plugin
6. Clover PHP - http://wiki.jenkins-ci.org/display/JENKINS/Clover+PHP+Plugin
7. Crap4J - http://wiki.jenkins-ci.org/display/JENKINS/Crap4J+Plugin
8. DRY - http://wiki.jenkins-ci.org/display/JENKINS/DRY+Plugin
9. HTML Publisher - http://wiki.jenkins-ci.org/display/JENKINS/HTML+Publisher+Plugin
10. JDepend - http://wiki.jenkins-ci.org/display/JENKINS/JDepend+Plugin
11. Plot - http://wiki.jenkins-ci.org/display/JENKINS/Plot+Plugin
12. PMD - http://wiki.jenkins-ci.org/display/JENKINS/PMD+Plugin
13. Violations - http://wiki.jenkins-ci.org/display/JENKINS/Violations
14. Warnings - https://wiki.jenkins-ci.org/display/JENKINS/Warnings+Plugin
15. xUnit - http://wiki.jenkins-ci.org/display/JENKINS/xUnit+Plugin

### 4.4 Cài đặt Autobuild kết hợp với Bitbucket:

1. Access to Bitbucket > Repository of project > Settings > Webhooks > Add webhook

<img src="https://quynh-nguyen.github.io/devops/jenkins/BitbucketJenkinsPush.png" title="Jenkins Dashboard" width="500">

2. Create new job/item

<img src="https://quynh-nguyen.github.io/devops/jenkins/BitbucketJenkinsPush2.png" title="Jenkins Dashboard" width="500">

### 4.5 Cài đặt Rocketeer để kết hợp với Jenkins:

1. Install Rocketeer

```shell
wget http://rocketeer.autopergamene.eu/versions/rocketeer.phar
chmod +x rocketeer.phar
mv rocketeer.phar /usr/local/bin/rocketeer
//TODO Install PHP for Jenkins server
sudo apt-get install php
//TODO Check rocketeer
rocketeer check
No connections have been set, please create one: (production) <~ Succeed
```

2. Setup remote server information

```shell
$ cd /var/lib/jenkins/drone-deploy/drone-deploy/drone-php
$ rocketeer ignite
No connections have been set, please create one: (production)develop
No host is set for [develop], please provide one:35.166.67.136
No username is set for [develop], please provide one:ec2-user
No password or SSH key is set for [develop], which would you use? (key) [key/password]key
Please enter the full path to your key (/var/lib/jenkins/.ssh/id_rs/var/lib/jenkins/.ssh/drone_project.pem
If a keyphrase is required, provide it
No repository is set for [repository], please provide one:git@bitbucket.org:nldanang/drone_php.git
No username is set for [repository], please provide one:quynh.nn@neo-lab.vn
No password is set for [repository], please provide one:
develop/0 | Ignite (Creates Rocketeer's configuration)
What is your application's name ? (drone-php)drone_deploy
The Rocketeer configuration was created at drone-php/.rocketeer
```

3. Configure

```shell
$ cd /var/lib/jenkins/drone-deploy/drone-deploy/drone-php
$ nano .rocketeer/config.php
Replace connections name production --> develop //It's Rocketeer bug
$ nano .rocketeer/remote.php
'root_directory' => '/var/www/html/',
'shared'         => [
        'storage/logs',
        'storage/framework/sessions',
        '.env',
    ],
'permissions'    => [
 
        // The folders and files to set as web writable
        'files'    => [
            //'app/database/production.sqlite',
            'bootstrap',
            'storage',
            'public',
        ],
 
        // Here you can configure what actions will be executed to set
        // permissions on the folder above. The Closure can return
        // a single command as a string or an array of commands
        'callback' => function ($task, $file) {
            return [
                sprintf('chmod -R 777 %s', $file),
                sprintf('chmod -R g+s %s', $file),
                sprintf('chown -R ec2-user:ec2-user %s', $file),
            ];
        },
 
    ],
$ nano .rocketeer/strategies.php
    //'test'         => 'Phpunit',
    'test'         => '',
    //return $composer->install([], ['--no-interaction' => null, '--no-dev' => null, '--prefer-dist' => null]);
    return $composer->install([]);
```

4. Nào cùng kiểm tra xem Rocketeer trên Jenkins Server của chúng ta đã chạy ngon chưa
```shell
rocketeer deploy --on="develop" --tests
```

```
Quỳnh Nguyễn
Email: likeguitarz@gmail.com
```
