#A.SNMP
##I.Khái niệm
- SNMP (Simple Network Management Protocol) là một tiêu chuẩn quản lý mạng được sử dụng rộng rãi trong các mạng truyền thông hỗ trợ giao thức TCP/IP
- SNMP cung cấp một phương thức quản lý các thiết bị như máy trạm hoặc máy chủ, bộ định tuyến, cầu, hub từ một máy tính trung tâm chứa phần mềm quản lý mạng
- SNMP hiện có 4 phiên bản: SNMPv1, SNMPv2c, SNMPv2u,SNMPv3.

##II.Vai trò
SNMP có thể giúp giải quyết các bài toán như:
- Cấu hình các thiết bị từ xa
- Giám sát tài nguyên máy chủ
- Giám sát lưu lượng trên các port của switch, router
- Hệ thống tự động cảnh báo sự cố tức thời

##III.Thành phần
- Theo RFC1157, kiến trúc của SNMP bao gồm 2 thành phần : 

 -Các trạm quản lý mạng (**network management station-NMS**)
 -Các thành tố mạng (**network element-NE**)
- Tiến trình:

 - Tiến trình chạy trên NMS được gọi là **SNMP Application**
 - Tiến trình chạy trên NE được gọi là **SNMP Agent**
- Một thiết bị hỗ trợ SNMP có thể cung cấp nhiều thông tin khác nhau, mỗi thông tin đó gọi là một **object**. Mỗi object có một tên gọi và một mã số để nhận dạng object đó, mã số gọi là **Object ID (OID)**. Ví dụ: Tên thiết bị được gọi là sysName, OID là 1.3.6.1.2.1.1.5
- Mỗi object có quyền truy cập là READ_ONLY hoặc READ_WRITE. Mọi object đều có thể đọc được nhưng chỉ những object có quyền READ_WRITE mới có thể thay đổi được giá trị.
- **MIB** (Management Information Base-Cơ sở thông tin quản lý) là một cấu trúc dữ liệu gồm các đối tượng được quản lý (Object)

##IV.Phương thức hoạt động SNMPv1

<img src="http://img.prntscr.com/img?url=http://i.imgur.com/j1Oa08t.png">

- Giao thức SNMPv1 có 5 phương thức hoạt động, tương ứng với 5 loại bản tin như sau:

| Bản tin | Mô tả |
|---------|-------|
| GetRequest | Manager gửi GetRequest cho agent để yêu cầu agent cung cấp thông tin nào đó dựa vào ObjectID (trong GetRequest có chứa OID) |
| GetNextRequest | Manager gửi GetNextRequest có chứa một ObjectID cho agent để yêu cầu cung cấp thông tin nằm kế tiếp ObjectID đó trong MIB. |
| SetRequest | Manager gửi SetRequest cho agent để đặt giá trị cho đối tượng của agent dựa vào ObjectID. |
| GetResponse | Agent gửi GetResponse cho Manager để trả lời khi nhận được GetRequest/GetNextRequest |
| Trap | Agent tự động gửi Trap cho Manager khi có một sự kiện xảy ra đối với một object nào đó trong agent. |

#B.Zabbix
##I.Cài đặt Zabbix server 2.4 trên CentOS6
###Bước1:Cài đặt gói Apache httpd
###Bước2:Cài đặt gói PHP
###Bước3:Cài đặt gói MySQL server
###Bước4:Cài đặt các gói hỗ trợ Zabbix

`yum -y install php-mysql php-gd php-xml php-bcmath`

`yum -y install http://repo.zabbix.com/zabbix/2.4/rhel/6/x86_64/zabbix-release-2.4-1.el6.noarch.rpm`

###Bước 5:Cài đặt Zabbix server, Zabbix agent để theo dõi chính nó

`yum -y install zabbix-server-mysql zabbix-web-mysql zabbix-agent zabbix_get`

###Bước 6:Tạo database cho Zabbix
- Đăng nhập mysql

`mysql -u root -p`
- Tạo database zabbix 

`create database zabbix;`
- Tạo và gán quyền user zabbix 

`grant all privileges on zabbix.* to zabbix@'localhost' identified by 'password';`

`grant all privileges on zabbix.* to zabbix@'%' identified by 'password';`

`flush privileges;`

- Sao chép cơ sở dữ liệu vào database zabbix

`cd /usr/share/doc/zabbix-server-mysql-*/create`

`mysql -u root -p zabbix < schema.sql`
`mysql -u root -p zabbix < images.sql`
`mysql -u root -p zabbix < data.sql`

- Cấu hình Zabbix server

`vi /etc/zabbix/zabbix_server.conf`

```
# line 73: add
DBHost=localhost
# line 107: add DB password for Zabbix
DBPassword=password
```

- Khởi động Zabbix server

`/etc/rc.d/init.d/zabbix-server start`

`chkconfig zabbix-server on `

- Cấu hình Zabbix agent

`vi /etc/zabbix/zabbix_agentd.conf`

```
# line 85: specify Zabbix server
Server=ip-server
# line 126: specify Zabbix server
ServerActive=ip-server
# line 137: change to the own hostname
Hostname=name
```

- Khởi động Zabbix agent

`/etc/rc.d/init.d/zabbix-agent start `

`chkconfig zabbix-agent on`

- Chỉnh cấu hình httpd

`vi /etc/httpd/conf.d/zabbix.conf`

```
# line 11: change access permittion for Zabbix frontend
Allow from localhost dai-ip-allow
# line 18: uncomment and change to your timezone
php_value date.timezone Asia/Bangkok
```

`/etc/rc.d/init.d/httpd restart`

- Truy cập địa chỉ ip-server để cấu hình trên giao diện web.

##II.Cài đặt giám sát máy CentOS6
###Bước 1: Cài đặt Zabbix agent
- Cài gói hỗ trợ Zabbix

`yum -y install http://repo.zabbix.com/zabbix/2.4/rhel/6/x86_64/zabbix-release-2.4-1.el6.noarch.rpm`

- Cài Zabbix agent

`yum -y install zabbix-agent`

- Cấu hình Zabbix agent

`vi /etc/zabbix/zabbix_agentd.conf`

```
# line 85: specify Zabbix server
Server=ip-server
# line 126: specify Zabbix server
ServerActive=ip-server
# line 137: change to the own hostname
Hostname=name-client
```
- Khởi động Zabbix agent
`/etc/rc.d/init.d/zabbix-agent start`

`chkconfig zabbix-agent on`

###Bước 2: Cấu hình trên Zabbix server
- Đăng nhập tài khoản zabbix

<img src="http://img.prntscr.com/img?url=http://i.imgur.com/XpsUQom.png">

- Chọn **Configuration** -> **Hosts** -> **Creat Host**

<img src="http://img.prntscr.com/img?url=http://i.imgur.com/1TqSouh.png">

- Cấu hình phần **Host**: Nhập Hostname; Chọn Group; Điền IP client

<img src="http://img.prntscr.com/img?url=http://i.imgur.com/qVUL7Td.png">

- Cấu hình phần **Templates**: Chọn Select

<img src="http://img.prntscr.com/img?url=http://i.imgur.com/euHMiKS.png">

- Chọn OS rồi click Select

<img src="http://img.prntscr.com/img?url=http://i.imgur.com/uzL4By3.png">

- Chọn **Add**

<img src="http://img.prntscr.com/img?url=http://i.imgur.com/Wq4t49y.png">

- Hoàn thành thêm thiết bị giám sát.

