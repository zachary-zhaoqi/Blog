```
try:
    fo = open("zhangheng/python/core_field.txt", "r")  #字段验证
    fh = open("core_field.log", "w")  #字段验证日志输出
    lines  = fo.readlines()
    errorlog = open("error.log", "w")  #字段验证日志输出
    print len(lines)
    print '开始执行sql文档，将不会打印动态日志，如果执行失败，会将sql以及失败原因写入log日志文件,英文日志将存放于error.log中：'
    for cmd in lines:    
        print cmd
        try:
            p = subprocess.Popen(cmd,shell=True, close_fds=True, stdin = subprocess.PIPE, stdout = subprocess.PIPE, stderr = subprocess.PIPE)
            while True:
                stdout,stderr = p.communicate()  #记录错误日志文件
                returncode = p.returncode
            #    print returncode
                if returncode == 0:
                    print '执行结果：执行成功！'
                 #   fh.write('执行结果：执行成功！\r\n')
                if returncode != 0:
                    errors = stderr.split('\n')
                    for erroline in errors :
                        if 'FAILED' in erroline:
                           errorlog.write(cmd)  #写入错误行查询语句
                           errorlog.write(erroline)  #写入错误行英文日志
                    fh.write(cmd)
                if returncode == 127:
                    print '执行结果：此行为空字符串！'
                    fh.write('执行结果：此行为空字符串！\r\n')  #中文日志
                if returncode == 17:
                    print '执行结果：找不到表异常'
                    fh.write('执行结果：找不到表\r\n')  #中文日志
                if returncode == 64:
                    print '执行结果：sql语句异常，缺失关键字'
                    fh.write('执行结果：sql语句错误！缺失关键字\r\n')  #中文日志
                if returncode == 41:
                    print '执行结果：sql语句异常，查询的字段不存在'
                    fh.write('执行结果：sql语句异常！查询的字段不存在\r\n')  #中文日志
                if stdout == '' and p.poll() != None:
                    break
        except Exception,re:
            print "调用hive客户端，执行hive语句出错"
            print str(re)
except Exception,re:
        print "读取文件错误！"
finally:
    fh.close()
    errorlog.close()
————————————————
版权声明：本文为CSDN博主「zhangheng007a」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_38120374/article/details/80607369
```


以上代码中returncode为执行返回结果的标志

我总结的有以下几种情况

returncode=0 表示执行成功

returncode=127 表示语句为空串

returncode=17 表示找不到表

returncode=64 表示缺失关键字

returncode=41 表示查询的字段不存在

如果有疑问，大家可以copy我的脚本自行进行验证。
————————————————
版权声明：本文为CSDN博主「zhangheng007a」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_38120374/article/details/80607369