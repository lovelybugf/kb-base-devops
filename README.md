# kb-base-devops

Kho tri thức nền tảng DevOps (tiếng Việt): Linux, container, Kubernetes, Git/GitOps, cloud.

- Bắt đầu từ [index.md](index.md) → `_index.md` của chủ đề.
- Viết trang theo chuẩn [kb-core](https://github.com/lovelybugf/kb-core/blob/main/index.md); tạo trang mới:

```bash
python kb-core/tools/new_page.py kb-base-devops/linux/<ten-trang> "Tiêu đề" --tags howto,linux
python kb-core/tools/kb_index.py kb-base-devops
python kb-core/tools/kb_lint.py kb-base-devops
```

Các trang mẫu hiện có là `unverified` (do AI soạn) — kiểm chứng rồi nâng `reliability`.
