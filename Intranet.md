#	小程序内网穿透

1. 临时体验开发时往往没有公网域名或者公网IP，本工具提供了一个公网代理服务，目的是方便开发测试

   **内网穿透示意图**

   ![p163821](/Users/zha/Desktop/p163821.png)

2. 使用DingTalk Design CLI启动内网穿透

   * 执行以下命令，下载DingTalk Design CLI工具。

     ```
     npm install dingtalk-design-cli@latest -g
     ```

   * 执行以下命令，检测是否成功安装

     ```
     ding -v
     ```

   * 执行一下命令，启动钉钉内网穿透

     ```
     ding ngrok --subdomain test --port 3000
     ```

     执行完该命令后，访问 `http://dingabcde.test.cn/` 都会映射到 `http://127.0.0.1:3000/`

     * test ：设置的映射域名
     * 3000 ： 设置的端口号

