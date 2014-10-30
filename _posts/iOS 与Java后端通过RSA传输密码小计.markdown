#iOS 与Java后端通过RSA传输密码小计


- 生成公钥私钥

1. 命令行

    ```shell
    	#openssl req -x509 -out public_key.der -outform der -new -newkey rsa:1024 -keyout private_key.pem -days 3650	
    ```
此时会见到2个文件生成:**private_key.pem**	  **public_key.der**
2. 

- 为Java后端导出PKCS8私钥
   因为java后端无法直接读取.pem格式的私钥,所以将pem导出为PKCS8格式

1. 使用命令行  
    
    ```shell
    #openssl pkcs8 -topk8 -inform PEM -outform DER -in rsa_pem.key -out pkcs8_der.key -nocrypt
    ```
生成的**pkcs8_der.key** 用cat 命令打开,复制到java代码中,作为const private常量,注意:一般这个私钥是有\n转义符的,不要删除.

- iOS
1. 将之前生成的公钥导入Xcode工程
2. 在iOS工程目录中,添加2个第三方依赖.不知道何为cocopod请百度.添加pod依赖:cd到xcode工程目录:

    ```shell
    #pod 'TBUtility', '~> 2.0.0-build-13'
    #pod 'XRSA', '~> 1.1.0'
    ```
3.导入RSA 和Base64 2个依赖
    ```objc
    #import "XRSA.h"
    #import <TBUtility/NSData+Base64.h>
    
    
        NSString *pubKeyPathStr = [[NSBundle mainBundle] pathForResource:@"public_key" ofType:@"der"];
        XRSA *rsa = [[XRSA alloc] initWithPublicKey:pubKeyPathStr];
        NSLog(@"passwordEncryptStr:\n%@", [rsa encryptToString:@"passwordEncryptStr"]);
    
    ```


- 期间踩到的坑
	- 使用服务端直接下发的PKCS8公钥,加密总出现读取密钥失败的问题
	- 交给服务端的私钥含有\n 转义符,把它给删了...
	- 服务端采用动态生成密钥对,然后debug找到公钥密文串,复制发送给我,但iOS自带的Security库并不支持直接使用密文串加密...
	