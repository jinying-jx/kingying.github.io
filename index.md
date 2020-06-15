### 安装ansible

```
# 安装依赖
[root@ansible ~]$ yum -y install gcc zlib zlib-devel openssl-devel

# 编译安装Python 3.6.5
[root@ansible ~]$ wget https://www.python.org/ftp/python/3.6.5/Python-3.6.5.tgz
[root@ansible ~]$ tar -zxvf Python-3.6.5.tgz
[root@ansible ~]$ cd Python-3.6.5
[root@ansible ~]$ ./configure
[root@ansible ~]$ make && make install

# 安装 virtualenv
[root@ansible ~]$ python3 -m pip install virtualenv
[root@ansible ~]$ useradd deploy
[root@ansible ~]$ su - deploy
[deploy@ansible ~]$ virtualenv -p $(which python3) .venv

# 安装 ansible
[deploy@ansible ~]$ source .venv/bin/activate
(.venv) [deploy@ansible ~]$ pip install ansible
(.venv) [deploy@ansible ~]$ ssh-keygen
(.venv) [deploy@ansible ~]$ ssh-copy-id node1.example.com
```

### 测试playbook

```
(.venv) [deploy@ansible ~]$ tree .
.
├── deploy.yml
├── inventory
│   └── testenv
└── roles
    └── testbox
        └── tasks
            └── main.yml
```
./inventory/testenv
```
(.venv) [deploy@ansible ~]$ vim inventory/testenv
```

```
[testservers]
node1.example.com

[testservers:vars]
server_name=node1.example.com
user=root
output=/root/test.txt
```


./roles/testbox/tasks/main.yml
```
(.venv) [deploy@ansible ~]$ vim roles/testbox/tasks/main.yml
```

```
- name: Print Servername and user to remote testbox
  shell: "echo 'Currently {{user}} is logining {{server_name}}' >> {{output}}"
```

./deploy.yml
```
(.venv) [deploy@ansible ~]$ vim deploy.yml
```

```
- hosts: "testservers"
  gather_facts: true
  remote_user: root
  roles:
    - testbox
```


