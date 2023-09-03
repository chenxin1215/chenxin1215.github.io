---
title: java操作服务器执行shell
date: 2023-02-08 06:53:14
tags: 
    - Java
categories: 编程经验
---

直接上代码

```java
    // 对象创建
    Connection connection = new Connection(host, port);
    // 登录鉴权
    connection.authenticateWithPassword(userName, password);
    // 获取session
    Session session = con.openSession();
    // 执行命令
    session.execCommand(cmds);

    InputStream stdOut = new StreamGobbler(session.getStdout());
    String outStr = processStream(stdOut, CHARSET);
    logger.info("输出结果："+ outStr);

    InputStream stdErr = new StreamGobbler(session.getStderr());
    String outErr = processStream(stdErr, CHARSET);
    session.waitForCondition(ChannelCondition.EXIT_STATUS, TIME_OUT);
    logger.info("执行命令" + cmds + ",错误信息："+ outErr);

    // 关闭连接
    session.getExitStatus();
    session.close();
```

其中processStream方法代码：
```java
    private static String processStream(InputStream in, String charset) throws Exception {
    StringBuilder sb = new StringBuilder();
        BufferedReader br = new BufferedReader(new InputStreamReader(in));
        while (true) {
        String line = br.readLine();
            if(line == null){
            break;
            }
            if(!line.trim().equals("")){
            sb.append(line).append("\n");
            }      
        }
        return sb.toString();
    }
```



