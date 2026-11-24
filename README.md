# NDLab01 – Packet Tracer Network Implementation

## 1. Giới thiệu

Repository này lưu trữ quá trình thực hiện bài thực hành **NDLab01 – Packet Tracer Network Implementation** bằng phần mềm Cisco Packet Tracer.

Mục tiêu của bài thực hành là xây dựng, cấu hình và kiểm tra một hệ thống mạng gồm mạng nội bộ Central và mạng ISP/Server. Thông qua bài lab, người thực hiện tìm hiểu cách kết nối thiết bị mạng, cấu hình địa chỉ IPv4, thiết lập định tuyến tĩnh (Static Routing), kiểm tra kết nối bằng Ping và Traceroute, đồng thời quan sát quá trình truyền dữ liệu trong Simulation Mode.

Nội dung được triển khai theo quy trình:

**Predict → Build → Configure → Verify → Simulate → Explain**

## 2. Mục tiêu thực hiện

- Làm quen với giao diện và các chức năng cơ bản của Cisco Packet Tracer.
- Xây dựng topology mạng theo yêu cầu của bài lab.
- Kết nối Router, Switch, máy tính và Eagle Server.
- Cấu hình địa chỉ IP, Subnet Mask và Default Gateway.
- Cấu hình Static Routing giữa hai Router.
- Kiểm tra khả năng kết nối giữa các thiết bị trong mạng.
- Sử dụng `ping`, `tracert` và các lệnh Cisco IOS để kiểm tra hệ thống.
- Quan sát quá trình trao đổi gói tin ARP và ICMP trong Simulation Mode.
- Ghi nhận kết quả và lưu lại hình ảnh minh chứng trong thư mục `evidence`.

## 3. Mô hình mạng

Hệ thống mạng được chia thành hai khu vực chính:

- **Central LAN:** Mạng nội bộ gồm Switch S1-Central và 11 Pods, mỗi Pod có 2 máy tính.
- **ISP/Server Network:** Mạng chứa Router R1-ISP và Eagle Server.

Hai khu vực được kết nối thông qua Router R1-ISP và Router R2-Central.

Đường truyền dữ liệu chính:

`hostPod → S1-Central → R2-Central → R1-ISP → Eagle Server`

Khi Eagle Server phản hồi, dữ liệu được truyền ngược lại theo đường đi phù hợp với bảng định tuyến.

### Thiết bị sử dụng

| Thiết bị | Số lượng | Vai trò |
|---|---:|---|
| Router 2811 | 2 | Kết nối và định tuyến giữa các mạng |
| Switch 2960-24TT | 1 | Kết nối các máy tính trong mạng LAN |
| PC-PT | 22 | Máy trạm thuộc 11 Pods |
| PC-PT (Eagle Server) | 1 | Đại diện cho máy chủ đích |
| **Tổng cộng** | **26** | |

*Eagle Server được mô phỏng bằng PC-PT trong Packet Tracer.*

### Topology mạng

Ảnh toàn bộ sơ đồ mạng được lưu tại:

`evidence/01-network-topology.png`

## 4. Kế hoạch địa chỉ IP

### 4.1. Địa chỉ IP của Router

| Thiết bị | Interface | IPv4 Address | Subnet Mask |
|---|---|---|---|
| R1-ISP | Serial0/0/0 | 10.10.10.6 | 255.255.255.252 |
| R1-ISP | FastEthernet0/0 | 192.168.254.253 | 255.255.255.0 |
| R2-Central | Serial0/0/0 | 10.10.10.5 | 255.255.255.252 |
| R2-Central | FastEthernet0/0 | 172.16.255.254 | 255.255.0.0 |

### 4.2. Địa chỉ IP của Eagle Server

| Thành phần | Giá trị |
|---|---|
| IP Address | 192.168.254.254 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.254.253 |

### 4.3. Địa chỉ IP của 11 Pods

Mỗi Pod gồm hai máy tính A và B.

