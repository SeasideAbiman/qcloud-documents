本文档将指导您如何排查 Windows 入侵类问题。
## 深入分析，查找入侵原因
### 一. 检查帐户和弱口令
1. 查看服务器已有系统或应用帐户是否存在弱口令。
 - 检查说明：主要检查系统管理员帐户、网站后台帐户、数据库帐户以及其他应用程序（FTP、Tomcat、phpMyAdmin 等）帐户是否存在弱口令。
>?帐户密码建议设置为大写、小写、特殊字符、数字组成的12 - 16位的复杂密码 ，也可使用密码生成器自动生成复杂密码。
 - 检查方法：根据实际情况自行确认。
 - 风险性：高。
2. 查看下服务器内是否有非系统和用户本身创建的账户。
 - 检查说明：一般黑客创建的异常账户账户名会在本地用户组显示出来。
 - 检查方法：打开 cmd 窗口，输入`lusrmgr.msc`命令，查看是否有新增的账号，如有管理员群组的（Administrators）里的新增账户，如有，请立即禁用或删除掉。
 - 风险性：高。
3. 检查是否存在隐藏账户名。
 - 检查说明：黑客为了逃避检查，往往会在您服务器内创建隐藏账户，隐藏账户在本地用户内是查看不到的。
 - 检查方法（您也可以通过下载 LP_Check 安全工具检查是否有隐藏账户）：
     1. 在桌面打开运行（可使用快捷键 Win + R），输入 regedit，即可打开注册表编辑器。
     1. 选择 HKEY_LOCAL_MACHINE/SAM/SAM，默认无法查看该选项内容，右键菜单选择权限，打开权限管理窗口。
     1. 选择当前用户（一般为 administrator），将权限勾选为完全控制，然后确定，关闭注册表编辑器。
     1. 再次打开注册表编辑器，即可选择 HKEY_LOCAL_MACHINE/SAM/SAM/Domains/Account/Users。
     1. 在 Names 项下可以看到实例所有用户名，如出现本地账户中没有的账户，即为隐藏账户，在确认为非系统用户的前提下，可删除此用户。    
 - 风险性：高。

### 二. 检查恶意进程和端口
1. 检查是否存在恶意进程在系统后台运行。
 - 检查说明：攻击者在入侵系统后，往往会运行恶意进程与外部进行通信，通过分析外联的进程，即可以找出入侵的控制进程。
 - 检查方法：
     1. 登录服务器，选择**开始** > **运行**。
     1. 输入 cmd，然后输入 netstat –nao 查看下服务器是否有未被授权的端口被监听。
     1. 打开任务管理器，检查对应的 PID 进程号所对应的进程是否为正常进程，例如通过 PID 号查看下运行文件的路径，删除对应路径文件，您也可以通过微软官方提供的 Process Explorer 工具进行排查。
 - 风险性：高。

### 三. 检查恶意程序及启动项
1. 检查服务器内部是否有异常的启动项。
 - 检查说明：攻击者在入侵系统后，往往会把恶意程序放到启动项中开机执行。
 - 检查方法：
     1. 登录服务器，选择**开始** > **所有程序**>**启动**。
     1. 默认情况下此目录在是一个空目录，确认是否有非业务程序在该目录下。
     1. 选择**开始** >**运行**，输入 msconfig，查看是否存在命名异常的启动项目，若存在则取消勾选命名异常的启动项目，并到命令中显示的路径删除文件。
     1. 选择**开始** > **运行**，输入 regedit，打开注册表，查看开机启动项是否正常，特别注意如下三个注册表项：
        HKEY_CURRENT_USER\software\micorsoft\windows\currentversion\run
        HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
        HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Runonce
    检查注册表右侧是否有启动异常的项目，如有请删除，并建议安装杀毒软件进行病毒查杀，清除残留病毒或木马。
 - 风险性：高。

1. 查看正在连接的会话。
 - 检查说明：检查服务器与网络上的其它服务器之间的会话或计划任务。
 - 检查方法：        
     1. 登录服务器，选择**开始** > **运行**。
     1. 输入 cmd，然后输入 netstat -ano，检查服务器与网络上的其它服务器之间的会话，并确认是否为正常连接。输入 schtasks，检查服务器中的计划任务，并确认是否为正常的计划任务。
 - 风险性：中。

### 四. 检查第三方软件漏洞
1. 如果您服务器内有运行对外应用软件（WWW、FTP 等），请您对软件进行配置，**限制应用程序的权限，禁止目录浏览或文件写权限**。
2. [开通腾讯云 Web 应用防火墙](https://console.cloud.tencent.com/guanjia/tea-overview) 防护，查看 Web 应用防护攻击日志。

## 常见问题
### 如何恢复被入侵后的网站或系统？
系统确认被入侵后，往往系统文件会被更改和替换，此时系统已经变得不可信，最好的方法就是重新安装系统，同时给新系统安装所有补丁。

### 如何防止网站或系统被再次入侵？
1. 改变所有系统账号的密码为 **复杂密码**（至少与入侵前不一致）。
2. **修改默认远程桌面端口**，操作如下：
 1. 选择**开始** > **运行**，然后输入 regedit。
 2. 打开注册表，进入如下路径： HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\Wds\rdpwd\Tds\tcp
 3. KEY_LOCAL_MACHINE\SYSTEM\CurrentContro1Set\Control\Tenninal Server\WinStations\RDP-Tcp
 4. 修改下右侧的 PortNamber 值。
3. 配置腾讯云安全组防火墙只允许 **指定 IP 才能访问远程桌面端口**。
4. **定期备份**重要业务数据和文件。
5. **定期更新**操作系统及应用程序组件版本（如 FTP、Struts2 等），防止被漏洞利用。
6. 安装**腾讯云主机安全 Agent** 和防病毒软件进行定期体检和扫描。
