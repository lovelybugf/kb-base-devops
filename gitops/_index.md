# GitOps

> Git workflow, CI/CD và triển khai theo GitOps (Argo CD, Flux).

## Trang

<!-- AUTO-INDEX START -->
- [Argo CD cơ bản — đồng bộ cluster từ Git](argocd-basics.md) — Argo CD liên tục so sánh trạng thái khai báo trong Git với trạng thái thật trên cluster rồi đồng bộ hai bên; mỗi ứng dụng là một tài nguyên `Application` trỏ tới repo, đường dẫn và cluster đích.
- [Quy trình nhánh Git (trunk-based) và commit message](git-branching-workflow.md) — Dùng mô hình trunk-based: `main` luôn ở trạng thái triển khai được, mọi thay đổi đi qua nhánh ngắn ngày và pull/merge request có review.
- [GitHub Actions bị website chặn (HTTP 403 từ Cloudflare)](github-actions-blocked-by-site.md) — Job trên runner do GitHub host gọi một website và nhận HTTP 403 kèm trang HTML của Cloudflare, trong khi cùng script chạy từ mạng trong nước thì được: website đang chặn theo IP của trung tâm dữ liệu.
<!-- AUTO-INDEX END -->
