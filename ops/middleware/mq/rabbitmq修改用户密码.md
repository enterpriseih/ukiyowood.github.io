# rabbitmq修改用户密码

```bash
rabbitmqctl  list_users    # 查看用户列表

rabbitmqctl  change_password  username  'new_pass'    # 修改对应用户的密码
```