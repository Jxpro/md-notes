## 一、环境搭建

1.   参考文章：[Hyperledger Fabric1.4环境搭建](https://www.cnblogs.com/cbkj-xd/p/11067790.html)

     ==error==

     运行 `./byfn.sh up` 时

     遇到   `Could not resolve host: nexus.hyperledger.org`   错误

     ==reason== 

     `nexus.hyperledger.org` 这个网站不再维护了

2.   参考文章：[Hyperledger Fabric1.0环境搭建](https://blog.csdn.net/qq_36336522/article/details/84071711)

     ==error==

      运行 `source download-dockerimages.sh -c x86_64-1.0.0 -f x86_64-1.0.0` 时

     遇到错误：

     ```text
     Cannot run peer because cannot init crypto, missing /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp folder 
     ```
     ==reason==

     1.   参考 [fabric部署e2e_cli单节点和first-network示例及相关错误](https://blog.csdn.net/vivian_ll/article/details/79966210) 文章
     2.   查看 `fabric/release/linux-amd64/bin` 文件夹，发现文件夹下无内容，也没有  `crypto、configtxgen` 等文件，可能原因是 `configtxgen` 版本不一致导致的。
     3.   尝试手动运行 `bootstrap.1.0.0.sh` 下载
     4.   发现同上面一样的错误 `Could not resolve host: nexus.hyperledger.org` 
     5.   所以最后的原因还是 `nexus.hyperledger.org` 这个网站不再维护了

<div style="text-align:center;margin-top:18px;"><span style="color:crimson;font-size:27px">所以2.X版本才是最终解 ！！！</span></div>

