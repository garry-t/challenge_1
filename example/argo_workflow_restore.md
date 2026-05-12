## Argo Workflows restore diagram

**Orchestration:** restore is driven only by **Argo Workflows** (manual trigger → workflow steps).

Manual restore trigger → a **Workflow** in Argo Workflows orchestrates Longhorn + Kubernetes + validation + cutover (e.g. `kubectl` patch on Deployment/StatefulSet/Service).

```mermaid
flowchart LR
  subgraph trigger["Trigger"]
    A[Manual: UI / CLI / webhook]
  end

  subgraph wf["Argo Workflows"]
    B[Step 1: Resolve backup ID\n(latest or pinned)]
    C[Step 2: Create restored volume\nLonghorn API / CR apply]
    D[Step 3: Create PVC + optional STS\nkubectl apply / patch]
    E[Step 4: Validation Job\nhealth / smoke / fs checks]
    F[Step 5: Cutover\npatch workload / Service / route]
  end

  subgraph cluster["Cluster"]
    LH[Longhorn]
    K8s[Kubernetes API]
    APP[App workload]
  end

  A --> B --> C --> D --> E --> F
  C --> LH
  D --> K8s
  E --> APP
  F --> APP
```



**Notes**

- Longhorn recurring backups are assumed already configured; the workflow selects a backup and materializes a new volume/PVC (or applies the equivalent Longhorn CRs).
- Cutover and validation run inside the workflow

