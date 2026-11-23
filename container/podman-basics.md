---
title: Podman cơ bản và chạy container như service (Quadlet)
tags: [howto, container, podman, systemd]
product: Podman 4.4+ (RHEL 9)
source: https://docs.podman.io/en/latest/
reliability: unverified
updated: 2026-11-23
owner: lovelybugf
---
**TL;DR:** Podman chạy container không cần daemon và chạy được bằng user thường (rootless), cú pháp lệnh tương thích Docker. Để container tự chạy cùng hệ thống, khai báo file Quadlet `.container` cho systemd thay vì dùng `podman generate systemd` (đã deprecated).

## Lệnh cơ bản

```bash
podman run -d --name web -p 8080:80 <IMAGE>
podman ps -a
podman logs -f web
podman exec -it web /bin/sh
podman stop web
podman rm web
podman images
podman rmi <IMAGE>
```

## Rootless — điểm cần nhớ

- Storage của user ở `~/.local/share/containers/`; image của root và của user tách biệt.
- User thường không mở được cổng < 1024 trên host; dùng cổng cao (8080) hoặc chạy bằng root.
- Cần dải `subuid`/`subgid` cho user (`/etc/subuid`, `/etc/subgid`).

## Volume và SELinux

```bash
podman run -d --name db -v /srv/db:/var/lib/data:Z <IMAGE>
```

`:Z` gắn nhãn SELinux riêng cho container này; `:z` gắn nhãn dùng chung cho nhiều container. Thiếu nhãn → container bị "permission denied" dù quyền file đúng.

## Chạy như service với Quadlet

File `~/.config/containers/systemd/web.container` (rootless) hoặc `/etc/containers/systemd/web.container` (root):

```ini
[Unit]
Description=Web container

[Container]
Image=<IMAGE>
PublishPort=8080:80

[Install]
WantedBy=default.target
```

```bash
systemctl --user daemon-reload          # root: bỏ --user, WantedBy=multi-user.target
systemctl --user start web.service
loginctl enable-linger <USER>           # service rootless chạy cả khi user không đăng nhập
```

Unit do Quadlet sinh ra không `enable` được bằng lệnh; mục `[Install]` trong file `.container` quyết định việc tự khởi động.

## Liên quan

- [Image và registry](image-and-registry.md)
- [systemd cơ bản](../linux/systemd-basics.md)

## Nguồn

- Podman documentation; podman-systemd.unit(5) — https://docs.podman.io/en/latest/
- Trang mẫu do AI soạn 2026-11-23, chưa chạy kiểm chứng (`unverified`).
