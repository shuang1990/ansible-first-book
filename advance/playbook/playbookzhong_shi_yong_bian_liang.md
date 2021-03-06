# 在Playbook中用户自定义的变量

用户可以在Playbook中，通过vars关键字自定义变量，使用时用\{\{ \}\}引用以来即可。

## Playbook中定义和使用的变量的方法

例如下面的例子中，用户定义变量名为http\_port，其值为为80。在tasks firewalld中，通过{{ http\_port }}引用。

```
---
- hosts: web
  vars:
    http_port: 80
  remote_user: root
  tasks:
  - name: insert firewalld rule for httpd
    firewalld: port={{ http_port }}/tcp permanent=true state=enabled immediate=yes
```

## 把变量放在单独的文件中

当变量比较多的时候，或者变量需要在多个playbook中重用的时候，可以把变量放到一个单独的文件中。通过关键字var\_files把文件中定义的变量引入playbook中，使用变量的方法和在本文件中定义的变量相同。

```
- hosts: web
  remote_user: root
  vars_files:
      - vars/server_vars.yml
  tasks:
  - name: insert firewalld rule for httpd
    firewalld: port={{ http_port }}/tcp permanent=true state=enabled immediate=yes
```

变量文件vars/server\_vars.yml的内容为：

```
http_port: 80
```

## 定义和使用复杂变量

有时候我们需要使用的变量的值不是简单的字符串或者数字，而是一个对象。这时候定义的语法如下，格式为YAML的字典格式：

```
foo:
  field1: one
  field2: two
```

访问复杂变量中的子属性，可以利用中括号或者点号：

```
foo['field1']
foo.field1
```

## YAML的陷阱

YAML的陷阱是某些时候YAML和Ansible Playbook的变量语法不能在一起好好工作了。这里仅发生在指冒号后面的值不能以{开头的时候，如果有必要以{开头，必须加上引号。总之在YAML值的定义中，如果提示YMAL语法错误，都可以尝试下加入引号来解决。

下面的代码会报错:

```
- hosts: app_servers
  vars:
      app_path: {{ base_path }}/22
```

解决办法：要在"{ "开始的值**加上引号**:

```
- hosts: app_servers
  vars:
       app_path: "{{ base_path }}/22"
```



