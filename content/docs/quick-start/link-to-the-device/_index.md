---
weight: 100
title: "连接到超算云平台"
---

# 连接到超算云平台

{{< variables vars="hpc-user,ssh-host,local-home" >}}

当然我们说的是 SSH 。应该没有人会用那个网页吧？也不对，应该是没有人不会用。

## 第一步 确认你有 SSH

随便打开一个终端，无论是 bash 、 cmd 、 PowerShell 还是什么乱七八糟。输入 `ssh` 按下回车。正常情况下，会跳出来这么一串东西：

```text
usage: ssh [-46AaCfGgKkMNnqsTtVvXxYy] [-B bind_interface] [-b bind_address]
           [-c cipher_spec] [-D [bind_address:]port] [-E log_file]
           [-e escape_char] [-F configfile] [-I pkcs11] [-i identity_file]
           [-J destination] [-L address] [-l login_name] [-m mac_spec]
           [-O ctl_cmd] [-o option] [-P tag] [-p port] [-Q query_option]
           [-R address] [-S ctl_path] [-W host:port] [-w local_tun[:remote_tun]]
           destination [command [argument ...]]
```

> [!NOTE]
> 什么叫没有 SSH ？我不信。

## 第二步 在校园网内尝试连接

接下来保持你位于校园网内，输入：

```sh
ssh @@hpc-user@@@logini.tongji.edu.cn -p 10022
```

> [!IMPORTANT]
> 这里 @@hpc-user@@ 是用户名，如果你不是这位老师的学生，请用那个正确的用户名。你可以在页面最顶端修改这里显示的内容。

@@hpc-user@@

然后输入密码。我相信你肯定连上了。

> [!NOTE]
> 什么叫输入密码没反应？密码是不会显示出来的。你就蒙眼按键盘，输完按回车。

> [!TIP]
> 如果连不上，可以试着检查：
> 
> 1. 在校园网吗？
> 2. 密码输对了吗？
> 3. 错误信息百度一下

## 第三步 谁记得住命令啊？使用 SSH 配置文件

上面这个命令还是太麻烦了，在你的电脑上找到这个文件 SSH 的配置文件（ Linux 通常是 `~/.ssh/config` ， Windows 通常是 `%USERPROFILE%/.ssh/config` ，如果没有可以自己创建一下，就是个文本文件）。把它当文本文件打开，然后把这团东西塞进去：

```text
Host hpc
    HostName logini.tongji.edu.cn
    User u13070
    Port 10022
```

保存它。

回到我们的终端，输入 `ssh hpc` 。

> [!TIP]
> 这里说的是你电脑的终端。刚刚那个终端已经连到超算平台了，如果想继续用刚刚那个终端，可以先 `exit` 退出。

## 第四步 谁记得住密码啊？使用密钥登录

如果每次连接都要输入那一串随机生成的、毫无逻辑的密码，你迟早被烦死。

> [!NOTE]
> 什么叫密码是 `123456` ？等着被盗号吧就。

在你的电脑上使用 `ssh-keygen` 生成一对密钥。我把它放在 `C:\Users\yueyi\.ssh\id_for_hpc` ，并且不设置 passphrase 。这是我操作时的记录：

```text
Generating public/private ed25519 key pair.
Enter file in which to save the key (C:\Users\yueyi/.ssh/id_ed25519): C:\Users\yueyi\.ssh\id_for_hpc
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in id_for_hpc
Your public key has been saved in id_for_hpc.pub
The key fingerprint is:
SHA256:ckHz/3YSA77qliwTUuCQ23VCByGZd99kFnmiiOXpV3A yueyi@DESKTOP-VE9QGI5
The key's randomart image is:
+--[ED25519 256]--+
|     ..+*o.   .o |
|    o +o+++ . E .|
|     = +.B.+.O o |
|    . o o.+oo.o  |
|      ..S.  o.o  |
|      .o. . .o o |
|       . o o. + .|
|        o +. . o |
|         =o      |
+----[SHA256]-----+
```

> [!IMPORTANT]
> 这里 `yueyi` 是我的用户名，你应该是找不到这个目录的。

这里我们设置了密钥的位置为 `C:\Users\yueyi\.ssh\id_for_hpc` ，但是实际进去将会看到两个文件：一个叫 `id_for_hpc` ，一个叫 `id_for_hpc.pub` 。

> [!CAUTION]
> 其中不带 `.pub` 的是私钥。请保管好它，不要泄露。如果你使用的设备不安全，或者希望在未来通过任何方式传递私钥，在先前生成时应当为它设置一个 passphrase （当然一般不需要，换设备重新生成一个就是了）。现在删掉它重新再生成一个也来得及。

现在要做的事情是，打开 `id_for_hpc.pub` ，会看到类似于这样的内容：

```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBwLLOJbq3byqJ8/KREL+93wzIUjpXQ75SUTTXRdE4BH yueyi@DESKTOP-VE9QGI5
```

把它复制下来。

然后登录超算平台：

```sh
ssh hpc
```

进入之后输入

```sh
echo "" >> ~/.ssh/authorized_keys    # 确保前面有个换行
echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBwLLOJbq3byqJ8/KREL+93wzIUjpXQ75SUTTXRdE4BH yueyi@DESKTOP-VE9QGI5" >> ~/.ssh/authorized_keys
```

> [!IMPORTANT]
> 这里密钥换成你自己的，不要粘我的。

接下来在你的电脑上，还是那个 SSH 配置文件，改成这样：

```text
Host hpc
    HostName logini.tongji.edu.cn
    User u13070
    Port 10022
    IdentityFile C:\Users\yueyi\.ssh\id_for_hpc
```

> [!IMPORTANT]
> 这里 `IdentityFile` 写刚刚创建的密钥的位置，不要粘我的。

现在重新尝试连接，直接输入

```powershell
ssh hpc
```

我猜这次不用输入密码就能进去了。