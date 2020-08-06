---
title: fabric-samples-fabcar-go示例的问题及解决方案
date: 2020-08-06 17:26:33
tags: [fabric]
categories: 编程技术
---

## 1. 启动Fabric测试网络

前提是已经拉取了Fabric的镜像文件。

然后拉取fabric-samples工程，并启动测试网络。

```
$ git clone https://github.com/hyperledger/fabric-samples.git
$ cd fabric-samples/fabcar
$ ./startFabric.sh
```

## 2. 执行Go版本的SDK测试程序时出错

```
$ cd go
$ ./runfabcar.sh

ENV_DAL:
DISCOVERY_AS_LOCALHOST=true
run fabcar...
 [fabsdk/core] 2020/08/06 06:06:43 UTC - cryptosuite.GetDefault -> INFO No default cryptosuite found, using default SW implementation
[{"Key":"CAR0","Record":{"make":"Toyota","model":"Prius","colour":"blue","owner":"Tomoko"}},{"Key":"CAR1","Record":{"make":"Ford","model":"Mustang","colour":"red","owner":"Brad"}},{"Key":"CAR10","Record":{"make":"VW","model":"Polo","colour":"Grey","owner":"Archie"}},{"Key":"CAR2","Record":{"make":"Hyundai","model":"Tucson","colour":"green","owner":"Jin Soo"}},{"Key":"CAR3","Record":{"make":"Volkswagen","model":"Passat","colour":"yellow","owner":"Max"}},{"Key":"CAR4","Record":{"make":"Tesla","model":"S","colour":"black","owner":"Adriana"}},{"Key":"CAR5","Record":{"make":"Peugeot","model":"205","colour":"purple","owner":"Michel"}},{"Key":"CAR6","Record":{"make":"Chery","model":"S22L","colour":"white","owner":"Aarav"}},{"Key":"CAR7","Record":{"make":"Fiat","model":"Punto","colour":"violet","owner":"Pari"}},{"Key":"CAR8","Record":{"make":"Tata","model":"Nano","colour":"indigo","owner":"Valeria"}},{"Key":"CAR9","Record":{"make":"Holden","model":"Barina","colour":"brown","owner":"Shotaro"}}]
Failed to submit transaction: Failed to submit: CreateAndSendTransaction failed: SendTransaction failed: orderers is nil
exit status 1
```

查询智能合约成功，写入智能合约时失败。错误信息如下：
`Failed to submit transaction: Failed to submit: CreateAndSendTransaction failed: SendTransaction failed: orderers is nil`

## 3. 执行Java版本的SDK测试程序成功

```
$ cd ../java
$ mvn test

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running org.example.ClientTest
log4j:WARN No appenders could be found for logger (org.hyperledger.fabric_ca.sdk.helper.Config).
log4j:WARN Please initialize the log4j system properly.
log4j:WARN See http://logging.apache.org/log4j/1.2/faq.html#noconfig for more info.
An identity for the admin user "admin" already exists in the wallet
An identity for the user "appUser" already exists in the wallet
[{"Key":"CAR0","Record":{"make":"Toyota","model":"Prius","colour":"blue","owner":"Tomoko"}},{"Key":"CAR1","Record":{"make":"Ford","model":"Mustang","colour":"red","owner":"Brad"}},{"Key":"CAR10","Record":{"make":"VW","model":"Polo","colour":"Grey","owner":"Archie"}},{"Key":"CAR2","Record":{"make":"Hyundai","model":"Tucson","colour":"green","owner":"Jin Soo"}},{"Key":"CAR3","Record":{"make":"Volkswagen","model":"Passat","colour":"yellow","owner":"Max"}},{"Key":"CAR4","Record":{"make":"Tesla","model":"S","colour":"black","owner":"Adriana"}},{"Key":"CAR5","Record":{"make":"Peugeot","model":"205","colour":"purple","owner":"Michel"}},{"Key":"CAR6","Record":{"make":"Chery","model":"S22L","colour":"white","owner":"Aarav"}},{"Key":"CAR7","Record":{"make":"Fiat","model":"Punto","colour":"violet","owner":"Pari"}},{"Key":"CAR8","Record":{"make":"Tata","model":"Nano","colour":"indigo","owner":"Valeria"}},{"Key":"CAR9","Record":{"make":"Holden","model":"Barina","colour":"brown","owner":"Shotaro"}}]
{"make":"VW","model":"Polo","colour":"Grey","owner":"Mary"}
{"make":"VW","model":"Polo","colour":"Grey","owner":"Archie"}
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 13.282 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
```

