#IPSec VPN Host to Host (without NAT)

WEST
~~~~

vagrant@west-01:
sudo cp /etc/ipsec.conf /etc/ipsec.conf.orig

cat<<EOF | sudo tee /etc/ipsec.conf
conn west-to-east
    authby=secret
    auto=route
    keyexchange=ike
    left=192.168.1.120
    right=192.168.1.121
    type=transport
    esp=aes128gcm16!
EOF

sudo cp /etc/ipsec.secrets /etc/ipsec.secrets.orig

cat<<EOF | sudo tee /etc/ipsec.secrets
192.168.1.120 192.168.1.121 : PSK "vagrant"
EOF

sudo ipsec restart
sudo ipsec statusall

~~~~
EAST
~~~~


vagrant@east-01:~$

sudo cp /etc/ipsec.conf /etc/ipsec.conf.orig

cat<<EOF | sudo tee /etc/ipsec.conf
conn east-to-west
    authby=secret
    auto=route
    keyexchange=ike
    left=192.168.1.121
    right=192.168.1.120
    type=transport
    esp=aes128gcm16!
EOF

sudo cp /etc/ipsec.secrets /etc/ipsec.secrets.orig

cat<<EOF | sudo tee /etc/ipsec.secrets
192.168.1.120 192.168.1.121 : PSK "vagrant"
EOF

sudo ipsec restart
sudo ipsec statusall

~~~~
smoke tests
~~~~

LEFT-WEST

vagrant@west-01::~/strongswan-5.8.2$ ping -s 4048 192.168.1.121


RIGHT-EAST

vagrant@east-01:~/strongswan-5.8.2$ sudo watch ipsec statusall
Every 2.0s: ipsec statusall                                                                                                                                 east-01: Sun Feb 16 18:48:07 2020
Status of IKE charon daemon (strongSwan 5.8.2, Linux 5.0.0-37-generic, x86_64):
  uptime: 33 seconds, since Feb 16 18:47:34 2020
  malloc: sbrk 2146304, mmap 0, used 425408, free 1720896
  worker threads: 11 of 16 idle, 5/0/0/0 working, job queue: 0/0/0/0, scheduled: 3
  loaded plugins: charon aes des rc2 sha2 sha1 md5 mgf1 random nonce x509 revocation constraints pubkey pkcs1 pkcs7 pkcs8 pkcs12 pgp dnskey sshkey pem fips-prf gmp curve25519 xcbc cmac hmac drbg attr kernel-netlink resolve socket-default stroke vici updown xauth-generic counters
Listening IP addresses:
  10.0.2.15
  192.168.1.121
Connections:
east-to-west:  192.168.1.121...192.168.1.120  IKEv1/2
east-to-west:   local:  [192.168.1.121] uses pre-shared key authentication
east-to-west:   remote: [192.168.1.120] uses pre-shared key authentication
east-to-west:   child:  dynamic === dynamic TRANSPORT
Routed Connections:
east-to-west{1}:  ROUTED, TRANSPORT, reqid 1
east-to-west{1}:   192.168.1.121/32 === 192.168.1.120/32
Security Associations (1 up, 0 connecting):
east-to-west[1]: ESTABLISHED 21 seconds ago, 192.168.1.121[192.168.1.121]...192.168.1.120[192.168.1.120]
east-to-west[1]: IKEv2 SPIs: 890f31a2573445ec_i 8526fa4155f6c299_r*, pre-shared key reauthentication in 2 hours
east-to-west[1]: IKE proposal: AES_CBC_128/HMAC_SHA2_256_128/PRF_AES128_XCBC/CURVE_25519
east-to-west{2}:  INSTALLED, TRANSPORT, reqid 1, ESP SPIs: cbb690a7_i c45ce6ae_o
east-to-west{2}:  AES_GCM_16_128, 81120 bytes_i (20 pkts, 1s ago), 81120 bytes_o (20 pkts, 1s ago), rekeying in 44 minutes
east-to-west{2}:   192.168.1.121/32 === 192.168.1.120/32


vagrant@east-01:~$ sudo tcpdump esp -i eth1
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth1, link-type EN10MB (Ethernet), capture size 262144 bytes
18:48:56.337730 IP west-01.local > east-01.local: ESP(spi=0xcbb690a7,seq=0x45), length 1440
18:48:56.337787 IP west-01.local > east-01.local: esp
18:48:56.337805 IP west-01.local > east-01.local: esp
18:48:56.337870 IP east-01.local > west-01.local: ESP(spi=0xc45ce6ae,seq=0x45), length 1440
18:48:56.337923 IP east-01.local > west-01.local: esp
18:48:56.337933 IP east-01.local > west-01.local: esp
18:48:57.362834 IP west-01.local > east-01.local: ESP(spi=0xcbb690a7,seq=0x46), length 1440
18:48:57.362876 IP west-01.local > east-01.local: esp
18:48:57.362880 IP west-01.local > east-01.local: esp
18:48:57.363036 IP east-01.local > west-01.local: ESP(spi=0xc45ce6ae,seq=0x46), length 1440
18:48:57.363152 IP east-01.local > west-01.local: esp
18:48:57.363178 IP east-01.local > west-01.local: esp
^C
12 packets captured
12 packets received by filter
0 packets dropped by kernel

~~~~
