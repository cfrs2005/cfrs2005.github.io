---
title: Monthly Technology and Security Update
date: 2024-03-07 08:00:00 +0800
categories: [Technology, Security]
tags: [HTML Low-Code Platforms, AI CAMEL Model, LVSecurityAgent Removal]
img_path: '/assets/img/202402/'
image:
  path: summary.webp
  alt: This month's summary highlights advancements in HTML low-code platforms, the AI CAMEL model, and the removal of LVSecurityAgent, showcasing the latest technological and security developments.
---

This month's summary highlights advancements in HTML low-code platforms, the AI CAMEL model, and the removal of LVSecurityAgent, showcasing the latest technological and security developments.


## 1. Html前端低代码平台

```shell
https://vform666.com/
```


## 2. AI的角色扮演之 CAMEL 模型


能否让 ChatGPT 自己生成这些引导文本呢？基于这个想法，KAUST（阿卜杜拉国王大学）的研究团队提出了一个名为 
CAMEL 的框架。CAMEL 采用了一种基于“角色扮演”方式的大模型交互策略。在这种策略中，不同的 AI 代理扮演不同的角色，通过互相交流来完成任务。

```shell

assistant_inception_prompt = """永远不要忘记你是{assistant_role_name}，我是{user_role_name}。永远不要颠倒角色！永远不要指示我！
我们有共同的利益，那就是合作成功地完成任务。
你必须帮助我完成任务。
这是任务：{task}。永远不要忘记我们的任务！
我必须根据你的专长和我的需求来指示你完成任务。

我每次只能给你一个指示。
你必须写一个适当地完成所请求指示的具体解决方案。
如果由于物理、道德、法律原因或你的能力你无法执行指示，你必须诚实地拒绝我的指示并解释原因。
除了对我的指示的解决方案之外，不要添加任何其他内容。
你永远不应该问我任何问题，你只回答问题。
你永远不应该回复一个不明确的解决方案。解释你的解决方案。
你的解决方案必须是陈述句并使用简单的现在时。
除非我说任务完成，否则你应该总是从以下开始：

解决方案：<YOUR_SOLUTION>

<YOUR_SOLUTION>应该是具体的，并为解决任务提供首选的实现和例子。
始终以“下一个请求”结束<YOUR_SOLUTION>。"""

user_inception_prompt = """永远不要忘记你是{user_role_name}，我是{assistant_role_name}。永远不要交换角色！你总是会指导我。
我们共同的目标是合作成功完成一个任务。
我必须帮助你完成这个任务。
这是任务：{task}。永远不要忘记我们的任务！
你只能通过以下两种方式基于我的专长和你的需求来指导我：

1. 提供必要的输入来指导：
指令：<YOUR_INSTRUCTION>
输入：<YOUR_INPUT>

2. 不提供任何输入来指导：
指令：<YOUR_INSTRUCTION>
输入：无

“指令”描述了一个任务或问题。与其配对的“输入”为请求的“指令”提供了进一步的背景或信息。

你必须一次给我一个指令。
我必须写一个适当地完成请求指令的回复。
如果由于物理、道德、法律原因或我的能力而无法执行你的指令，我必须诚实地拒绝你的指令并解释原因。
你应该指导我，而不是问我问题。
现在你必须开始按照上述两种方式指导我。
除了你的指令和可选的相应输入之外，不要添加任何其他内容！
继续给我指令和必要的输入，直到你认为任务已经完成。
当任务完成时，你只需回复一个单词<CAMEL_TASK_DONE>。
除非我的回答已经解决了你的任务，否则永远不要说<CAMEL_TASK_DONE>。"""


```


## 3. LVSecurityAgent 关停MAC


```shell

# 删除

echo 'delete shit.app..need your root pwd';
sudo rm -rf /Applications/LVSecurityAgent.app;
sudo rm -rf /Applications/dvc-manageproxy-exe.app
echo 'script is fighting...';
# 这流氓软件会把该文件夹锁定，无法直接rm删除，所以要先改变文件夹属性解锁
sudo chflags noschg /opt/LVUAAgentInstBaseRoot; 
echo 'delete shit.datafile..';
sudo rm -rf /opt/LVUAAgentInstBaseRoot;
sudo rm -rf /Library/LaunchAgents/com.lvmagent.gui.plist
sudo rm -rf /Library/LaunchAgents/com.leagsoft.uniremote.plist
sudo rm -rf /Library/LaunchDaemons/com.lvmagent.core.plist
sudo rm -rf /Library/LaunchDaemons/com.lvmagent.filemonitor.plist
sudo rm -rf /Library/LaunchAgents/com.lvmagent.screen.plist

echo 'kill shit.process..';
sudo ps -ef|grep -E 'LVUAAgentInstBaseRoot|dvc-manageproxy-exe' |grep -v "grep"|awk '{print $2}'|xargs sudo kill -9;
echo 'congratulations! You throw that shit!';



# 禁用保活

# Vim分别执行每个文件，修改里面的内容（就是一个一个的改）
# 下面的如果找不到文件请在用户目录下查看也就是 ~/Library/
sudo vim /Library/LaunchAgents/com.lvmagent.gui.plist

sudo vim /Library/LaunchAgents/com.leagsoft.uniremote.plist

sudo vim /Library/LaunchDaemons/com.lvmagent.core.plist

sudo vim /Library/LaunchDaemons/com.lvmagent.filemonitor.plist

sudo vim /Library/LaunchAgents/com.lvmagent.manageproxy.plist

sudo vim /Library/LaunchAgents/com.lvmagent.screen.plist

# 启动核心

sudo /opt/LVUAAgentInstBaseRoot/dvc-core-exe 

```


## 4. Linux 机器重启时间查看

```shell
cd /var/log
/var/log# grep -a Booting kern.*
kern.log:Feb 19 14:37:38 gaussian kernel: [    0.000000] Booting Linux on physical CPU 0x0
kern.log:Feb 19 14:53:57 gaussian kernel: [    0.000000] Booting Linux on physical CPU 0x0
kern.log:Feb 19 18:01:26 gaussian kernel: [    0.000000] Booting Linux on physical CPU 0x0
kern.log.1:Feb  4 00:11:13 gaussian kernel: [    0.000000] Booting Linux on physical CPU 0x0
kern.log.1:Feb  4 00:16:54 gaussian kernel: [    0.000000] Booting Linux on physical CPU 0x0
kern.log.1:Feb  4 00:22:35 gaussian kernel: [    0.000000] Booting Linux on physical CPU 0x0
```


## 5. homebrew 安装disable的版本

```shell
HOMEBREW_NO_INSTALL_FROM_API=1 brew install go@1.18
```
