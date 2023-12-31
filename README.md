# 适用于amd64（x86_64）平台lede的frp
 
从[lede仓库](https://github.com/coolsnowwolf/packages/tree/master/net/frp)拉取的代码，除了改版本号为0.51.3，未作其他修改

如果你是非amd64（x86_64）平台，请自行修改`FRP_URL`中与架构相关的部分，将`sdk_url`改为对应架构的sdk地址。

**此包只提供一个安装到/usr/bin/下的`frps`或`frpc`，界面可以用[我的](https://github.com/yhl452493373/luci-app-frpc)，也可以用lede官方的**

## LEDE SDK下编译
```bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
FRP_URL=$( curl -sL https://api.github.com/repos/fatedier/frp/releases | grep -P 'download/v[\d.]+/frp_[\d.]+_linux_amd64.tar.gz' | awk -F '"' '{print $4}' | awk 'NR==1{print}' )
FRP_VERSION=$( echo $FRP_URL | awk -F '/' '{print $8}' | awk '{gsub(/v/,"");print $1}' )
FRP_HASH=$( curl -sL https://codeload.github.com/fatedier/frp/tar.gz/v$FRP_VERSION | sha256sum | awk -F ' ' '{print $1}' )
sdk_url=https://archive.openwrt.org/releases/21.02.6/targets/x86/64/openwrt-sdk-21.02.6-x86-64_gcc-8.4.0_musl.Linux-x86_64.tar.xz
golang_commit=b6468a6bd5b61d811ae2567a9814aed44354e555
golang_url=https://codeload.github.com/coolsnowwolf/packages/tar.gz/

rm -rf sdk && mkdir sdk && curl "$sdk_url" | tar -xJ -C ./sdk --strip-components=1
cd sdk
./scripts/feeds update -a

rm -rf feeds/packages/lang/golang
curl "$golang_url$golang_commit" | tar -xz -C "feeds/packages/lang" --strip=2 "packages-$golang_commit/lang/golang"

rm -rf feeds/packages/net/frp
git clone https://github.com/yhl452493373/openwrt-frp.git feeds/packages/net/frp
sed -i -e 's/^PKG_VERSION.*/PKG_VERSION:='''$FRP_VERSION'''/' feeds/packages/net/frp/Makefile
sed -i -e 's/^PKG_HASH.*/PKG_HASH:='''$FRP_HASH'''/' feeds/packages/net/frp/Makefile

./scripts/feeds install -a
make defconfig
make package/frp/clean
make package/frp/compile V=s
```
以上编译时，会拉取最新的frp代码。编译后，在`bin/packages/平台架构/packages`下。amd64（x86_64）在 `bin/packages/x86_64/packages` 下
