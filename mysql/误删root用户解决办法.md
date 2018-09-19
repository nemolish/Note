误删root用户解决办法

1 、停止mysql5 服务：
net stop mysql5

2、开一个DOS 窗口执行：
c:\mysql\bin>mysqld-nt --skip-grant-tables  &

3、再开一个DOS 窗口执行：
c:\mysql\bin>mysql mysql

```
update user set password = password('new_password') where user = 'root';
```

4、启动mysql5 服务：
net start mysql5