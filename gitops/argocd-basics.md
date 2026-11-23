---
title: Argo CD cơ bản — đồng bộ cluster từ Git
tags: [concept, gitops, argocd, kubernetes]
product: Argo CD 2.x
source: https://argo-cd.readthedocs.io/en/stable/
reliability: unverified
updated: 2026-11-23
owner: lovelybugf
---
**TL;DR:** Argo CD liên tục so sánh trạng thái khai báo trong Git với trạng thái thật trên cluster rồi đồng bộ hai bên; mỗi ứng dụng là một tài nguyên `Application` trỏ tới repo, đường dẫn và cluster đích. Muốn đổi hệ thống thì sửa Git, không sửa tay trên cluster.

## Nguyên tắc GitOps

1. Khai báo (declarative): trạng thái mong muốn mô tả bằng YAML/Helm/Kustomize.
2. Có phiên bản: lưu trong Git, mọi thay đổi qua commit + review.
3. Kéo tự động: agent trong cluster (Argo CD, Flux) tự kéo thay đổi, CI không cần quyền vào cluster.
4. Hoà giải liên tục: lệch giữa Git và cluster được phát hiện và sửa.

## Application tối thiểu

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: <APP>
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://<GIT_SERVER>/<ORG>/<REPO>.git
    targetRevision: main
    path: deploy/overlays/prod
  destination:
    server: https://kubernetes.default.svc
    namespace: <NAMESPACE>
  syncPolicy:
    automated:
      prune: true        # xoá tài nguyên không còn trong Git
      selfHeal: true     # sửa lại khi có người chỉnh tay trên cluster
    syncOptions:
      - CreateNamespace=true
```

## Lệnh CLI

```bash
argocd app list
argocd app get <APP>
argocd app diff <APP>          # khác biệt Git ↔ cluster
argocd app sync <APP>
argocd app history <APP>
argocd app rollback <APP> <HISTORY_ID>   # tắt automated sync trước khi rollback
```

## Lưu ý

- Không commit Secret thô vào Git: dùng Sealed Secrets, External Secrets Operator hoặc SOPS.
- Tách repo cấu hình triển khai khỏi repo mã nguồn; CI chỉ cập nhật tag image trong repo cấu hình.
- Flux là lựa chọn tương đương (CRD `GitRepository` + `Kustomization`, không có UI mặc định).
- Trên OpenShift, Argo CD được cung cấp qua OpenShift GitOps Operator — xem [kb-redhat/ocp](https://github.com/lovelybugf/kb-redhat/blob/main/ocp/_index.md).

## Liên quan

- [Quy trình nhánh Git](git-branching-workflow.md)
- [Kubernetes — đối tượng cốt lõi](../container-orchestration/kubernetes-core-objects.md)

## Nguồn

- Argo CD documentation — https://argo-cd.readthedocs.io/en/stable/
- OpenGitOps principles — https://opengitops.dev/
- Trang mẫu do AI soạn 2026-11-23, chưa chạy kiểm chứng (`unverified`).
