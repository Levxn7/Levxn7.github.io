---
title: 关于8月13日Github改版,hexo受到影响的总结
date: 2021-08-20 10:31:18
categories: 
- 高级前端笔记
tags: 
- 其他
---

关于8月13日Github改版,hexo受到影响的总结

众所周知,2021年8月13日Github的改版影响了不少项目,Github以密码不安全为由建议用户改用秘钥.

当时的提交报错为:
```js
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.
```
这个问题其实很好解决,网上有很多人分享了解决过程,解决完上述报错,一般还会引起一系列报错,国内网络上都有很多人分享解决办法,这里不多赘述.
参考<https://www.codepeople.cn/2021/09/02/github-problem/>

但是修改之后hexo编译后也是犹豫8.13的问题同步不到github.io上面去,怎么办呢

```js
ssh -T git@github.com//验证key 如果验证失败执行下列命令
ssh-keygen -t rsa -C "xxx@qq.com"//一路回车即可
ssh -v git@github.com
//　　最后两句会出现：
//　　No more authentication methods to try.
//　　Permission denied (publickey).
//　　这时候再输入
ssh-agent -s
/*然后会提示类似的信息：　　
SSH_AUTH_SOCK=/tmp/ssh-GTpABX1a05qH/agent.404; export SSH_AUTH_SOCK;  　　
SSH_AGENT_PID=13144; export SSH_AGENT_PID;  　　
echo Agent pid 13144;
*/
ssh-add ~/.ssh/id_rsa
/*这时候应该会提示：　　Identity added: ...（这里是一些ssh key文件路径的信息）　　
（注意）如果出现错误提示：　　Could not open a connection to your authentication agent.　　
请执行命令：eval `ssh-agent -s`后继续执行命令 ssh-add ~/.ssh/id_rsa，这时候一般没问题啦。
将里面的内容复制，进入你的github账号，在settings下，SSH and GPG keys下new SSH key，
title随便取一个名字，然后将id_rsa.pub里的内容复制到Key中，完成后Add SSH Key。
*/
ssh -T git@github.com
/*提示：Hi xxx! You've successfully authenticated, but GitHub does not provide shell access.
　　这时候你的问题就解决啦
 */
```