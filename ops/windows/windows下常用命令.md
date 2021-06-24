# windows下常用命令

## 进程
```
tasklist | findstr nginx

# 杀进程
taskkill /pid PID /F
```

## 执行任务
```
# 执行程序
start demo.exe

# 后台执行程序，不能关cmd
start /b demo.exe
```