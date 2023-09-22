---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# iOS 证书转换相关命令

证书有效期：

```
openssl x509 -in xxx.pem -noout -dates
```

生成pem格式的证书：

```
openssl pkcs12 -in CertificateName.p12 -out CertificateName.pem -nodes
```

[附：我的博客地址](https://gsl201600.github.io/2019/02/21/iOS%E8%AF%81%E4%B9%A6%E8%BD%AC%E6%8D%A2%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4/)
