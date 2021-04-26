# windows_Incident_Response
1 准备工作
    
    1.1 任务计划检查如果报错需要修改chcp
        1.1.1 输入chcp记录原本的代码
        1.1.2 然后输入chcp 437或者chcp 65001
        1.1.3 检查完毕后修改chcp为原本的代码
    1.2 用户检查报错
	      1.2.1 需要修改计算机\HKEY_LOCAL_MACHINE\SAM\SAM的权限，勾选administrators组的读取权限


2 自动化获取现象、持久化机制、痕迹

    2.1 创建检查结果文件夹，修改保存路径【E:\20210101】。
    2.2 用管理员打开cmd复制以下内容并回车，结尾多复制一个空行。
	netstat -ano | findstr LIST > E:\20210101\1-listening.log
	netstat -ano | findstr EST > E:\20210101\2-established.log
	netstat -ano | findstr SYN > E:\20210101\3-synSent.log
	netstat -ano | findstr WAIT > E:\20210101\4-timeWait.log
	wmic process get caption,commandline,creationDate,executablepath,handle,ParentProcessId > E:\20210101\5-process.log

	schtasks /query /fo LIST /v > E:\20210101\6-cron.log
	reg export HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run E:\20210101\7-1autoRun.log
	reg query "HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce" >> E:\20210101\7-2autoRun.log
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" >> E:\20210101\7-3autoRun.log
	set > E:\20210101\8-set.log
	reg query "HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services" /s > E:\20210101\9-service_001.log
	reg query HKEY_CURRENT_USER\Environment /v UserInitMprLogonScript > E:\20210101\10-userLogin.log
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Userinit >> E:\20210101\10-userLogin.log
	reg export HKEY_LOCAL_MACHINE\SAM\SAM\Domains\Account\Users E:\20210101\11-user.log
	net user >> E:\20210101\11-user.log
	net localgroup administrators >> E:\20210101\11-user.log
	copy %SystemRoot%\System32\Winevt\Logs\Security.evtx E:\20210101\12-security.evtx
	copy %SystemRoot%\System32\Winevt\Logs\System.evtx E:\20210101\13-system.evtx 


3 手动获取痕迹

    3.1 可疑文件，包括但不限于，查询完成后截图
		    使用everything，搜索指定日期下的新增文件
		    dm:		选择可执行文件（查询后改为【所有】）
		    dm:		path: 			.jsp或者.php或者.asp
		    dm:		path:
		    dm:															#如有异常文件，根据异常文件的时间进行排序，查看该时间下有无其他新增
