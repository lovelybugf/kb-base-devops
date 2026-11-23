# Linux

> Quản trị Linux nói chung (RHEL và tương thích): systemd, lưu trữ, mạng, bảo mật cơ bản.

Phần riêng của sản phẩm RHEL (subscription, Leapp, vòng đời phiên bản) nằm ở [kb-redhat/rhel](../../kb-redhat/rhel/_index.md).

## Trang

<!-- AUTO-INDEX START -->
- [LVM — tạo và mở rộng volume](storage-lvm.md) — LVM gom đĩa vật lý (PV) thành nhóm (VG) rồi cắt thành ổ logic (LV); tăng dung lượng khi đang chạy bằng `lvextend -r`, lệnh này mở rộng luôn filesystem.
- [systemd cơ bản — quản lý service và xem log](systemd-basics.md) — Dùng `systemctl` để bật, tắt, xem trạng thái service và `journalctl -u <UNIT>` để xem log.
<!-- AUTO-INDEX END -->
