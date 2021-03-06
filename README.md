# dnsmasq_dnscrypt_configure
for china network environment
    
中文说明: [搭建 dnsmasq + dnscrypt 解析组合](https://abbypan.github.io/2017/10/11/dnsmasq-dnscrypt-dnswrapper)

configure file example : /etc/... 

# install 

    $ apt-get install dnsmasq

    $ apt-get install dnscrypt-proxy

    $ apt-get install libsodium-dev libevent-dev autoconf
    $ git clone --recursive git://github.com/cofyc/dnscrypt-wrapper.git
    $ cd dnscrypt-wrapper
    $ make configure
    $ ./configure
    $ make install
    
# plan_a

    weibo.cn
    <--plain--> dnsmaq(127.0.0.1:53)
    <--plain--> local_pub_resolver(114.114.114.114)

    www.google.com
    <--plain--> dnsmaq(127.0.0.1:53)
    <--plain--> localhost_dnscrypt_forwarding(127.0.0.1:53330, dnscrypt-proxy)
    <--dnscrypt--> remote_dnscrypt_pub_resolver(208.67.220.220, opendns)


# plan_b 

    weibo.cn
    <--plain--> dnsmaq(127.0.0.1:53)
    <--plain--> local_open_resolver(202.106.46.151)

    www.google.com
    <--plain--> dnsmaq(127.0.0.1:53)
    <--plain--> localhost_dnscrypt_forwarding(127.0.0.1:53330, dnscrypt-proxy)
    <--dnscrypt--> remote_dnscrypt_forwarding(yyy.yyy.yyy.yyy:53332, dnscrypt-wrapper)
    <--plain--> remote_dnscrypt_forwarding(yyy.yyy.yyy.yyy, 127.0.0.1:53333, dnscrypt-proxy)
    <--dnscrypt--> remote_dnscrypt_resolver(zzz.zzz.zzz.zzz:53335, dnscrypt-wrapper)
    <--plain--> remote_dnscrypt_resolver(zzz.zzz.zzz.zzz, 127.0.0.1:53, bind)

# plan_c 

    weibo.cn
    <--plain--> dnsmaq(127.0.0.1:53)
    <--plain--> local_open_resolver(202.106.46.151)

    www.google.com
    <--plain--> dnsmaq(127.0.0.1:53)
    <--plain--> localhost_dnscrypt_forwarding(127.0.0.1:53330, dnscrypt-proxy)
    <--dnscrypt--> remote_dnscrypt_resolver(xxx.xxx.xxx.xxx:53332, dnscrypt-wrapper)
    <--plain--> remote_dnscrypt_resolver(xxx.xxx.xxx.xxx, 127.0.0.1:53, bind)
                or  
                remote_dnscrypt_resolver(xxx.xxx.xxx.xxx, 8.8.8.8, google dns)

# Note 

localhost dnsmasq can add specific subnet

    sudo dnsmasq --add-subnet=xxx.xxx.xxx.0/24 &

but some public resolvers may ignore the downstream queries subnet, directly use client ip.

plan_c is better when we build own resolver on socks5 proxy server, as remote_dnscrypt_resolver, to satisfy with Geolocation DNS.
