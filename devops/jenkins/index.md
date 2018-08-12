---
layout: default
---
# Continuous-Integration, Continuous-Deployment và hành trình với Jenkins.

Bài viết này sẽ bỏ qua các phần lý thuyết và định nghĩa dài dòng, thay vào đó chúng ta đặt ra một bài toán, và đi giải nó - bằng **Jenkins** kết hợp cùng với Rocketeer hoặc Capistrano.

## 1. Demo:

## 2. Đặt vấn đề:

> Là một Scrum Master có thiên hướng về Technical hay là một Teachnical Lead. Một ngày nào đó bạn bắt đầu cảm thấy bản thân phải quản lý quá nhiều Server, team phát triển đông và source code liên tục được thêm vào hệ thống.
>Bạn sẽ cần đến **Continuous-Integration, Continuous-Deployment**.

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

<img src="https://quynh-nguyen.github.io/devops/jenkins/JenkinsHome.png" title="CI/CD Workflow" width="200">

Lấy password generated bởi quá trình cài đặt Jenkins Service:

```shell
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Switch command user sang `jenkins`. Từ thời điểm này chúng ta sẽ phải chạy toàn bộ _command line_ bằng user này.

```shell
sudo su jenkins
```

Chọn một số Jenkins plugin cơ bản mà bạn muốn sử dụng. Anh em cũng có thể bỏ qua bước này, vì có thể cài thêm plugins bất cứ lúc nào.

<img src="https://quynh-nguyen.github.io/devops/jenkins/JenkinsPlugins.png" title="Jenkins Plugins" width="200">

Tạo Administrator user và truy cập vào Jenkins Dashboard.

<img src="https://quynh-nguyen.github.io/devops/jenkins/JenkinsDashboard.png" title="Jenkins Dashboard" width="200">

### 4.2 Cài đặt bảo mật cho Jenkins:

Để phân quyền và bảo vệ các `job` của Jenkins.
Chúng ta sẽ cần cài đặt 1 Plugin: [Role-based Authorization Strategy](http://wiki.jenkins-ci.org/display/JENKINS/Role+Strategy+Plugin)

1. Jenkins > Manage Jenkins > Manage Plugins > Choose tab Available > Search Role-based Authorization and install.
2. Jenkins > Manage Jenkins > Configure Global Security >
* Tạo global roles, ví dụ như admin, job creator, anonymous, etc., và thiết lập các quyền: Overall, Slave, Job, Run, View
* Tạo project roles, allowing to set only Job and Run permissions on a project basis.
* Tạo slave roles, allowing to set node-related permissions.
* Assigning these roles to users.

Text can be **bold**, _italic_, ~~strikethrough~~ or `keyword`.

[Link to another page](https://kubernetes.io/).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### Header 3

```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![Octocat](https://assets-cdn.github.com/images/icons/emoji/octocat.png)

### Large image

![Branching](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```
