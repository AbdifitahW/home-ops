<div align="center">

## 📊 Cluster Status

</div>


<div align="center">

[![Discord](https://img.shields.io/discord/673534664354430999?style=for-the-badge&label&logo=discord&logoColor=white&color=blue)](https://discord.gg/home-operations)&nbsp;&nbsp;
[![Talos](https://img.shields.io/endpoint?url=https%3A%2F%2Fkromgo.kp2.qzz.io%2Ftalos_version&style=for-the-badge&logo=talos&logoColor=white&color=blue&label=%20)](https://talos.dev)&nbsp;&nbsp;
[![Kubernetes](https://img.shields.io/endpoint?url=https%3A%2F%2Fkromgo.kp2.qzz.io%2Fkubernetes_version&style=for-the-badge&logo=kubernetes&logoColor=white&label=%20&color=blue)](https://kubernetes.io)&nbsp;&nbsp;
[![Flux](https://img.shields.io/endpoint?url=https%3A%2F%2Fkromgo.kp2.qzz.io%2Fflux_version&style=for-the-badge&logo=flux&logoColor=white&color=blue&label=%20)](https://fluxcd.io)&nbsp;&nbsp;
</div>

<div align="center">

[![Age-Days](https://img.shields.io/endpoint?url=https%3A%2F%2Fkromgo.kp2.qzz.io%2Fcluster_age_days&style=flat-square&label=Age)](https://github.com/kashalls/kromgo)&nbsp;&nbsp;
[![Uptime-Days](https://img.shields.io/endpoint?url=https%3A%2F%2Fkromgo.kp2.qzz.io%2Fcluster_uptime_days&style=flat)](https://github.com/kashalls/kromgo)&nbsp;&nbsp;
[![Node-Count](https://img.shields.io/endpoint?url=https%3A%2F%2Fkromgo.kp2.qzz.io%2Fcluster_node_count&style=flat-square&label=Nodes)](https://github.com/kashalls/kromgo)&nbsp;&nbsp;
[![Pod-Count](https://img.shields.io/endpoint?url=https%3A%2F%2Fkromgo.kp2.qzz.io%2Fcluster_pod_count&style=flat-square&label=Pods)](https://github.com/kashalls/kromgo)&nbsp;&nbsp;
[![CPU-Usage](https://img.shields.io/endpoint?url=https%3A%2F%2Fkromgo.kp2.qzz.io%2Fcluster_cpu_usage&style=flat-square&label=CPU)](https://github.com/kashalls/kromgo)&nbsp;&nbsp;
[![Memory-Usage](https://img.shields.io/endpoint?url=https%3A%2F%2Fkromgo.kp2.qzz.io%2Fcluster_memory_usage&style=flat-square&label=Memory)](https://github.com/kashalls/kromgo)&nbsp;&nbsp;
[![Alerts](https://img.shields.io/endpoint?url=https%3A%2F%2Fkromgo.kp2.qzz.io%2Fcluster_alert_count&style=flat-square&label=Alerts)](https://github.com/kashalls/kromgo)
</div>


## Cluster Structure

```sh 
📁 kubernetes/       # Kubernetes cluster defined as code
├── 📁 apps          # Apps deployed into my cluster grouped by namespace
├── 📁 components    # Re-usable kustomize components
└── 📁 flux          # Flux system config
```

# Flux Kustomization Dependencies

The dependency structure of the core Flux Kustomizations in this cluster, specifically focusing on the storage (Rook-Ceph) and observability (Prometheus) stack.

## Dependency Graph

The following diagram illustrates how the Kustomizations are ordered and the HelmReleases they manage.

```mermaid
graph TD
    subgraph Kustomizations
        KS_PROM[kube-prometheus-stack]
        KS_ROOK_CLUSTER[rook-ceph-cluster]
        KS_ROOK_OPERATOR[rook-ceph-operator]
    end

    subgraph HelmReleases
        HR_PROM[kube-prometheus-stack]
        HR_ROOK_CLUSTER[rook-ceph-cluster]
        HR_ROOK_OPERATOR[rook-ceph-operator]
    end

    KS_PROM -- dependsOn --> KS_ROOK_CLUSTER
    KS_ROOK_CLUSTER -- dependsOn --> KS_ROOK_OPERATOR

    KS_PROM -. creates .-> HR_PROM
    KS_ROOK_CLUSTER -. creates .-> HR_ROOK_CLUSTER
    KS_ROOK_OPERATOR -. creates .-> HR_ROOK_OPERATOR
```

## Description

1.  **rook-ceph-operator**: The base Kustomization that installs the Rook-Ceph operator.
2.  **rook-ceph-cluster**: Depends on the operator being ready. It configures the actual Ceph cluster, pools, and storage classes.
3.  **kube-prometheus-stack**: Depends on the storage cluster being ready, as it uses Ceph block storage (RBD) for persistent data (Prometheus and Alertmanager metrics/state).

Each Kustomization points to a directory containing a `HelmRelease` resource, which Flux's Helm Controller then reconciles to install the respective applications.


## Special Thanks
Thanks to the [home-operations](https://discord.gg/home-operations) community for providing tons of insight and huge shoutout to the [@onedr0p](https://github.com/onedr0p) for his awesome cluster template a great starting point anyone wishing to run some k8s at home. 