查询和写入智能合约都是成功的。

## 4. 比较Go版本和Java版本SDK测试程序的区别

看了一下两种版本的测试程序，使用的是同一个连接配置文件：

`fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.yaml`

但Java版本的没问题，Go版本就出现上面的错误。让人郁闷呐！

## 5. 网上寻求解决方案

使用关键词`fabcar orderers is nil`在谷歌中查找。

谷歌真是最爱，第一条就是我需要的内容。

[https://stackoverflow.com/questions/62927393/hyperledger-2-2-fabcar-transaction-issue](https://stackoverflow.com/questions/62927393/hyperledger-2-2-fabcar-transaction-issue)

`stackoverflow`这个网站真的很赞，在上面总能找到各种各样的技术问题和答案。这次也没有让我失望。

浏览了一下题主的问题，跟我遇到的情况一模一样，欧耶~！

可是这个问题下面就一个人回答，也不对题。还是题主自己搞定了问题，他在问题的后面附加了一段信息：

```
It worked, the solution is add channel into connection-org1.yaml:

channels:
  mychannel:
    orderers:
      - orderer.example.com
    peers:
      peer0.org1.example.com:
      endorsingPeer: true
      chaincodeQuery: true
      ledgerQuery: true
      eventSource: true
 ```
 
 
有解决方案就OK！感谢`Nguyen Tien Huy`这哥们。

## 6. 试验解决方案

我把题主追加的配置添加到我的`connection-org1.yaml`文件中，然后运行程序，居然...居然...还是出错了。

```
$./runfabcar.sh

ENV_DAL:
DISCOVERY_AS_LOCALHOST=true
run fabcar...
 [fabsdk/core] 2020/08/06 07:32:34 UTC - cryptosuite.GetDefault -> INFO No default cryptosuite found, using default SW implementation
Failed to connect to gateway: Failed to apply config option: failed to initialize configuration: unable to load endpoint config: failed to initialize endpoint config from config backend: network configuration load failed: failed to load channel configs: failed to load channel orderers: Could not find Orderer Config for channel orderer [orderer.example.com]
exit status 1
```

关键是这句：
`Could not find Orderer Config for channel orderer [orderer.example.com]`

说明找不到orderer.example.com的连接信息。我观察了一下`connection-org1.yaml`文件的结构，参照peer的配置增加了一段针对orderer的配置信息：

```
orderers:
  orderer.example.com:
    url: grpcs://211.88.25.149:7050
    tlsCACerts:
      pem: |
          -----BEGIN CERTIFICATE-----
          MIICCjCCAbGgAwIBAgIUFQmU8j1hCSJmdSoLEa/6BtfzrOAwCgYIKoZIzj0EAwIw
          YjELMAkGA1UEBhMCVVMxETAPBgNVBAgTCE5ldyBZb3JrMREwDwYDVQQHEwhOZXcg
          WW9yazEUMBIGA1UEChMLZXhhbXBsZS5jb20xFzAVBgNVBAMTDmNhLmV4YW1wbGUu
          Y29tMB4XDTIwMDgwNTAzMDcwMFoXDTM1MDgwMjAzMDcwMFowYjELMAkGA1UEBhMC
          VVMxETAPBgNVBAgTCE5ldyBZb3JrMREwDwYDVQQHEwhOZXcgWW9yazEUMBIGA1UE
          ChMLZXhhbXBsZS5jb20xFzAVBgNVBAMTDmNhLmV4YW1wbGUuY29tMFkwEwYHKoZI
          zj0CAQYIKoZIzj0DAQcDQgAETFPVGXkshRigcy4ghTLuooUZ3XsOz2S6DBS56Zm6
          VtBczejebMmkFN1+w2LMdIXjSmADgUHujmgZIQSwcLM6U6NFMEMwDgYDVR0PAQH/
          BAQDAgEGMBIGA1UdEwEB/wQIMAYBAf8CAQEwHQYDVR0OBBYEFMOa2JjrXTCBHAE8
          BRUZ8PSeqEjIMAoGCCqGSM49BAMCA0cAMEQCIFNo8kBg664OZHXP8KctX/oLwWPq
          9H+yTvryIAlLXN+KAiBGa+lWjXfA924x0CEyWLjnxe8IZnNG+gKV3kpQF/A9KQ==
          -----END CERTIFICATE-----

    grpcOptions:
      ssl-target-name-override: orderer.example.com
      hostnameOverride: orderer.example.com
```

再次运行测试程序。YES！成功了！

```
$ ./runfabcar.sh

ENV_DAL:
DISCOVERY_AS_LOCALHOST=true
run fabcar...
 [fabsdk/core] 2020/08/06 07:38:09 UTC - cryptosuite.GetDefault -> INFO No default cryptosuite found, using default SW implementation
 [fabsdk/fab] 2020/08/06 07:38:09 UTC - fab.detectDeprecatedNetworkConfig -> WARN Getting orderers from endpoint config channels.orderer is deprecated, use entity matchers to override orderer configuration
 [fabsdk/fab] 2020/08/06 07:38:09 UTC - fab.detectDeprecatedNetworkConfig -> WARN visit https://github.com/hyperledger/fabric-sdk-go/blob/master/test/fixtures/config/overrides/local_entity_matchers.yaml for samples
[{"Key":"CAR0","Record":{"make":"Toyota","model":"Prius","colour":"blue","owner":"Tomoko"}},{"Key":"CAR1","Record":{"make":"Ford","model":"Mustang","colour":"red","owner":"Brad"}},{"Key":"CAR10","Record":{"make":"VW","model":"Polo","colour":"Grey","owner":"Archie"}},{"Key":"CAR2","Record":{"make":"Hyundai","model":"Tucson","colour":"green","owner":"Jin Soo"}},{"Key":"CAR3","Record":{"make":"Volkswagen","model":"Passat","colour":"yellow","owner":"Max"}},{"Key":"CAR4","Record":{"make":"Tesla","model":"S","colour":"black","owner":"Adriana"}},{"Key":"CAR5","Record":{"make":"Peugeot","model":"205","colour":"purple","owner":"Michel"}},{"Key":"CAR6","Record":{"make":"Chery","model":"S22L","colour":"white","owner":"Aarav"}},{"Key":"CAR7","Record":{"make":"Fiat","model":"Punto","colour":"violet","owner":"Pari"}},{"Key":"CAR8","Record":{"make":"Tata","model":"Nano","colour":"indigo","owner":"Valeria"}},{"Key":"CAR9","Record":{"make":"Holden","model":"Barina","colour":"brown","owner":"Shotaro"}}]
{"make":"VW","model":"Polo","colour":"Grey","owner":"Mary"}
{"make":"VW","model":"Polo","colour":"Grey","owner":"Archie"}
```

查询和写入都成功了。

自动生成的连接配置文件`connection-org1.yaml`对于Go版本的SDK测试程序是有缺陷的，但针对Java版本的SDK测试程序就完全没问题。目前还搞不清楚为什么会如此！

虽然成功了，但从输出来看，还是有些小问题：

```
fab.detectDeprecatedNetworkConfig -> WARN Getting orderers from endpoint config channels.orderer is deprecated, use entity matchers to override orderer configuration
 [fabsdk/fab] 2020/08/06 07:38:09 UTC - fab.detectDeprecatedNetworkConfig -> WARN visit https://github.com/hyperledger/fabric-sdk-go/blob/master/test/fixtures/config/overrides/local_entity_matchers.yaml for samples
```

从提示看，虽然添加的这段配置可以工作，但官方好像已经不推荐这种形式，而是推荐使用`entity matchers`的方式。

那么什么是`entity matchers`？直接提供了一个sample让参考。

打开示例看了看，并参照例子中的格式配置到`connection-org1.yaml`文件中用来替代我添加的内容。很遗憾，没有成功。

所以，虽然暂时解决了这个问题，但并非最终方案。关于如何使用`entity matchers`，后续还要继续研究。
