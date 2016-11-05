# Hướng dẫn cấu hình IP tĩnh cho các server chạy hệ điều hành Linux: Ubuntu 12.04, Ubuntu 14.04, Centos6 và Centos7

Thông thường với các server, chúng ta thường cấu hình IP tĩnh cho server với mục đích đảm bảo cho việc truy nhập từ client đến server không bị thay đổi IP hay gián đoạn và 1 vài mục đích khác nữa.

Bài viết sẽ hướng dẫn việc cấu hình IP tĩnh cho các server chạy hệ điều hành Linux phổ biến như: Ubuntu 12.04, Ubuntu 14.04, Centos 6 và Centos 7.

## 1. Ubuntu 12.04 và Ubuntu 14.04
Với các server debian-based như Ubuntu, chúng ta sẽ thực hiện cấu hình IP tĩnh cho server trong file: /etc/network/interfaces. 

Giả sử ở đây tôi có interface eth0 gắn với server cần cấu hình IP tĩnh.
Trong ví dụ này tôi sử dụng vim là text editor, các bạn có thể sử dụng text editor khác mà các bạn quen dùng hơn: như vi, nano ....

- Đầu tiên: mở file config /etc/network/interfaces
```
root@ubuntu:~# vim /etc/network/interfaces
```

- Sau đó sửa file với cấu hình như sau: 
```
#The loopback network interface
auto lo
iface lo inet loopback

#The primary network interface
auto eth0
iface eth0 inet static
  address 192.168.98.100
  netmask 255.255.255.0
  gateway 192.168.98.2
  dsn-nameserer 8.8.8.8
```

Giải thích file cấu hình: 

Phần này là cấu hình cho card loopback. Mặc định với tất cả các server: 
```
#The loopback network interface
auto lo
iface lo inet loopback
```

Đây chính là phần cấu hình IP tĩnh cho interface eth0: 
```
# The primary network interface
auto eth0				
iface eth0 inet static			
    address 192.168.98.100
    netmask 255.255.255.0
    gateway 192.168.98.2
    dsn-nameserer 8.8.8.8
```

*  Dòng 1: auto eth0 sẽ cấu hình cho interface eth0 khởi động cùng hệ thống
-  Dòng 2: iface eth0 inet static tạo 1 đoạn cấu hình tĩnh với tên là eth0 trên card Ethernet của chúng ta. 
Các dòng tiếp theo là phần cấu hình IP cho interface: 
    + address: địa chỉ chúng ta muốn đặt cho server
    + netmask: subnet mask của dải mạng chúng ta sử dụng
    + gateway: default gateway cho server
    + dns-nameserver: DNS server sẽ dùng cho server để phân giải internet. Ở đây chọn DNS server của google.

Sau khi cấu hình xong đúng như trên, chúng ta sẽ lưu file lại và thoát ra ngoài. 

Để cập nhật cấu hình IP mới cho interface trên ubuntu, chúng ta sẽ dùng lệnh: 
```
root@ubuntu:~# ifdown -a && ifup -a
```

Sau đó kiểm tra xem cấu hình cho interface đã đúng chưa và đã hoạt động chưa bằng các lệnh: 
- Show các network interface của server
```
root@ubuntu:~# ip a
```
- Thực hiện ping thử ra internet hoặc đến server cùng dải mạng: 
```
root@ubuntu:~# ping google.com
root@ubuntu:~# ping 192.168.98.10
```

Như vậy chúng ta đã hoàn thành cấu hình IP tĩnh cho server Ubuntu

## 2. CentOS 6 và CentOS 7
Với các server CentOS, chúng ta sẽ thực hiện cấu hình IP tĩnh cho server trong file: /etc/sysconfig/network-scripts/ifcfg-interface_name
Với interface_name ở đây đối với Centos 6 thường là eth0, eth1, eth2....; còn với Centos 7 thường là enp0s1, enp0s2 ......


### a. CentOS 6. 
Giả sử ở đây có interface eth0 cần cấu hình IP tĩnh.
- Mở file config: /etc/sysconfig/network-scripts/ifcfg-eth0
```
root@centos6.5:~# vim /etc/sysconfig/network-scripts/ifcfg-eth0
```

- Thực hiện chỉnh sửa file cấu hình như sau: 
```
DEVICE=eth0				        # Tên của interface
ONBOOT=yes 			            # Khởi động interface cùng server
IPADDR=192.168.1.2 		        # IP tĩnh chúng ta muốn đặt cho server
BOOTPROTO=static 		        # Phương thức cài đặt cho interface này là cấu                                       hình IP tĩnh
HWADDR=20:89:84:c8:12:8a		# Mac address của card ethernet
TYPE=Ethernet
NETMASK=255.255.255.0		    # Subnetmask của dải mạng chúng sử dụng
GATEWAY=192.168.1.1		        # Default gateway của server
```

Lưu file và thoát ra ngoài

- Tiếp theo là cấu hình DNS server để phân giải DNS. 
Mở file /etc/resolv.conf và thêm vào dns server như sau: 
```
nameserver 8.8.8.8
```
Lưu file và thoát ra ngoài

- Cuối cùng là restart lại dịch vụ network bằng lệnh: 
```
root@centos6.5:~# service network restart
```

- Kiểm tra cấu hình và kết nối.
Done

### b. CentOS 7
Giả sử ở đây có interface enp0s1 cần cấu hình ip tĩnh.
- Mở file cấu hình: /etc/sysconfig/network-scripts/ifcfg-enp0s1 :
```
root@centos7:~# vim /etc/sysconfig/network-scripts/ifcfg-enp0s1
```

- Thực hiện sửa file config như sau: 
```
TYPE=Ethernet
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
UUID=75a170ba-6bfa-4553-8070-e658eb8ffb1e
NAME=enp0s1
BOOTPROTO=static
IPADDR=192.168.1.10
GATEWAY = 192.168.1.1
ONBOOT=yes
```

Lưu lại file và thoát ra. 

**Chú ý:**
```
TYPE=Ethernet
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
UUID=75a170ba-6bfa-4553-8070-e658eb8ffb1e
```
Phần cấu hình này thường là mặc định với 1 server centos 7 cho card mạng gắn sẵn từ đầu vào server, thông thường chúng ta sẽ không cần thay đổi. Phần cấu hình của chúng ta ở đây nằm ở 5 dòng cuối: 
```
NAME=enp0s1
BOOTPROTO=static
IPADDR=192.168.1.10
GATEWAY = 192.168.1.1
ONBOOT=yes
```

- Tiếp theo chúng ta sẽ thực hiện cấu hình DNS server. Mở file cấu hình /etc/resolv.conf: 
```
root@centos7:~# vim /etc/resolv.conf
```

- Sửa file config này, thêm vào DNS nameserver chúng ta muốn sử dụng: 
```
nameserver 8.8.8.8
```

- Cuối cùng khởi động lại network service bằng lệnh: 
```
root@centos7:~# systemctl restart network.service
```

- Kiểm tra cấu hình và kết nối. 

Done 
