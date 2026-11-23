---
title: systemd cơ bản — quản lý service và xem log
tags: [howto, linux, systemd]
product: systemd (RHEL 8/9)
source: https://www.freedesktop.org/software/systemd/man/latest/systemctl.html
reliability: unverified
updated: 2026-11-23
owner: lovelybugf
---
**TL;DR:** Dùng `systemctl` để bật, tắt, xem trạng thái service và `journalctl -u <UNIT>` để xem log. Muốn đổi cấu hình unit thì tạo drop-in bằng `systemctl edit <UNIT>`, không sửa file trong `/usr/lib/systemd/system`.

## Lệnh hay dùng

```bash
systemctl status <UNIT>                  # trạng thái, PID, vài dòng log gần nhất
systemctl start <UNIT>                   # stop | restart | reload tương tự
systemctl enable --now <UNIT>            # bật cùng hệ thống và chạy ngay
systemctl disable --now <UNIT>           # tắt và dừng ngay
systemctl is-enabled <UNIT>
systemctl is-active <UNIT>
systemctl --failed                       # unit đang lỗi
systemctl daemon-reload                  # bắt buộc sau khi sửa tay file unit
```

## Vị trí unit file

| Đường dẫn | Ý nghĩa |
|---|---|
| `/usr/lib/systemd/system/` | Do gói RPM cài — không sửa, bị ghi đè khi cập nhật gói |
| `/etc/systemd/system/` | Quản trị viên tạo hoặc ghi đè — ưu tiên cao nhất |
| `/etc/systemd/system/<UNIT>.d/*.conf` | Drop-in: ghi đè từng tham số của unit |
| `/run/systemd/system/` | Unit sinh lúc chạy, mất khi reboot |

## Ghi đè cấu hình bằng drop-in

```bash
systemctl edit <UNIT>        # lưu vào /etc/systemd/system/<UNIT>.d/override.conf
systemctl cat <UNIT>         # xem unit gốc + mọi drop-in đang áp dụng
systemctl restart <UNIT>
```

Ví dụ nội dung drop-in tăng giới hạn số file mở:

```ini
[Service]
LimitNOFILE=65536
```

## Xem log với journalctl

```bash
journalctl -u <UNIT> -b                      # log của unit từ lần khởi động hiện tại
journalctl -u <UNIT> -f                      # theo dõi trực tiếp
journalctl -u <UNIT> --since "1 hour ago"
journalctl -xeu <UNIT>                       # lỗi gần nhất kèm giải thích
journalctl -p err -b                         # mọi log mức error từ khi boot
```

## Timer (thay cho cron)

```bash
systemctl list-timers --all
systemctl enable --now <NAME>.timer
systemctl disable --now <NAME>.timer
```

Một timer gồm `<NAME>.timer` (lịch chạy) và `<NAME>.service` (việc cần làm). Ví dụ tắt job định kỳ của Insights client: xem [Tắt Red Hat Lightspeed](../../kb-redhat/satellite/disable-lightspeed.md).

## Liên quan

- [LVM — tạo và mở rộng volume](storage-lvm.md)

## Nguồn

- systemctl(1), systemd.unit(5), journalctl(1) — https://www.freedesktop.org/software/systemd/man/latest/
- Trang mẫu do AI soạn 2026-11-23, chưa chạy kiểm chứng (`unverified`).
