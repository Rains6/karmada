---
title: Support multi-cluster services with LB
authors:
- "@XiShanYongYe-Chang"
- "@GitHubxsy"
- "@Rains6"
reviewers:
- TBD
approvers:
- TBD

creation-date: 2023-06-01

---

# Support multi-cluster services with LB

## Summary

我们已经基于 Kubernetes 社区 [sig-multicluster](https://github.com/kubernetes/community/tree/master/sig-multicluster) 提出的 [mcs-api](https://github.com/kubernetes-sigs/mcs-api)，实现了 MCS 功能，解决了多集群间服务发现的问题。但是 MCS 并未解决多集群维度服务暴露的问题。当前提案提出了一个新的 MultiClusterService（简称 MCS） API，用于在 OSI 四层上暴露多集群服务。MCS 将外部流量转发到 Karmada 管理的成员集群 Service 后端上，突破了单集群 Service 的边界限制，实现多集群流量统一入口。

## Motivation

使用户可以像使用单集群 Service 一样使用 MCS，从而轻松地管理多个 Kubernetes 集群中的服务和应用程序，帮助用户快速简洁实现在多个 Kubernetes 集群之间进行负载均衡和四层网络流量路由。

### Goals

- 定义 MCS API 来支持多集群服务的暴露。
- 整合之前基于 mcs-api 实现的多集群服务发现原子能力，通过 MCS API 为用户提供更抽象、更简单的资源定义。
- 在 [multicluster-cloud-provider](https://github.com/karmada-io/multicluster-cloud-provider) 仓库中提供 MCS 对接 LoadBalancer 的公共接口以及相关的控制器实现，供云提供商 LB 对接实现中 import。

### Non-Goals

特定云提供商对于 LB 对接的实现。

## Proposal

### User Stories (Optional)

#### Story 1

#### Story 2

### Notes/Constraints/Caveats (Optional)

### Risks and Mitigations

## Design Details

### 用户视角图：

1、创建 Deployment、Service 并分发至成员集群
2、创建 multiClusterService 对象
3、multicluster-cloud-provider-xxx 将 pod ip 注册到 LB 实例

![image](https://github.com/XiShanYongYe-Chang/karmada/assets/78244870/2a458457-2399-4293-bd06-7d4630ff93b8)

```go
type MultiClusterService struct {
	metav1.TypeMeta   `json:",inline"`
	metav1.ObjectMeta `json:"metadata,omitempty"`

	// Spec is the desired state of the MultiClusterService.
	// +optional
	Spec ServiceSpec `json:"spec,omitempty"`

	// Status is the current state of the MultiClusterService.
	// +optional
	Status corev1.ServiceStatus `json:"status,omitempty"`
}

type ServiceSpec struct {
	// Backend 设置保留的后端，对于 LoadBalance ServiceType 生效
	Backend ServiceBackend `json:"backend"`

	// Types 指定 MCS 类型，可以指定多个类型，不能指定为空
	Types []ServiceType `json:"types"`
}

type ServiceBackend struct {
	// Port 指定暴露的业务端口
	Port string `json:"port"`
}

type ServiceType string

const (
	// LoadBalance 将指定 Service 对外暴露，将后端 IP 注册到 LB 上
	LoadBalance ServiceType = "LoadBalance"
	// ClusterIP 利用 mcs-api 进行多集群间服务发现，会自动创建ServiceExport、ServiceImport并分发至所有成员集群
	ClusterIP ServiceType = "ClusterIP"
)
```

### MultiClusterService 与 Service 的对应关系如何？

MultiClusterService 与 Service 的 name 字段保持一致，其应该视为与 Service 为同一个 Service，只不是一种多集群表现形态。在多集群服务暴露与多集群服务发现的过程中，会将对MultiClusterService的处理转化为 karmada 控制面上同名 Service 的处理。

### 多集群间如何进行域名访问？

域名解析保持一致：svcname.ns.svc.cluster.local

### ClusterIP 类型的 MultiClusterService controller实现逻辑放在何处？

ClusterIP 类型的 MultiClusterService controller实现逻辑放在 karmada-controller-manager 中。

### Test Plan

<!--
**Note:** *Not required until targeted at a release.*

Consider the following in developing a test plan for this enhancement:
- Will there be e2e and integration tests, in addition to unit tests?
- How will it be tested in isolation vs with other components?

No need to outline all test cases, just the general strategy. Anything
that would count as tricky in the implementation, and anything particularly
challenging to test, should be called out.

-->

## Alternatives

<!--
What other approaches did you consider, and why did you rule them out? These do
not need to be as detailed as the proposal, but should include enough
information to express the idea and why it was not acceptable.
-->

<!--
Note: This is a simplified version of kubernetes enhancement proposal template.
https://github.com/kubernetes/enhancements/tree/3317d4cb548c396a430d1c1ac6625226018adf6a/keps/NNNN-kep-template
-->
