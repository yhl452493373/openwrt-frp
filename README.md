# openwrt-frp
 
从[lede仓库](https://github.com/coolsnowwolf/packages/tree/master/net/frp)拉取

## 以下为在LEDE中的编译方式

### 1、删除原有代码
```bash
rm -rf feeds/packages/net/frp
```

### 2、拉取新代码
```bash
git clone https://github.com/yhl452493373/openwrt-frp.git feeds/packages/net/frp
```

### 3、更新到frp最新版源码
```bash
FRP_URL=$( curl -sL https://api.github.com/repos/fatedier/frp/releases | grep -P 'download/v[\d.]+/frp_[\d.]+_linux_amd64.tar.gz' | awk -F '"' '{print $4}' | awk 'NR==1{print}' )
FRP_VERSION=$( echo $FRP_URL | awk -F '/' '{print $8}' | awk '{gsub(/v/,"");print $1}' )
FRP_HASH=$( curl -sL https://codeload.github.com/fatedier/frp/tar.gz/v0.51.3 | sha256sum | awk -F ' ' '{print $1}' )
sed -i -e 's/^PKG_VERSION.*/PKG_VERSION:='''$FRP_VERSION'''/' feeds/packages/net/frp/Makefile
sed -i -e 's/^PKG_HASH.*/PKG_HASH:='''$FRP_HASH'''/' feeds/packages/net/frp/Makefile
```

### 4、执行`make menuconfig`，并在`Network`-`Web Servers/Proxies`选中`frpc`，然后编译
