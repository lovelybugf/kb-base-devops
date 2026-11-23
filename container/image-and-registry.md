---
title: Container image và registry
tags: [reference, container-image, registry, podman]
product: Podman, Skopeo (RHEL 9)
source: https://github.com/containers/image/blob/main/docs/containers-registries.conf.5.md
reliability: unverified
updated: 2026-11-23
owner: lovelybugf
---
**TL;DR:** Tên image đầy đủ có dạng `registry/namespace/repository:tag`, và tag có thể bị ghi đè nên production nên ghim theo digest `@sha256:...`. Dùng `skopeo` để xem metadata và sao chép image giữa các registry mà không cần pull về máy.

## Cấu trúc tên image

```text
registry.redhat.io/ubi9/ubi:latest
└─ registry ──────┘ └ns┘ └repo┘ └tag┘
<REGISTRY>/<NAMESPACE>/<REPO>@sha256:<DIGEST>     # ghim tuyệt đối
```

- Luôn ghi tên đầy đủ có registry. Tên ngắn (`ubi9`) được tra theo `unqualified-search-registries` trong `/etc/containers/registries.conf` — dễ kéo nhầm image từ registry khác.
- Tag (`:9.4`, `:latest`) là con trỏ di động; digest là định danh nội dung bất biến.

## Lệnh hay dùng

```bash
podman login <REGISTRY>
podman pull <REGISTRY>/<NAMESPACE>/<REPO>:<TAG>
podman build -t <REGISTRY>/<NAMESPACE>/<REPO>:<TAG> -f Containerfile .
podman push <REGISTRY>/<NAMESPACE>/<REPO>:<TAG>
podman image inspect --format '{{.Digest}}' <REGISTRY>/<NAMESPACE>/<REPO>:<TAG>
skopeo inspect docker://<REGISTRY>/<NAMESPACE>/<REPO>:<TAG>
skopeo list-tags docker://<REGISTRY>/<NAMESPACE>/<REPO>
skopeo copy docker://<SRC_REGISTRY>/<NAMESPACE>/<REPO>:<TAG> docker://<DST_REGISTRY>/<NAMESPACE>/<REPO>:<TAG>
```

## Lưu ý bảo mật

- Thông tin đăng nhập registry lưu trong `${XDG_RUNTIME_DIR}/containers/auth.json` (rootless) — không commit file này.
- Chỉ dùng image từ registry tin cậy; kiểm tra chữ ký khi registry hỗ trợ (`policy.json`).
- Môi trường không có Internet: đồng bộ image về registry nội bộ hoặc dùng Satellite làm nơi phân phối nội dung container — xem [Satellite](https://github.com/lovelybugf/kb-redhat/blob/main/satellite/_index.md).

## Liên quan

- [Podman cơ bản](podman-basics.md)

## Nguồn

- containers-registries.conf(5) — https://github.com/containers/image/blob/main/docs/containers-registries.conf.5.md
- skopeo(1) — https://github.com/containers/skopeo
- Trang mẫu do AI soạn 2026-11-23, chưa chạy kiểm chứng (`unverified`).
