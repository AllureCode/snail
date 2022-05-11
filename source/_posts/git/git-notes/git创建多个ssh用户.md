# git创建多个ssh用户

## 起因

> 我们在工作中，可能在公司使用的是公司内部的gitlab或gitee等版本管理工具，但是，在闲暇之余，我们可能也有自己的gothub账户。为了使用ssh进行无认证操作，因此我们需要创建多个ssh用户

## 实践

>1.通过ssh-keygen -t rsa -C 'your github account@qq.com' -f ~/.ssh/github_id_rsa
>
>2.执行上诉命令之后，会在本地 ~/.ssh 下面生成两个新的ssh信息 我们只需将生成的github_id_rsa.pub复制到github ssh设置的地方即可。
>
>注意：复制的时候使用cat ~/.ssh/github_id_rsa.pub  然后复制即可。 不然 github可能会出现 ssh key invalid
>
>3.完成2之后，在本地执行 touch ~/.ssh/config  创建一个空的config文件
>
>4.在config文件中写入
>
>```
>   #gitlab
>       Host gitlab.com
>       HostName gitlab.com
>       PreferredAuthentications publickey
>       IdentityFile ~/.ssh/id_rsa
>                                                                               
>   # github
>       Host github.com
>       HostName github.com
>       PreferredAuthentications publickey
>       IdentityFile ~/.ssh/github_id_rsa
>```
>
>5.测试
>
>使用 ssh -T git@github.com  输入 yes回车即可。

