---
title: Operations baseline considerations for Azure Kubernetes Service
description: Learn about considerations for your operations baseline for Azure Kubernetes Service.
author: BrianBlanchard
ms.author: pidebrui
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: scenario
ms.custom: think-tank, e2e-aks
---

# Operations baseline considerations for Azure Kubernetes Service

Kubernetes is a relatively new technology, rapidly evolving with an impressive ecosystem. As such, it can be challenging to manage it. By properly designing your Azure Kubernetes Service (AKS) solution with management and monitoring in mind, you can work toward operational excellence and customer success.

## Design considerations

Consider the following factors:

- Be aware of [AKS limits](/azure/aks/quotas-skus-regions). Use multiple AKS instances to scale beyond those limits.
- Be aware of ways to isolate workloads logically within a cluster and physically in separate clusters.
- Be aware of ways to control resource consumption by workloads.
- Be aware of ways to help Kubernetes understand the health of your workloads.
- Be aware of various virtual machines sizes and the impact of using one or the other. Larger VMs can handle more load. Smaller VMs can easier be replaced by others when unavailable for planned and unplanned maintenance. Also be aware of the concept of node pools, VMs in a scale set, which allows to have virtual machines of a different size in the same cluster. Larger VMs are more optimal because [AKS reserves a smaller percentage of its resources](/azure/aks/concepts-clusters-workloads#resource-reservations) making more of its resources available for your workloads.
- Be aware of ways to monitor and log AKS. Kubernetes consists of various components and monitoring and logging should provide an insight of its health, trends as well as potential issues.
- Building on monitoring and logging there can be many events generated by Kubernetes or applications running on top. Alerts can help differentiate between log entries for historical purposes and those that require immediate action.
- Be aware of updates and upgrades that you should do. At the Kubernetes level there are major, minor and patch versions. The customer should apply these update, to remain in a supported state according to the policy in upstream Kubernetes. At the worker host level OS kernel patches may require a reboot, which the customer should do, as well as upgrades to new OS versions. In addition to manually upgrading a cluster, you can set an [auto-upgrade channel](/azure/aks/upgrade-cluster#set-auto-upgrade-channel) on your cluster.
- Be aware of resource limitations of the cluster as well as individual workloads.
- Be aware of the differences between [horizontal pod autoscaler and cluster autoscaler](/azure/aks/concepts-scale)
- Consider securing traffic between pods using [network policies and the Azure policies plug-in](/azure/aks/use-network-policies)
- To help troubleshoot your application and services running on AKS, you may need to view the logs generated by control plane components. You may want to [enable resource logs for AKS](/azure/azure-monitor/containers/container-insights-log-query#resource-logs) since logging is not enabled by default.

## Design recommendations

- Understand AKS limits:
  - [Quotas and regional limits](/azure/aks/quotas-skus-regions)
  - [AKS service limits](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-kubernetes-service-limits)
- Use logical isolation at the namespace level to separate applications, teams, environments, business units. [Multitenancy and cluster isolation](/azure/aks/operator-best-practices-cluster-isolation). Also node pools can help at nodes with different node specifications, and maintenance like Kubernetes upgrades [multiple node pools](/azure/aks/use-multiple-node-pools)
- Plan and apply resource quotas at the namespace level. If pods don't define resource requests and limits, reject the deployment using policies, and so on. This does not apply to kube-system pods, since not all kube-system pods have requests and limits. Monitor resource usage and adjust quotas as needed. [Basic scheduler features](/azure/aks/operator-best-practices-scheduler)
- Add health probes to your pods. Make sure pods contain `livenessProbe`, `readinessProbe`, and `startupProbe` [AKS health probes](/azure/application-gateway/ingress-controller-add-health-probes).
- Use VM sizes big enough to contain multiple container instances so you get the benefits of increased density, but not so big that your cluster can't handle the workload of a failing node.
- Use a monitoring solution. [Azure Monitor for containers](/azure/azure-monitor/containers/container-insights-overview) is set up by default and provides easy access to many insights. You can use [Prometheus integration](/azure/azure-monitor/containers/container-insights-prometheus-integration) if you want to drill deeper or have experience using Prometheus. If you also want to run a monitoring application on AKS, you should also use Azure Monitor to monitor that application.
- Use an alerting system to provide notifications when things need direct action. [Metric alerts](/azure/azure-monitor/containers/container-insights-metric-alerts)
- Use [automatic node pool scaling](/azure/aks/cluster-autoscaler) feature together with [horizontal pod autoscaler](/azure/aks/concepts-scale#horizontal-pod-autoscaler) to meet application demands and to mitigate peak hours loads.
- Use Azure Advisor to get best practice recommendations on cost, security, reliability, operational excellence and performance. Also use [Microsoft Defender for Cloud](/azure/security-center/defender-for-kubernetes-introduction) to prevent and detect threats like image vulnerabilities.
- Use [Azure Arc](/azure/azure-arc/kubernetes/overview) enabled Kubernetes to manage non-AKS Kubernetes clusters in Azure using Azure Policy, Defender for Cloud, GitOps, and so on.
- Use [pod requests and limits](/azure/aks/developer-best-practices-resource-management#define-pod-resource-requests-and-limits) to manage the compute resources within an AKS cluster. Pod requests and limits inform the Kubernetes scheduler which compute resources to assign to a pod.
