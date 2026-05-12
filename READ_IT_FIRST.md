## POC Assumptions 

1. Since kubernetes platform wasn't specified in the challenge I give priority to kubernetes generic solutions.
2. I assume that monolith application doesnt support any kind sharding or split RW request across differrent storages, one app -> one storage volume -> one customer.
3. According to https://github.com/google/leveldb I assume that this db was chosen due to performance and storage utilisation against resiliency it is crucial for current POC.
4. LVM + restic it is old-school approach which is doesnt fit to modern kubernetes world
5. I dont consider Restic as efficient tool, Restic works on FS level high churn file change brings IO consumption and bakup window can be increased in time.
6. I assume Application health remains application-owned (startup/readiness/liveness endpoints)
7. I assume that nic uplink at least 5Gbps in DC, ~550-600 MB/s 
8. I assume exists terraform provider which gives ability provision infrastructure