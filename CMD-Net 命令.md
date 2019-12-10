# CMD-Net 命令

## Net User
作用：添加或更改用户帐号或显示用户帐号信息。  
命令格式:  
    Net user  
    [ username [ password | * ] [options] ] [/domain] 

有关参数说明：
- 键入不带参数的Net user查看计算机上的用户帐号列表
- username添加、删除、更改或查看用户帐号名
- password为用户帐号分配或更改密码
- 提示输入密码
- /domain在计算机主域的主域控制器中执行操作。该参数仅在 Windows NT Server 域成员的 Windows NT Workstation 计算机上可用。默认情况下，Windows NT Server 计算机在主域控制器中执行操作。注意：在计算机主域的主域控制器发生该动作。它可能不是登录域。
　　例如：Net user ghq123查看用户GHQ123的信息。