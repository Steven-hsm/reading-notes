## nginx命令行

1. 格式: nginx -s reload

2. 帮助: -? -h

3. 使用指定的配置文件:-c

4. 指定配置指令:-g

5. 指定运行目录:-p

6. 发送信号: -s

```shell
	nginx -s stop #立即停止服务
	nginx -s quit #优雅的停止服务
	nginx -s reload #重载配置文件
	nginx -s reopen # 重新开始记录日志文件
```

7. 测试配置文件是否有语法错误:-t -T
8. 打印nginx的版本信息,编译信息: -v -V