| Pod | Máy tính A | Máy tính B |
|---|---|---|
| Pod 1 | 172.16.1.1 | 172.16.1.2 |
| Pod 2 | 172.16.2.1 | 172.16.2.2 |
| Pod 3 | 172.16.3.1 | 172.16.3.2 |
| Pod 4 | 172.16.4.1 | 172.16.4.2 |
| Pod 5 | 172.16.5.1 | 172.16.5.2 |
| Pod 6 | 172.16.6.1 | 172.16.6.2 |
| Pod 7 | 172.16.7.1 | 172.16.7.2 |
| Pod 8 | 172.16.8.1 | 172.16.8.2 |
| Pod 9 | 172.16.9.1 | 172.16.9.2 |
| Pod 10 | 172.16.10.1 | 172.16.10.2 |
| Pod 11 | 172.16.11.1 | 172.16.11.2 |

Thông số chung cho các máy tính:

- Subnet Mask: `255.255.0.0`
- Default Gateway: `172.16.255.254`

### 4.4. Địa chỉ quản trị Switch

| Thành phần | Giá trị |
|---|---|
| Interface | VLAN 1 |
| IP Address | 172.16.254.1 |
| Subnet Mask | 255.255.0.0 |
| Default Gateway | 172.16.255.254 |

## 5. Các nội dung triển khai

### 5.1. Xây dựng topology

- Thêm hai Router 2811.
- Thêm một Switch 2960-24TT.
- Thêm 22 máy tính cho 11 Pods.
- Thêm một PC-PT đại diện cho Eagle Server.
- Lắp module WIC-2T để sử dụng kết nối Serial nếu Router chưa có cổng Serial.
- Kết nối các thiết bị theo đúng topology và cổng giao tiếp.

### 5.2. Cấu hình Router

**R1-ISP**

- Cấu hình địa chỉ IP cho Serial0/0/0 và FastEthernet0/0.
- Bật các interface bằng lệnh `no shutdown`.
- Cấu hình `clock rate` tại đầu DCE nếu phù hợp.
- Thiết lập Static Route về mạng Central.

**R2-Central**

- Cấu hình địa chỉ IP cho Serial0/0/0 và FastEthernet0/0.
- Bật các interface bằng lệnh `no shutdown`.
- Thiết lập Static Route đến mạng Server.

### 5.3. Cấu hình thiết bị đầu cuối

- Cấu hình IP Address cho 22 máy tính.
- Cấu hình Subnet Mask và Default Gateway.
- Cấu hình IP Address và Default Gateway cho Eagle Server.
- Cấu hình IP quản trị cho Switch nếu sử dụng.

### 5.4. Cấu hình định tuyến

Sử dụng Static Routing để thiết lập đường đi giữa hai mạng.

**Trên R2-Central:**

```bash
ip route 192.168.254.0 255.255.255.0 10.10.10.6
```

**Trên R1-ISP:**

```bash
ip route 172.16.0.0 255.255.0.0 10.10.10.5
```

Việc cấu hình tuyến đường ở cả hai Router giúp bảo đảm có đường đi đến mạng đích và đường phản hồi về mạng nguồn.

## 6. Kiểm tra và xác minh kết nối

Các lệnh được sử dụng trong quá trình kiểm tra:

| Lệnh | Mục đích |
|---|---|
| `show ip interface brief` | Kiểm tra trạng thái các interface |
| `show ip route` | Kiểm tra bảng định tuyến |
| `ipconfig` | Kiểm tra cấu hình IP trên máy tính |
| `ping` | Kiểm tra khả năng kết nối giữa các thiết bị |
| `tracert` | Theo dõi đường đi của gói tin đến đích |

### Các bài kiểm tra chính

| Mã | Nội dung kiểm tra | Kết quả mong đợi |
|---|---|---|
| T1 | Kiểm tra IP bằng `ipconfig` | IP, Mask và Gateway chính xác |
| T2 | Ping Default Gateway | Kết nối thành công |
| T3 | Ping R1-ISP | Kết nối thành công |
| T4 | Ping Eagle Server | Kết nối thành công |
| T5 | Tracert đến Eagle Server | Quan sát được các hop |
| T6 | Ping giữa hai máy trong cùng Pod | Kết nối thành công |
| T7 | Kiểm tra bảng định tuyến | Có Connected và Static Route phù hợp |

*Kết quả PASS/FAIL thực tế được ghi nhận trong báo cáo và các ảnh minh chứng. Không mặc định các bài kiểm tra đã thành công khi chưa thực hiện.*

## 7. Simulation Mode

Sử dụng Simulation Mode trong Cisco Packet Tracer để quan sát quá trình truyền và nhận dữ liệu giữa máy tính và Eagle Server.

