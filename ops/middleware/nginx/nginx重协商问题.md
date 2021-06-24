# 检测服务器是否开启重协商功能（用于CVE-2011-1473漏洞检测)
## 背景
由于服务器端的重新密钥协商的开销至少是客户端的10倍，因此攻击者可利用这个过程向服务器发起拒绝服务攻击。`OpenSSL 1.0.2`及以前版本受影响。
## 方法
使用OpenSSL（linux系统基本都自带）连接服务器进行测试：
```bash
openssl s_client -connect ip:port

HEAD / HTTP/1.0
R
```
## 示例
服务器443端口开启重协商，使用`openssl s_client -connect 10.10.18.8:443 `连接测试（删除了部分证书信息）：
```linux
[root@yum-17 pentmenu-1.7.4]# openssl s_client -connect 10.10.18.8:443
CONNECTED(00000003)
depth=0 C = CN, ST = Beijing, L = Beijing, O = Yonyou, OU = Yonyou, CN = yonyou, emailAddress = test.yonyou.com
verify error:num=18:self signed certificate
verify return:1
depth=0 C = CN, ST = Beijing, L = Beijing, O = Yonyou, OU = Yonyou, CN = yonyou, emailAddress = test.yonyou.com
verify return:1
---
Certificate chain
 0 s:/C=CN/ST=Beijing/L=Beijing/O=Yonyou/OU=Yonyou/CN=yonyou/emailAddress=test.yonyou.com
   i:/C=CN/ST=Beijing/L=Beijing/O=Yonyou/OU=Yonyou/CN=yonyou/emailAddress=test.yonyou.com
---
Server certificate
-----BEGIN CERTIFICATE-----
MIID3TCCAsWgAwIBAgIJALZBk5ueSh4SMA0GCSqGSIb3DQEBCwUAMIGEMQswCQYD
VQQGEwJDTjEQMA4GA1UECAwHQmVpamluZzEQMA4GA1UEBwwHQmVpamluZzEPMA0G
A1UECgwGWW9ueW91MQ8wDQYDVQQLDAZZb255b3UxDzANBgNVBAMMBnlvbnlvdTEe
MBwGCSqGSIb3DQEJARYPdGVzdC55b255b3UuY29tMB4XDTIwMTIxNjA0MTkyNloX
DTMwMTIxNDA0MTkyNlowgYQxCzAJBgNVBAYTAkNOMRAwDgYDVQQIDAdCZWlqaW5n
MRAwDgYDVQQHDAdCZWlqaW5nMQ8wDQYDVQQKDAZZb255b3UxDzANBgNVBAsMBllv
bnlvdTEPMA0GA1UEAwwGeW9ueW91MR4wHAYJKoZIhvcNAQkBFg90ZXN0Lnlvbnlv
dS5jb20wggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCy9d9LQTEip4Ll
2eKlFtppP/166V0ZtjhnKJkV5HhCImacdNNNWR2Yl/0aycgKw3thMFFRE7aCSH06
evSMfphwCjedb8oJaCqsiezB0/8WxpQqcz4M53AG9O2aDmxDSy1nzt/8NYrtHSWh
4JpTjkLZ1QHW411h60M/Z7e5oyU1jnDFWtmQYQJapehNk6ThR4UYTrU19qCv1rqA
QygR5TsijgklXLH+nPh/JMiR80To96wHloqUAir+jWBAtQhi+/bzmbKIYtkPUEIc
OlnJjevn1XY1hGiKLRAIHJNlGjSAZJq74mshH660RzpuTIAZsr4jdVWomC7phdRi
JT8lbd2TAgMBAAGjUDBOMB0GA1UdDgQWBBSbX6CEtnzSSbS1Qia9PHlr5K9ByjAf
BgNVHSMEGDAWgBSbX6CEtnzSSbS1Qia9PHlr5K9ByjAMBgNVHRMEBTADAQH/MA0G
CSqGSIb3DQEBCwUAA4IBAQCHoY4r3RfcAqTtu/+/E+saXZ3mPGozfoSGkJQ2DPcN
z4GwFiffVFrhP8nLihefCW+MVNPZDJLEAoR6oU+HoMgUf8rB6K/vwDhsLmOl2m3U
Ls3TjALOaaPtNeR9+tnp/u7snVMzva+d2Hr7Dcctie3pufilQVPO9zRVt9P35ON9
pRApwVV109J4xUaH0lY88wVdLTxP7hUFtwZRLVl5DdCgNyB1kZH/r5r0cIqcpvpz
qxjTV3MX5dpEQjSYuOEFLxNOoVgwwe7+Qd1wAMcpgtm9HOctmcku+WEdUKfMpyWC
dTd5ryL+LQFPlA4CMSCn/wK7z8UCGmI5/F7qrxe6e4yh
-----END CERTIFICATE-----
subject=/C=CN/ST=Beijing/L=Beijing/O=Yonyou/OU=Yonyou/CN=yonyou/emailAddress=test.yonyou.com
issuer=/C=CN/ST=Beijing/L=Beijing/O=Yonyou/OU=Yonyou/CN=yonyou/emailAddress=test.yonyou.com
---
No client certificate CA names sent
Peer signing digest: SHA256
Server Temp Key: ECDH, P-256, 256 bits
---
SSL handshake has read 1663 bytes and written 415 bytes
---
New, TLSv1/SSLv3, Cipher is ECDHE-RSA-AES256-GCM-SHA384
Server public key is 2048 bit
Secure Renegotiation IS supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
SSL-Session:
    Protocol  : TLSv1.2
    Cipher    : ECDHE-RSA-AES256-GCM-SHA384
    Session-ID: B147FC74F646DE55FA04FC25CA3749001A4E1A514225A356186A918CFEB373AB
    Session-ID-ctx: 
    Master-Key: 779323DDB5DE0591CA88367B36A5249D333D5E251B4F46B03322456490BA659A377D7691F807A200C133A2425155CDFB
    Key-Arg   : None
    Krb5 Principal: None
    PSK identity: None
    PSK identity hint: None
    TLS session ticket lifetime hint: 300 (seconds)
    TLS session ticket:
    0000 - 6f 9b 18 0c 9a 66 d5 8f-0c 07 8b de 1f 0b 09 4c   o....f.........L
    0010 - 2c 62 85 20 e7 c0 aa 9b-aa d8 45 57 3d c5 2d 5a   ,b. ......EW=.-Z
    0020 - 4f 32 a1 aa ad d2 b2 1a-77 31 6f 8f 96 8f 25 d7   O2......w1o...%.
    0030 - 83 49 77 1a 59 20 d7 47-26 09 24 9f f7 84 c5 8d   .Iw.Y .G&.$.....
    0040 - 71 89 62 d3 61 9a a8 e1-41 bc 9e 65 18 bc f9 4f   q.b.a...A..e...O
    0050 - 7d c1 32 bb eb b3 11 47-e8 46 59 f7 9b 7b ea 2f   }.2....G.FY..{./
    0060 - d7 34 bf ce 5a 01 08 b3-1b 47 3e 3c a1 55 a5 24   .4..Z....G><.U.$
    0070 - f6 2a 8a 73 02 63 e7 aa-54 df 1d c0 12 d4 94 c7   .*.s.c..T.......
    0080 - 7a 33 a6 70 21 d3 8e 59-ad b9 8f 2c 4e b4 4d ee   z3.p!..Y...,N.M.
    0090 - 2f 85 15 c4 ce 48 a3 32-aa af fa 0e 05 f3 fa 53   /....H.2.......S
    00a0 - e3 9d ed 72 20 ae a2 22-e9 1c e1 9f 6b 40 51 87   ...r .."....k@Q.

    Start Time: 1608170662
    Timeout   : 300 (sec)
    Verify return code: 18 (self signed certificate)
---
HEAD / HTTP/1.0
R
RENEGOTIATING
140337423468432:error:14094153:SSL routines:ssl3_read_bytes:no renegotiation:s3_pkt.c:1481:
[root@yum-17 pentmenu-1.7.4]# 
```
输入`R`后出发重协商，此时服务器报错并断开连接，说明服务器重协商功能被关闭。