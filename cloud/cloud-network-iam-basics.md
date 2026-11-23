---
title: Mạng và IAM trên cloud — khái niệm chung
tags: [concept, cloud, networking, iam]
source: https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html
reliability: unverified
updated: 2026-11-23
owner: lovelybugf
---
**TL;DR:** Mạng cloud xoay quanh mạng ảo riêng (VPC/VNet) chia thành subnet public và private, kiểm soát luồng truy cập bằng security group hoặc NSG. IAM quyết định ai được làm gì thông qua danh tính, vai trò và policy, theo nguyên tắc quyền tối thiểu.

## Tên gọi tương ứng giữa các nhà cung cấp

| Khái niệm | AWS | Azure | Google Cloud |
|---|---|---|---|
| Mạng ảo riêng | VPC | Virtual Network (VNet) | VPC network |
| Tường lửa gắn với máy/NIC | Security Group | Network Security Group | Firewall rules |
| Ra Internet từ subnet private | NAT Gateway | NAT Gateway | Cloud NAT |
| Kết nối về on-premise | Site-to-Site VPN, Direct Connect | VPN Gateway, ExpressRoute | Cloud VPN, Interconnect |
| Danh tính cho ứng dụng | IAM Role | Managed Identity | Service Account |
| Nhật ký thao tác | CloudTrail | Activity Log | Cloud Audit Logs |

## Mạng — nguyên tắc thiết kế

- Chọn dải địa chỉ riêng (RFC 1918) không trùng với mạng nội bộ và các VPC khác sẽ kết nối.
- Subnet public chỉ chứa load balancer, bastion; máy ứng dụng và database ở subnet private.
- Mặc định chặn tất cả, chỉ mở cổng cần thiết, nguồn cụ thể (không mở `0.0.0.0/0` cho SSH/RDP).
- Ghi lại sơ đồ mạng bằng placeholder trong kho, không ghi dải IP thật của khách hàng.

## IAM — nguyên tắc

- Không dùng tài khoản root/owner cho việc hằng ngày; bật MFA cho mọi người dùng.
- Cấp quyền theo vai trò (role/group), không gán trực tiếp từng người.
- Ứng dụng dùng danh tính được quản lý và thông tin xác thực ngắn hạn thay vì access key cố định.
- Quyền tối thiểu, rà soát định kỳ; bật nhật ký thao tác và giữ đủ lâu theo yêu cầu tuân thủ.

## Liên quan

- [Mô hình dịch vụ cloud](cloud-service-models.md)

## Nguồn

- AWS, What is Amazon VPC — https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html
- Microsoft, Azure Virtual Network overview — https://learn.microsoft.com/azure/virtual-network/virtual-networks-overview
- Trang mẫu do AI soạn 2026-11-23, chưa kiểm chứng (`unverified`).
