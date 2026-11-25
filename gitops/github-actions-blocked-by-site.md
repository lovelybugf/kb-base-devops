---
title: GitHub Actions bị website chặn (HTTP 403 từ Cloudflare)
tags: [troubleshooting, cicd, gitops]
product: GitHub Actions (runner do GitHub host)
source: "Phiên Claude Code 2026-11-25 (b3031a33)"
reliability: internal-tested
updated: 2026-11-25
owner: lovelybugf
---
**TL;DR:** Job trên runner do GitHub host gọi một website và nhận HTTP 403 kèm trang HTML của Cloudflare, trong khi cùng script chạy từ mạng trong nước thì được: website đang chặn theo IP của trung tâm dữ liệu. Không lách chặn; bỏ bước đó khỏi lịch trên GitHub và chạy nó từ mạng được phép (self-hosted runner, hoặc lịch trên máy cục bộ).

## Triệu chứng

- Bước gọi website lỗi `HTTP 403`, thân phản hồi là HTML bắt đầu bằng `<!DOCTYPE html>` và `<!--[if lt IE 7]> <html class="no-js ie6 oldie"` — mẫu trang chặn của Cloudflare ("Sorry, you have been blocked").
- Lỗi lặp lại ở mọi lần chạy theo lịch; các bước không gọi website đó vẫn xanh.

## Chẩn đoán

1. Mở log của bước lỗi (Actions → run → job → bước) và xem đoạn đầu phản hồi.
2. Chạy đúng script đó, cùng User-Agent, từ một máy trong mạng mà website phục vụ, ở chế độ không ghi dữ liệu:

```bash
python tools/update_daily.py --sources <NHOM> --dry-run
```

| Kết quả từ máy cục bộ | Kết luận |
|---|---|
| Thành công | Website chặn theo IP/ASN/quốc gia của runner (trung tâm dữ liệu ở nước ngoài) |
| Cũng 403 | Website chặn client tự động nói chung — không muốn bị truy cập bằng script |

3. Đọc `robots.txt` và điều khoản của website để biết họ cho phép truy cập tự động đến đâu.

## Nguyên nhân

Website bật WAF/bot management của Cloudflare chặn dải IP trung tâm dữ liệu; runner do GitHub host chạy trên hạ tầng cloud ở nước ngoài. Đây là chính sách của chủ website, không phải lỗi workflow.

## Cách xử lý

- Không lách chặn: không giả User-Agent trình duyệt, không dùng proxy hay IP dân cư, không tự giải challenge.
- Bỏ bước đó khỏi lịch trên GitHub Actions (để workflow không đỏ mỗi ngày), ghi lý do trong comment workflow và tài liệu nguồn.
- Chạy bước đó từ mạng được phép, tần suất thấp, User-Agent định danh rõ: self-hosted runner đặt trong mạng đó, hoặc lịch trên máy cục bộ (Windows Task Scheduler, cron) theo chuỗi pull → chạy → kiểm tra → commit → push.
- Website chặn cả truy cập tự động từ máy cục bộ → dừng dùng nguồn đó, tìm nguồn có API hoặc giấy phép.

Ví dụ Windows Task Scheduler chạy script Python không hiện cửa sổ, 2 lần/ngày thứ 2–thứ 7, chạy bù khi máy bật lại (PowerShell, chạy bằng chính user có thông tin đăng nhập Git):

```powershell
$action = New-ScheduledTaskAction -Execute "<PYTHON_DIR>\pythonw.exe" -Argument '-I "<REPO>\tools\local_update.py" --sources <NHOM>' -WorkingDirectory "<REPO>"
$days = 'Monday','Tuesday','Wednesday','Thursday','Friday','Saturday'
$t1 = New-ScheduledTaskTrigger -Weekly -DaysOfWeek $days -At '11:30'
$t2 = New-ScheduledTaskTrigger -Weekly -DaysOfWeek $days -At '17:30'
$settings = New-ScheduledTaskSettingsSet -StartWhenAvailable -AllowStartIfOnBatteries -ExecutionTimeLimit (New-TimeSpan -Minutes 10) -MultipleInstances IgnoreNew
$principal = New-ScheduledTaskPrincipal -UserId "$env:USERDOMAIN\$env:USERNAME" -LogonType Interactive -RunLevel Limited
Register-ScheduledTask -TaskName '<TEN_TASK>' -TaskPath '\<THU_MUC>\' -Action $action -Trigger $t1,$t2 -Settings $settings -Principal $principal
```

- `pythonw.exe` không bật cửa sổ console; script phải tự ghi log ra file vì `sys.stdout` là `None`.
- `-LogonType Interactive`: task chạy trong phiên đăng nhập của user nên dùng được thông tin đăng nhập Git đã lưu (Git Credential Manager).
- Script chạy `git pull --rebase --autostash` trước khi ghi để không xung đột với commit của CI.

## Phòng ngừa

- Trước khi đưa một nguồn web lên runner do GitHub host, chạy thử một lần bằng `workflow_dispatch` và đọc `robots.txt`/điều khoản.
- Tách nhóm nguồn theo nơi chạy được (CI hay máy cục bộ) ngay trong script, ví dụ bằng tham số `--sources`.

## Liên quan

- [Quy trình nhánh Git](git-branching-workflow.md)
- Trường hợp thực tế: [nguồn `sjc` của kb-market-vn](https://github.com/lovelybugf/kb-market-vn/blob/main/sources.md)

## Nguồn

- Phiên Claude Code 2026-11-25 (b3031a33): 4/4 lần chạy theo lịch trên runner GitHub nhận 403 từ Cloudflare; cùng script từ máy ở Việt Nam chạy thành công; task Task Scheduler như trên chạy thử thành công.
