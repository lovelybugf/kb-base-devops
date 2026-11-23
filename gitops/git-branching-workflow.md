---
title: Quy trình nhánh Git (trunk-based) và commit message
tags: [howto, git, gitops]
source: https://www.conventionalcommits.org/en/v1.0.0/
reliability: unverified
updated: 2026-11-23
owner: lovelybugf
---
**TL;DR:** Dùng mô hình trunk-based: `main` luôn ở trạng thái triển khai được, mọi thay đổi đi qua nhánh ngắn ngày và pull/merge request có review. Commit message theo Conventional Commits để đọc lịch sử và sinh changelog dễ.

## Vòng đời một thay đổi

```bash
git switch main
git pull --ff-only
git switch -c feat/<ten-ngan>
# sửa file, commit nhỏ và có ý nghĩa
git add -p
git commit -m "feat(api): thêm endpoint health"
git push -u origin feat/<ten-ngan>
# mở PR/MR → review → CI xanh → squash merge
git switch main
git pull --ff-only
git branch -d feat/<ten-ngan>
```

## Conventional Commits

Dạng: `<type>(<scope>): <mô tả>`; thay đổi phá vỡ tương thích thêm `!` hoặc dòng `BREAKING CHANGE:` ở thân.

| type | Khi nào |
|---|---|
| `feat` | Tính năng mới |
| `fix` | Sửa lỗi |
| `docs` | Chỉ tài liệu (kho tri thức dùng nhiều nhất) |
| `refactor` | Đổi cấu trúc, không đổi hành vi |
| `test` | Thêm/sửa test |
| `ci` | Pipeline |
| `chore` | Việc lặt vặt: cập nhật phụ thuộc, mục lục |

## Quy tắc

- Bảo vệ `main`: bắt buộc PR/MR, review và CI đạt; cấm force push.
- Nhánh sống ngắn (≤ vài ngày); thay đổi lớn chia nhỏ hoặc dùng feature flag.
- Chỉ rebase nhánh của mình chưa chia sẻ; không viết lại lịch sử đã push lên nhánh chung.
- Đánh dấu phát hành bằng tag có chú thích theo SemVer: `git tag -a v1.2.0 -m "Phát hành 1.2.0"`.
- Không commit bí mật; lỡ commit → đổi bí mật đó ngay, xoá khỏi lịch sử chỉ là bước phụ.

## Liên quan

- [Argo CD cơ bản](argocd-basics.md)
- Quy ước commit cho các kho tri thức: [kb-core/standards/naming.md](https://github.com/lovelybugf/kb-core/blob/main/standards/naming.md)

## Nguồn

- Conventional Commits 1.0.0 — https://www.conventionalcommits.org/en/v1.0.0/
- Pro Git — https://git-scm.com/book
- Trang mẫu do AI soạn 2026-11-23, chưa chạy kiểm chứng (`unverified`).
