---
title: Mô hình dịch vụ cloud (IaaS, PaaS, SaaS) và trách nhiệm chia sẻ
tags: [concept, cloud]
source: https://csrc.nist.gov/pubs/sp/800/145/final
reliability: unverified
updated: 2026-11-23
owner: lovelybugf
---
**TL;DR:** IaaS cho thuê hạ tầng (máy ảo, mạng, đĩa), PaaS cho thuê nền tảng chạy ứng dụng, SaaS cho thuê phần mềm hoàn chỉnh. Càng lên cao nhà cung cấp quản lý càng nhiều, nhưng dữ liệu, tài khoản và phân quyền luôn là trách nhiệm của khách hàng.

## Ai quản lý phần nào

| Lớp | On-premise | IaaS | PaaS | SaaS |
|---|---|---|---|---|
| Dữ liệu, tài khoản, phân quyền | Khách hàng | Khách hàng | Khách hàng | Khách hàng |
| Ứng dụng | Khách hàng | Khách hàng | Khách hàng | Nhà cung cấp |
| Runtime, middleware | Khách hàng | Khách hàng | Nhà cung cấp | Nhà cung cấp |
| Hệ điều hành, vá lỗi OS | Khách hàng | Khách hàng | Nhà cung cấp | Nhà cung cấp |
| Ảo hoá, máy chủ, mạng, lưu trữ | Khách hàng | Nhà cung cấp | Nhà cung cấp | Nhà cung cấp |
| Trung tâm dữ liệu | Khách hàng | Nhà cung cấp | Nhà cung cấp | Nhà cung cấp |

## Ví dụ

- IaaS: máy ảo AWS EC2, Azure Virtual Machines, Google Compute Engine.
- PaaS: dịch vụ OpenShift được quản lý (ROSA, ARO, OpenShift Dedicated), Azure App Service.
- SaaS: Microsoft 365, Google Workspace.

## Mô hình triển khai

- Public cloud, private cloud, hybrid cloud (kết hợp on-premise + public), multi-cloud (nhiều nhà cung cấp).

## Tiêu chí chọn

- Mức kiểm soát cần có (cấu hình OS, kernel, mạng) ↔ công sức vận hành chấp nhận được.
- Yêu cầu tuân thủ, vị trí lưu dữ liệu, kết nối về hệ thống nội bộ.
- Mô hình chi phí (trả theo dùng vs cam kết dài hạn) và khả năng chuyển đổi nhà cung cấp.

## Liên quan

- [Mạng và IAM trên cloud](cloud-network-iam-basics.md)

## Nguồn

- NIST SP 800-145, The NIST Definition of Cloud Computing — https://csrc.nist.gov/pubs/sp/800/145/final
- Trang mẫu do AI soạn 2026-11-23, chưa kiểm chứng (`unverified`).
