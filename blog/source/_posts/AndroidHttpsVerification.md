---
title: Android Https双向验证
date: 2018-01-14 14:33:47
categories:
    - Android
tags:
    - Android
    - Https
---

对于网络安全，我们的重视程度远远及不上欧美等国家，部署Https的网站远远少于他们；但随着互联网不断普及，国民对数据安全的需求也越来越高，Https逐渐在国内普及开来。
最近公司项目也开始转为Https，故简单记录一下Android Https的一些设置。
<!--more-->

### 生成相关证书
1. 从后台那里拿到P12格式的证书文件，如：server.pfx;
2. 生成服务器cer证书

#### 安装openssl

我采用的是直接下载安装包，[下载地址](http://slproweb.com/products/Win32OpenSSL.html)；也可以选择下载源码自己编译openssl。

#### 生成证书server.cer
安装之后配置环境变量，用管理员权限打开或者使用win10的PowerShell，输入
```
openssl pkcs12 -in server.pfx -out server.cer
```
然后输入证书的密码和PEM密码，成功之后生成证书server.cer

#### 生成客户端信任证书库client.truststore
由于安卓端的证书类型必须是BKS类型，需要这样做：
1. 下载这个jar：[bcprov-ext-jdk15on-159.jar](http://www.bouncycastle.org/download/bcprov-ext-jdk15on-159.jar)
2. 将jar文件放在 Java 主目录下的 jre/lib/ext目录下
3. 修改jre/lib/security/java.security这个文件：在List of providers 注释的地方添加这一行`security.provider.11=org.bouncycastle.jce.provider.BouncyCastleProvider`
4. 然后重启终端，输入下面代码生成client.truststore，密码自己改：
```
keytool -import -v -alias server -file server.cer -keystore client.truststore -storepass 123456 -storetype BKS -provider org.bouncycastle.jce.provider.BouncyCastleProvider
```
如果不添加jar文件，会报`Class not found`错误。
但是，某些情况下（大概是证书的格式或来源不符合要求）还是会报错：**所输入的不是 X.509 证书**

#### 证书格式转换工具XCA
1. 下载安装：[地址](https://sourceforge.net/projects/xca/ )
2. 打开程序，点击：**File>New DataBase**新建库文件，选择保存位置，输入密码，即成功创建库。
3. 导入证书server.cer：点击：**Import>Certificate**，选择之前生成的证书server.cer，导入。
4. 导出p7b格式证书：
  ![导出p7b格式证书1](http://os21ow86u.bkt.clouddn.com/Snipaste_2018-01-14_16-51-05.png)

  选择p7b格式，点击ok导出xxxx.p7b的证书
  ![导出p7b格式证书1](http://os21ow86u.bkt.clouddn.com/Snipaste_2018-01-14_16-59-30.png)

5. 打开xxxx.p7b证书文件，找到证书，右键>所有任务>导出
  ![导出X.509格式证书](http://os21ow86u.bkt.clouddn.com/Snipaste_2018-01-14_17-07-06.png)

6. 导出X.509格式证书，选择之前server.cer文件覆盖保存，确认生成server.cer证书
  ![导出X.509格式证书](http://os21ow86u.bkt.clouddn.com/Snipaste_2018-01-14_17-11-38.png)

7.最后，重新执行命令，会提示`是否信任此证书? [否]:`，输入`是`生成client.truststore：
```
keytool -import -v -alias server -file server.cer -keystore client.truststore -storepass 123456 -storetype BKS -provider org.bouncycastle.jce.provider.BouncyCastleProvider
```

### Android端SSL认证请求
我们需要两个证书：
1. server.pfx：客户端证书，用于请求的时候给服务器来验证身份
2. client.truststore：客户端证书库，用于验证服务器端身份，防止钓鱼

#### SSLSocketFactory方式进行SSL认证

将两个证书添加到android项目的assets目标下面，建立SSL验证工具，代码如下：

```java
public class SSLHelper {
    private static final String KEY_STORE_TYPE_BKS = "bks";
    private static final String KEY_STORE_TYPE_P12 = "PKCS12";
    public static final String KEY_STORE_CLIENT_PATH = "server.pfx";//P12文件
    private static final String KEY_STORE_TRUST_PATH = "client.truststore";//truststore文件
    public static final String KEY_STORE_PASSWORD = "123456";//P12文件密码
    private static final String KEY_STORE_TRUST_PASSWORD = "123456";//truststore文件密码

    public static SSLSocketFactory getSSLSocketFactory(Context context) {
        SSLSocketFactory factory = null;

        try {
            // 服务器端需要验证的客户端证书
            KeyStore keyStore = KeyStore.getInstance(KEY_STORE_TYPE_P12);
            // 客户端信任的服务器端证书
            KeyStore trustStore = KeyStore.getInstance(KEY_STORE_TYPE_BKS);

            InputStream ksIn = context.getResources().getAssets()
              .open(KEY_STORE_CLIENT_PATH);
            InputStream tsIn = context.getResources().getAssets()
              .open(KEY_STORE_TRUST_PATH);
            try {
                keyStore.load(ksIn, KEY_STORE_PASSWORD.toCharArray());
                trustStore.load(tsIn, KEY_STORE_TRUST_PASSWORD.toCharArray());
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                try {
                    ksIn.close();
                } catch (Exception e) {
                    e.printStackTrace();
                }
                try {
                    tsIn.close();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            //信任管理器
            TrustManagerFactory trustManagerFactory = TrustManagerFactory.getInstance(
              TrustManagerFactory.getDefaultAlgorithm());
            trustManagerFactory.init(trustStore);
            //密钥管理器
            KeyManagerFactory keyManagerFactory = KeyManagerFactory.getInstance("X509");
            keyManagerFactory.init(keyStore, KEY_STORE_PASSWORD.toCharArray());
            //初始化SSLContext
            SSLContext sslContext = SSLContext.getInstance("TLS");
            sslContext.init(keyManagerFactory.getKeyManagers(), 
                            trustManagerFactory.getTrustManagers(), null);
            factory =  sslContext.getSocketFactory();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (KeyManagementException e) {
            e.printStackTrace();
        } catch (KeyStoreException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (UnrecoverableKeyException e) {
            e.printStackTrace();
        }

        return factory;
    }

}
```

#### OkHttpClient配置

```java
OkHttpClient client = new OkHttpClient.Builder()
  .sslSocketFactory(SSLHelper.getSSLSocketFactory(getApplication()))
```

#### 主机名验证

如果出现主机名验证错误`hostname error`，需要添加`hostnameVerifier`：

```java
OkHttpClient client = new OkHttpClient.Builder()
  .sslSocketFactory(SSLHelper.getSSLSocketFactory(getApplication()))
  .hostnameVerifier(new UnSafeHostnameVerifier());
```

```java
public class UnSafeHostnameVerifier implements HostnameVerifier {
    @Override
    public boolean verify(String hostname, SSLSession session) {
        return true;
    }
}
```

至此，Https双向验证的配置全部完成了。

本文基于笔者项目实践，关于SSL认证笔者还没有了解透彻，如有错误，请多多指出。



### 参考

[Android HTTPS SSL双向验证](http://frank-zhu.github.io/android/2017/03/30/android-https-ssl-part-02/)

[ keytool 错误: java.lang.Exception: 所输入的不是 X.509 证书](http://blog.csdn.net/zouchengxufei/article/details/51671330)