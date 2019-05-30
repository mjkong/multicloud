# EC2에 ansible을 활용하여 nginx 설치하기

## ansible 환경설정

ansible 실행을 위해서 아래의 절차대로 실행하여 환경을 설정합니다.

~~~shell
$ sudo mkdir /etc/ansible
~~~


```/etc/ansible/ansible.cfg``` 파일을 생성합니다.


~~~shell
[defaults]
private_key_file=<ec2 실습시 사용한 key 파일 위치>/infra_admin
remote_user = ubuntu
~~~

```/etc/ansible/hosts``` 파일을 생성합니다.

~~~shell
[ec2_server]
13.125.252.116 (앞서 생성한 ec2 public ip 정보로 교체)
~~~

임의의 디렉토리에서 ```nginx_install.yaml``` 파일을 생성하고 다음과 같은 내용을 입력합니다.

~~~shell
---
- hosts: ec2_server
  become: yes
  tasks:
    - name: Installs Nginx web server
      apt: pkg=nginx state=installed update_cache=true
      notify:
        - start nginx

    - name: Add a line in /etc/nginx/sites-available/default
      lineinfile: dest=/etc/nginx/sites-available/default
                  regexp='# Only for nginx-naxsi'
                  line='location /images {}'
~~~


파일 작성이 완료되었으면 다음의 명령을 통해서 EC2 서버에 반영합니다.

~~~shell
$ ansible-playbook nginx_install.yaml
~~~