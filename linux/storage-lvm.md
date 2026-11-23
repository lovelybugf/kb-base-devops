---
title: LVM — tạo và mở rộng volume
tags: [howto, linux, lvm, storage]
product: RHEL 8/9
source: https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/configuring_and_managing_logical_volumes/index
reliability: unverified
updated: 2026-11-23
owner: lovelybugf
---
**TL;DR:** LVM gom đĩa vật lý (PV) thành nhóm (VG) rồi cắt thành ổ logic (LV); tăng dung lượng khi đang chạy bằng `lvextend -r`, lệnh này mở rộng luôn filesystem. XFS chỉ tăng được, không thu nhỏ được.

## Khái niệm

| Thành phần | Ý nghĩa | Lệnh xem |
|---|---|---|
| PV (physical volume) | Đĩa hoặc phân vùng đã khởi tạo cho LVM | `pvs` |
| VG (volume group) | Kho dung lượng gom từ nhiều PV | `vgs` |
| LV (logical volume) | Ổ logic cắt từ VG, chứa filesystem | `lvs` |

```bash
lsblk
pvs; vgs; lvs
df -hT
```

## Tạo volume mới

Chạy bằng root:

```bash
pvcreate /dev/<DISK>
vgcreate <VG> /dev/<DISK>
lvcreate -n <LV> -L 50G <VG>          # hoặc: -l 100%FREE
mkfs.xfs /dev/<VG>/<LV>
mkdir -p <MOUNTPOINT>
echo "/dev/<VG>/<LV> <MOUNTPOINT> xfs defaults 0 0" >> /etc/fstab
systemctl daemon-reload
mount -a
```

## Mở rộng khi đang chạy

1. Thêm dung lượng cho VG — một trong hai cách:

```bash
# a) Gắn đĩa mới
pvcreate /dev/<NEW_DISK>
vgextend <VG> /dev/<NEW_DISK>

# b) Đã tăng kích thước đĩa ảo chứa PV (PV nằm trực tiếp trên đĩa, không phân vùng)
pvresize /dev/<DISK>
```

2. Tăng LV và filesystem cùng lúc (`-r` gọi `xfs_growfs` hoặc `resize2fs`):

```bash
lvextend -r -L +20G /dev/<VG>/<LV>     # hoặc: -l +100%FREE
df -hT <MOUNTPOINT>
```

## Lưu ý

- XFS (mặc định trên RHEL) không thu nhỏ được; muốn giảm phải backup, tạo lại, restore.
- PV nằm trên phân vùng: phải mở rộng phân vùng trước (`growpart`, gói `cloud-utils-growpart`) rồi mới `pvresize`.
- Snapshot LVM cần dung lượng trống trong VG; snapshot đầy sẽ hỏng.
- Thư mục hay phải mở rộng trên Satellite: `/var/lib/pulp` — xem [download policy](https://github.com/lovelybugf/kb-redhat/blob/main/satellite/download-policy.md).

## Liên quan

- [systemd cơ bản](systemd-basics.md)

## Nguồn

- RHEL 9, Configuring and managing logical volumes — https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/configuring_and_managing_logical_volumes/index
- Trang mẫu do AI soạn 2026-11-23, chưa chạy kiểm chứng (`unverified`).
