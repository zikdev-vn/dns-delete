# Block ad modun linux

## **Yêu cầu hệ thống**
- Box chạy Linux (Debian/Ubuntu là lý tưởng vì Pi-hole hỗ trợ tốt).
- Quyền root để cài đặt phần mềm.
- Khả năng cấu hình router (để thay đổi DNS của mạng sang box Linux của bạn).

---

## **Cài đặt Pi-hole**

### 1. Cập nhật hệ thống
Chạy các lệnh sau để đảm bảo hệ thống của bạn được cập nhật:
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Cài đặt Pi-hole
Chạy lệnh sau để cài đặt Pi-hole:
```bash
curl -sSL https://install.pi-hole.net | bash
```
- Trong quá trình cài đặt, bạn sẽ được yêu cầu chọn DNS server (Google, Cloudflare, v.v.).
- Pi-hole sẽ tự động cấu hình và khởi động dịch vụ.

### 3. Cấu hình tường lửa (nếu cần)
Nếu bạn có tường lửa đang chạy, hãy mở cổng DNS (53):
```bash
sudo ufw allow 53/tcp
sudo ufw allow 53/udp
```

---

## **Cấu hình Router**
1. Truy cập vào trang quản lý của router (thường là `192.168.1.1` hoặc `192.168.0.1`).
2. Tìm mục cấu hình DNS.
3. Thay đổi DNS server thành IP của box Linux (ví dụ: `192.168.1.100`).

---

## **(Tuỳ chọn) Kích hoạt DHCP trên Pi-hole**
Nếu bạn muốn Pi-hole quản lý DHCP thay cho router:
1. Vào giao diện web quản trị của Pi-hole (http://<IP-của-box>/admin).
2. Bật DHCP trong phần **Settings > DHCP**.
3. Tắt DHCP trên router để tránh xung đột.

---

## **Kiểm tra và sử dụng**
1. Thử truy cập các trang web có nhiều quảng cáo để kiểm tra.
2. Truy cập giao diện quản trị của Pi-hole để theo dõi các truy vấn DNS và quảng cáo bị chặn.

---

## **Nâng cao**
- **Thêm danh sách chặn**: Bạn có thể thêm các danh sách chặn quảng cáo tùy chỉnh.
- **Tùy chỉnh danh sách trắng/đen**: Nếu một trang bị chặn nhầm, bạn có thể thêm nó vào danh sách trắng.

---
# xoay proxy 

```bash
//tao 500 proxy cua vieltel va vinaphon can pai mua goi da phien cua nha mang
:local vlanPrefix "macvlan"

:for iii from=1 to=500 do={
    :do {
        /interface macvlan add interface=ether1 name=($vlanPrefix . $iii)
    } on-error={
        :log warning "Failed to create macvlan for $vlanPrefix.$iii"
    }
}
// xoay proxy
:while (true) do={
    :if ($proxyIndex >= [:len $proxyList]) do={
        :set $proxyIndex 0
    }
    :local currentProxy ($proxyList->[$proxyIndex])

    # Áp dụng proxy hiện tại
    /ip proxy set address=($currentProxy->"address") port=($currentProxy->"port")
    :log info "Switched to proxy: $currentProxy"

    # Tăng index và chờ 5 phút
    :set $proxyIndex ($proxyIndex + 1)
    :delay 300s
}
```