Các giao thức và thành phần được quan sát:

- **ARP (Address Resolution Protocol):** Tìm địa chỉ MAC tương ứng với địa chỉ IP trong mạng cục bộ.
- **ICMP Echo Request:** Gói tin yêu cầu kiểm tra kết nối.
- **ICMP Echo Reply:** Gói tin phản hồi từ thiết bị đích.
- **Routing:** Quá trình Router lựa chọn đường đi dựa trên bảng định tuyến.

Đường truyền cần quan sát:

`PC → Switch → R2-Central → R1-ISP → Eagle Server`

## 8. Thư mục minh chứng (Evidence)

Thư mục `evidence/` lưu trữ các ảnh chụp màn hình trong quá trình thực hiện bài lab. Các ảnh được đánh số và đặt tên theo nội dung để thuận tiện cho việc theo dõi, đối chiếu và chấm điểm.

| STT | Tên file | Nội dung minh chứng |
|---|---|---|
| 01 | `01-network-topology.png` | Toàn bộ sơ đồ mạng trên Packet Tracer |
| 02 | `02-serial-connection.png` | Kết nối Serial giữa R1-ISP và R2-Central |
| 03 | `03-lan-and-server.png` | Switch, các máy tính và Eagle Server |
| 04 | `04-r1-isp-configuration.png` | Cấu hình Router R1-ISP |
| 05 | `05-r2-central-configuration.png` | Cấu hình Router R2-Central |
| 06 | `06-routing-table.png` | Kết quả lệnh `show ip route` |
| 07 | `07-ipconfig.png` | Kết quả kiểm tra cấu hình IP bằng `ipconfig` |
| 08 | `08-ping-gateway.png` | Kết quả ping Default Gateway |
| 09 | `09-ping-r1-isp.png` | Kết quả ping Router R1-ISP |
| 10 | `10-ping-eagle-server.png` | Kết quả ping Eagle Server |
| 11 | `11-traceroute.png` | Kết quả lệnh `tracert` |
| 12 | `12-ping-within-pod.png` | Ping giữa hai máy tính trong cùng Pod |
| 13 | `13-router-routing-tables.png` | Bảng định tuyến của cả R1 và R2 |
| 14 | `14-simulation-arp-icmp.png` | Simulation Mode, quá trình ARP và ICMP |

## 9. Cấu trúc Repository

```text
NDLab01-Packet-Tracer/
│
├── README.md
│
├── NDLab01_HoTen.pkt
│
├── report/
│   └── NDLab01_Report.pdf
│
└── evidence/
    ├── 01-network-topology.png
    ├── 02-serial-connection.png
    ├── 03-lan-and-server.png
    ├── 04-r1-isp-configuration.png
    ├── 05-r2-central-configuration.png
    ├── 06-routing-table.png
    ├── 07-ipconfig.png
    ├── 08-ping-gateway.png
    ├── 09-ping-r1-isp.png
    ├── 10-ping-eagle-server.png
    ├── 11-traceroute.png
    ├── 12-ping-within-pod.png
    ├── 13-router-routing-tables.png
    └── 14-simulation-arp-icmp.png
```

**Lưu ý:** Tên file trong cấu trúc trên là tên đề xuất. Hãy sử dụng đúng tên file thực tế khi lưu ảnh và bảo đảm các đường dẫn trong README trùng khớp.

## 10. Kết luận

Thông qua bài thực hành NDLab01, người thực hiện có cơ hội tiếp cận quy trình thiết kế và triển khai một hệ thống mạng cơ bản bằng Cisco Packet Tracer.

Bài lab tập trung vào việc xây dựng topology, cấu hình địa chỉ IPv4, thiết lập Static Routing, kiểm tra kết nối giữa các mạng và quan sát quá trình truyền dữ liệu bằng Simulation Mode.

Các kết quả kiểm tra và hình ảnh minh chứng được lưu trữ trong repository nhằm phục vụ việc theo dõi quá trình thực hiện, đánh giá và hoàn thiện bài báo cáo.

---

**Công cụ thực hiện:** Cisco Packet Tracer  
**Bài thực hành:** NDLab01 – Packet Tracer Network Implementation  
**Sinh viên thực hiện:** Lê Tâm
**MSSV:** 19110432 
**Môn học:** Thiết kế mạng