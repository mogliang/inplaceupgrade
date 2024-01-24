## K3s Support in CAPI
### Goals
- Introduce CAPI boostrap provider and controlplane provider for k3s.
- Allow user to use CAPI to manage k3s target cluster lifecycle.

### Non-Goals
- To make AKS IOT support k3s target cluster, there are extra work need to be done on each layer, like API changes, AKS operator and Image preparation. These are not in this scope.

### Proposal
AKS IOT use CAPI as the core component to management target cluster lifecycle. By default, CAPI only support K8s distribution. Well, it provides contract to allow developers to implement their own bootstrap and controlplane logic.

To enable K3s management capability for CAPI, we will need implement bootstrap provider for k3s, let it create node bootstrap script to init/join k3s cluster. And implement controlplane provider for k3s, let it manage k3s controlplane lifecycle (create/upgrade/heal etc.)

There is a community project which works on CAPI k3s providers [cluster-api-k3s](https://github.com/cluster-api-provider-k3s/cluster-api-k3s). Currently, the project is not ready for production. Based on test, it shall be able to create k3s cluster, other management feature like monitor, upgrade, node auto-heal doesn't work well.

We shall leverage this community project, identify the backlogs need to be done and contribute, and push project to be mature.

## In-place Update Support in CAPI
### Goals
- Introduce external upgrade stretegy in CAPI, allowing developers to implment their own upgrade logic.
- Introduce cluster upgrade operator, implement CAPI external upgrade contract, orchestrate nodes in-place upgrade for target cluster.
### Non-Goals
- Addons like CNI, CSI are not part of cluster infra, and not in this scope.
### Proposal
Rollout upgrade in CAPI are not suitable for all scenarios, there are community members raised a feature ask for in-place upgrade, and feature group is created for this topic, we've engaged on previous version of AKS-IOT design.

Currently, a proposal is drafted. it suggests a pluggable upgrade strategy architecture that allows external entities (upgrade strategy implementers) to handle the Kubernetes cluster upgrade process. The design aims to decouple core CAPI controllers from the specific mechanisms involved in upgrades, providing users with the flexibility to choose or implement their own strategies. The external upgrade strategy is proposed to be triggered by configuring the Control Plane(KCP)/MachineDeployment with a new field strategy: external. This ensures a consistent user experience by leveraging existing logic in these controllers to determine when changes are required.
![Alt text](image.png)

We shall participate in this feature design and implementation, make sure it fits our scenario. And we also need implement our upgrade operator to communiate with MOC and make A/B upgrade.

https://hackmd.io/Wv_u2xXJQsaj4wWFQ3PqCQ?view

https://hackmd.io/CM9pJl4bQJOnjFjwudJcbg?view


### A/B Update capability
Dev Lead: ??
To make in-place upgrade on target cluster, NodeAgent shall be able to write image content to node partition, and ssh to node for executing commands.

## Image Management
Dev Lead: ??
There isn't CSV on AKS IOT Host, we'll need API to be able cache Image on target host.