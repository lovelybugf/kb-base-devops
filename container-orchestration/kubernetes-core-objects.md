---
title: Kubernetes — các đối tượng cốt lõi và lệnh kubectl
tags: [concept, kubernetes, container]
product: Kubernetes 1.30+
source: https://kubernetes.io/docs/concepts/
reliability: unverified
updated: 2026-11-23
owner: lovelybugf
---
**TL;DR:** Kubernetes chạy container trong Pod, nhưng gần như không tạo Pod trực tiếp mà khai báo Deployment (số bản sao, image) và Service (địa chỉ ổn định để truy cập các Pod). OpenShift xây trên các đối tượng này; phần riêng của OpenShift nằm ở kb-redhat.

## Đối tượng cốt lõi

| Đối tượng | Dùng để | Ghi chú |
|---|---|---|
| Pod | Đơn vị chạy nhỏ nhất, 1+ container chung mạng/volume | Pod chết không tự hồi sinh nếu không có controller |
| Deployment | Ứng dụng không trạng thái: số bản sao, rolling update, rollback | Quản lý ReplicaSet |
| StatefulSet | Ứng dụng có trạng thái: tên Pod cố định, volume riêng từng Pod | Database, hàng đợi |
| DaemonSet | Mỗi node một Pod | Agent log, giám sát |
| Job / CronJob | Việc chạy một lần / định kỳ | |
| Service | Địa chỉ + DNS ổn định trỏ tới nhóm Pod theo label | ClusterIP, NodePort, LoadBalancer |
| Ingress | Định tuyến HTTP(S) từ ngoài vào Service | OpenShift dùng Route |
| ConfigMap / Secret | Cấu hình / dữ liệu nhạy cảm cho Pod | Secret chỉ mã hoá base64, không phải mã hoá bảo mật |
| PersistentVolumeClaim | Yêu cầu lưu trữ bền vững | Cấp qua StorageClass |
| Namespace | Phân vùng tài nguyên, quyền, quota | OpenShift gọi là Project |

## Ví dụ Deployment + Service

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 2
  selector:
    matchLabels: {app: web}
  template:
    metadata:
      labels: {app: web}
    spec:
      containers:
        - name: web
          image: <REGISTRY>/<NAMESPACE>/<REPO>:<TAG>
          ports:
            - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: web
spec:
  selector: {app: web}
  ports:
    - port: 80
      targetPort: 8080
```

## Lệnh kubectl hay dùng

```bash
kubectl get pods -n <NAMESPACE> -o wide
kubectl describe pod <POD> -n <NAMESPACE>              # xem Events khi Pod lỗi
kubectl logs <POD> -n <NAMESPACE> -c <CONTAINER> --previous
kubectl apply -f deployment.yaml
kubectl rollout status deployment/web -n <NAMESPACE>
kubectl rollout undo deployment/web -n <NAMESPACE>
kubectl scale deployment/web --replicas=3 -n <NAMESPACE>
kubectl get events -n <NAMESPACE> --sort-by=.lastTimestamp
```

## Liên quan

- OpenShift (Route, Project, Operator, `oc`): [kb-redhat/ocp](../../kb-redhat/ocp/_index.md)
- Triển khai theo GitOps: [Argo CD cơ bản](../gitops/argocd-basics.md)
- [Podman cơ bản](../container/podman-basics.md)

## Nguồn

- Kubernetes Concepts — https://kubernetes.io/docs/concepts/
- Trang mẫu do AI soạn 2026-11-23, chưa chạy kiểm chứng (`unverified`).
