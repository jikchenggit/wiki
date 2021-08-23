---
title: ansibleplaybook
description: ansibleplaybook
published: true
date: 2021-08-23T03:44:17.273Z
tags: ansibleplaybook
editor: markdown
dateCreated: 2020-08-21T14:56:00.193Z
---

# 自动化部署

使用`Ansible playbook ` 自动化部署. 

Ansible playbook 会自动执行[安装](/greenplum/安装)中安装步骤.
[Ansible](https://docs.ansible.com/)


> 重要提示:本例子仅作为实例,用于说明如何使用配置工具(如Ansible,Chef或Pupper) 自动完成greenplum 数据库的集群配置和软件安装任务.

示例为Centos7 设计的.它将会创建gpadmin 用户,安装greenplum 软件.将已安装好的所有者和组设置为gpadmin,并未gpadmin 用户设置PAM 安全限制. 

可以修改脚本,以便适配操纵系统平台 

以下是主要步骤 .


1. 安装ANsible , [documentation](https://docs.ansible.com/)
2. 设置从master 节点到所有主机的免密登录.使用`SSH-copy-id` 命令在每个主机上安装公钥.

3. 编辑为hosts 文件,包含所有主机列表.
```
mdw
sdw1
sdw2
...
```
4. 拷贝playbook 文件`naisble-playbook.yml 

5. 编辑playbook 的变量,例如要创建的gpadmin 管理用户和密码.以及正在安装的greenplum 数据库版本.
6. 运行playbook,将要安装的包传输给`packge_path` 参数.
```
ansible-playbook ansible-playbook.yml -i hosts -e package_path=./greenplum-db-6.0.0-rhel7-x86_64.rpm

```

# playbook 配置文件

```
---

- hosts: all
  vars:
    - version: "6.0.0"
    - greenplum_admin_user: "gpadmin"
    - greenplum_admin_password: "changeme"
    # - package_path: passed via the command line with: -e package_path=./greenplum-db-6.0.0-rhel7-x86_64.rpm
  remote_user: root
  become: yes
  become_method: sudo
  connection: ssh
  gather_facts: yes
  tasks:
    - name: create greenplum admin user
      user:
        name: "{{ greenplum_admin_user }}"
        password: "{{ greenplum_admin_password | password_hash('sha512', 'DvkPtCtNH+UdbePZfm9muQ9pU') }}"
    - name: copy package to host
      copy:
        src: "{{ package_path }}"
        dest: /tmp
    - name: install package
      yum:
        name: "/tmp/{{ package_path | basename }}"
        state: present
    - name: cleanup package file from host
      file:
        path: "/tmp/{{ package_path | basename }}"
        state: absent
    - name: find install directory
      find:
        paths: /usr/local
        patterns: 'greenplum*'
        file_type: directory
      register: installed_dir
    - name: change install directory ownership
      file:
        path: '{{ item.path }}'
        owner: "{{ greenplum_admin_user }}"
        group: "{{ greenplum_admin_user }}"
        recurse: yes
      with_items: "{{ installed_dir.files }}"
    - name: update pam_limits
      pam_limits:
        domain: "{{ greenplum_admin_user }}"
        limit_type: '-'
        limit_item: "{{ item.key }}"
        value: "{{ item.value }}"
      with_dict:
        nofile: 524288
        nproc: 131072
    - name: find installed greenplum version
      shell: . /usr/local/greenplum-db/greenplum_path.sh && /usr/local/greenplum-db/bin/postgres --gp-version
      register: postgres_gp_version
    - name: fail if the correct greenplum version is not installed
      fail:
        msg: "Expected greenplum version {{ version }}, but found '{{ postgres_gp_version.stdout }}'"
      when: "version is not defined or version not in postgres_gp_version.stdout"
```



当playbook 执行成功后,可以继续创建存储区域并初始化greenplum 数据库系统.

