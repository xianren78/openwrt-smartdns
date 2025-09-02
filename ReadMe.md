# openwrt-smartdns actions编译

1, 根据[smartdns](https://github.com/pymumu/smartdns)、[webui](https://github.com/pymumu/smartdns-webui) 和[luci-app-smartdns](https://github.com/pymumu/luci-app-smartdns)的最新 commit 使用 **openssl 3.5** 进行 **nightly** 编译

2, 使用 **OpenWrt 24.10 SDK**和 **SNAPSHOT SDK** 编译，支持**X86-64**和**aarch64_generic**两种架构，其他版本OP请自行测试

3, 使用 OpenWrt sdk docker外置openssl进行编译，编译时长在20分钟以内；如果用原生openssl编译，时长约30分钟；如不使用docker方式，直接sdk下编译，由于需要编译rust时长50分钟。

4, 对Makefile的修改主要是 OPENSSL库外置还是内置，是否在线编译RUST，另外SNAPSHOT版对Hash match要求严格，不能skip。

5, SNAPSHOT版证书问题，[看看这儿](https://github.com/pymumu/smartdns/discussions/2081#discussioncomment-14199079)

6, actions绝大部分都是AI糊的，如有需要自行取用。

以下是LUCI最新特性展示， LUCI也支持使用 PikuZheng 大佬编译的[smartdns_with_ui（tag名有with_ui字样）](https://github.com/PikuZheng/smartdns/releases)：

![Luci支持WEBUI](https://github.com/user-attachments/assets/ef47d004-3b6f-4338-a08e-bf2be95c8ad5)

![支持QUIC等协议](https://github.com/user-attachments/assets/6df1019d-2771-437d-a2cd-cf2032eb0abb)

![支持查看日志](https://github.com/user-attachments/assets/7b6ed9b9-dd66-4ff8-b252-6e8a430ac954)


# 以下是原仓库说明，支持OpenWrt下集成编译

原仓库为smartdns独立仓库，为单独编译使用，可配合luci-app-smartdns一起使用。  
luci界面：[luci-app-smartdns](https://github.com/pymumu/luci-app-smartdns)

## 使用方式

注意：如下命令操作路径为openwrt源代码所在目录。  

### 复制仓库中的文件到如下目录，并执行安装

```shell
./feeds/packages/net/smartdns/
./scripts/feeds install package -a
```

### 执行openwrt配置, 选中smartdns

执行编译配置：

```shell
make menuconfig
```

* 选择路径：

 Network > smartdns  
 Network > smartdns-ui  

* 编译模式：

1. 若编译独立软件包，选择编译模式为`M`
1. 若编译到固件中，选择编译模式为`*`
1. UI为单独安装包，需要选择后才能编译。

### 执行openwrt编译

仅编译软件包：

```shell
make package/feeds/packages/smartdns/compile
```

编译固件以及软件包。

```shell
make -j8
```

## 懒人脚本

也可可执行如下命令，一次性下载smartdns。

下列命令可采用复制粘贴的方式执行， 注意目录需要在openwrt源代码目录中。

```shell
WORKINGDIR="`pwd`/feeds/packages/net/smartdns"
mkdir $WORKINGDIR -p
rm $WORKINGDIR/* -fr
wget https://github.com/pymumu/openwrt-smartdns/archive/master.zip -O $WORKINGDIR/master.zip
unzip $WORKINGDIR/master.zip -d $WORKINGDIR
mv $WORKINGDIR/openwrt-smartdns-master/* $WORKINGDIR/
rmdir $WORKINGDIR/openwrt-smartdns-master
rm $WORKINGDIR/master.zip

./scripts/feeds install -a
make menuconfig

```

上述命令完成后，可执行编译。

