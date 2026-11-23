# CLAUDE.md — kb-base-devops

Phạm vi: nền tảng DevOps trung lập sản phẩm — Linux, container, Kubernetes upstream, Git/GitOps, khái niệm cloud. Không chứa nội dung riêng sản phẩm Red Hat (ở kb-redhat).

## Thứ tự đọc
1. File này → [index.md](index.md) → `_index.md` của chủ đề → mở 2–3 trang cần thiết.
2. Không đọc toàn bộ kho; chỉ mở `raw/` khi cần đối chiếu tài liệu gốc.

## Quy ước
- Chuẩn chung ở [kb-core](../kb-core/index.md); tag chỉ lấy từ `kb-core/standards/tags.md`.
- Mỗi trang 1 chủ đề, ≤ 500 dòng, frontmatter + dòng `**TL;DR:**` ngay đầu.
- Lệnh trong code block; giá trị môi trường dùng placeholder `<HOST_FQDN>`, `<VG>`, `<NAMESPACE>`.
- Trang mẫu hiện là `reliability: unverified` (AI soạn): khi trả lời phải nói rõ và khuyên kiểm lại trước khi chạy trên production.
- Khi trả lời: nêu đường dẫn trang đã dùng.
- Sửa trang → cập nhật `updated`, chạy `kb_index.py` + `kb_lint.py` (trong kb-core/tools), ghi 1 dòng vào [log.md](log.md).

## Kho liên quan
- Cổng vào: [../kb-hub/index.md](../kb-hub/index.md)
- OpenShift, RHEL, Satellite, IdM, AAP: [../kb-redhat/index.md](../kb-redhat/index.md) — ở đây chỉ đặt link.

## Không được
- Không lưu mật khẩu, API key, token, IP/hostname nội bộ của khách hàng.
- Không viết lại nội dung riêng sản phẩm Red Hat ở đây — đặt link sang kb-redhat.
- Không sửa/xoá dòng cũ trong `log.md`.
