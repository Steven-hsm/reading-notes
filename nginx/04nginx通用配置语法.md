##  配置语法
1. 配置文件由指令和指令快构成
2. 每条指令以;分号结尾,指令与参数间以空格符号分隔
3. 指令快以{}大括号将多条指令组织在一起
4. include语句允许组合多个配置文件以提升可维护性
5. 使用#符号添加注释,提高可读性
6. 使用$符号使用变量
7. 部分指令的参数支持正则表达式
## 时间单位
1. ms milliseconds
2. s seconds
3. m minutes
4. h hours
5. d days
6. w weeks
7. M months,30天
8. y years,365 days
## 空间单位
1. ' '  bytes
2. k/K kilobytes
3. m/M megabytes
4. g/G gigabytes
## http配置的指令快
1. http
2. server
3. location
4. upstream

