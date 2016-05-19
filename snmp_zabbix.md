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
- Theo RFC1157, kiến trúc của SNMP bao gồm 2 thành phần : các trạm quản lý mạng (**network management station-NMS**) và các thành tố mạng (**network element-NE**)
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
