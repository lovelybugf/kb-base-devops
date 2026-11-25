---
title: Sửa PowerPoint/Word bằng PowerShell 5.1 và Office COM
tags: [howto, windows]
product: Windows PowerShell 5.1, Microsoft Office (COM)
source: "Phiên Claude Code 2026-11-25 (dc97f4ce)"
reliability: internal-tested
updated: 2026-11-25
owner: lovelybugf
---
**TL;DR:** Trên Windows có Office, sửa .pptx/.docx tự động được bằng PowerShell 5.1 + COM mà không cần Python. Ba bẫy chính: file .ps1 phải thuần ASCII (chuỗi tiếng Việt để trong file JSON UTF-8), thuộc tính số của COM phải ép `[single]`, và luôn chạy thử trên bản sao rồi xuất ảnh kiểm tra trước khi sửa file thật.

## Mở file

```powershell
$ppt  = New-Object -ComObject PowerPoint.Application
$pres = $ppt.Presentations.Open('<PATH>\copy.pptx', $false, $false, $false)   # ReadOnly, Untitled, WithWindow
$word = New-Object -ComObject Word.Application
$doc  = $word.Documents.Open('<PATH>\copy.docx')
```

File đang mở trong PowerPoint của người dùng: gắn vào bằng `[Runtime.InteropServices.Marshal]::GetActiveObject('PowerPoint.Application')` và **không** gọi `Quit()`.

## Bẫy thường gặp

- **Script phải thuần ASCII:** PowerShell 5.1 đọc .ps1 không có BOM theo bảng mã ANSI, tiếng Việt trong script bị hỏng. Để chuỗi trong file JSON UTF-8:

```powershell
$s = [System.IO.File]::ReadAllText('<PATH>\strings.json', [System.Text.Encoding]::UTF8) | ConvertFrom-Json
```

- **Thuộc tính số cần `[single]`:** `$shape.Left = [single]120`; gán số nguyên trần báo "Specified cast is not valid".
- **Biến không phân biệt hoa/thường:** biến cục bộ trùng tên một tham số `[string]` của script bị ép thành chuỗi — đặt tên khác.
- **Script chèn slide không idempotent:** chạy lại sẽ nhân đôi slide — chạy lại trên một bản sao mới.

## Quy trình an toàn

1. Chép file gốc ra bản sao, chạy script trên bản sao.
2. Xuất slide ra ảnh để xem bằng mắt: `$pres.Slides(1).Export('<PATH>\s1.png', 'PNG')`.
3. Kiểm tra chữ tràn khung trên mọi slide đã sửa.
4. Áp script vào file thật (hoặc chép bản sao đã duyệt đè lên), lưu, đóng.

## Lệnh trong slide/tài liệu phải copy chạy được

- Không có dấu nháy cong (“ ” ‘ ’) hay gạch en/em (– —) trong khối lệnh — AutoCorrect của Office tự thay khi gõ; chèn lệnh từ file thay vì gõ tay.
- Không có khoảng trắng sau `\` ở cuối dòng của lệnh nhiều dòng.
- Khối lệnh dùng phông monospace.

## Liên quan

- [GitHub Actions bị website chặn](../gitops/github-actions-blocked-by-site.md) — có ví dụ PowerShell cho Task Scheduler

## Nguồn

- Phiên Claude Code 2026-11-25 (dc97f4ce): sửa deck 53 và 39 slide, Word mở file đầu ra không lỗi; kiểm ký tự trong khối lệnh trên deck.
