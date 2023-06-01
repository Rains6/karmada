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

<!--
This section should contain enough information that the specifics of your
change are understandable. This may include API specs (though not always
required) or even code snippets. If there's any ambiguity about HOW your
proposal will be implemented, this is the place to discuss them.
-->

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
