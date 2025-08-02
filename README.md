# CosmoSpan Dataset

This dataset contains synthetic data prepared to evaluate scheduling and descheduling mechanisms within Edge-Cloud
Continuum environments.
It includes detailed information on infrastructure (nodes), workloads (pods), network latencies, and the outcomes of
various placement experiments.
The dataset was used to assess the performance of the proposed CosmoSpan platform in comparison to a baseline
configuration using the default Kubernetes Scheduler.

## Structure and Contents Explanation

The dataset is organized into two primary top-level folders: `datasets` and `results`.

### `datasets` folder:

This folder contains the synthetic data generated for the evaluation of scheduling and descheduling algorithms. Each
subfolder within `datasets` represents a unique simulation scenario, characterized by the following naming convention:
`<number_of_nodes>_nodes_<target_resource_utilization>_util_<id>`.

For example: `10000_nodes_70_util_7` signifies a dataset with:

* **10,000 nodes**: The total number of compute nodes simulated in this environment.
* **70% target resource utilization**: The desired percentage of all available resources (CPU, Memory, Bandwidth) that
  pods are intended to consume across the entire infrastructure. This helps in simulating various load conditions.
* **7th generated dataset of this type**: A unique identifier for different instantiations of the same node count and
  target utilization.

Each of these scenario subfolders (`<number_of_nodes>_nodes_<target_resource_utilization>_util_<id>`) contains the
following
core files:

* **`nodes.csv`**: This file details the characteristics of each compute node in the simulated environment. Parameters
  used for generation are defined at the beginning of each file.
    * **`hostname`**: Unique identifier for the node.
    * **`layer`**: Categorization of the node's location (e.g., fog or cloud).
    * **`region`**: Geographical region of the node.
    * **`cloud_zone`**: Specific availability zone within a cloud region for cloud nodes.
    * **`fog_location`**: Specific physical location for fog nodes.
    * **`cpu_cores`**: Number of available CPU cores on the node.
    * **`single_core_score`**: Performance score of a single CPU core.
    * **`base_single_core_score`**: Baseline performance score of a single CPU core, used for comparison.
    * **`memory_GiB`**: Available memory in Gigabytes on the node.
    * **`ingress_bandwidth_Mbps`**: Maximum incoming network bandwidth in Megabits per second.
    * **`egress_bandwidth_Mbps`**: Maximum outgoing network bandwidth in Megabits per second.
    * **`uptime_30d_percentage`**: Simulated uptime percentage over a 30-day period, indicating node reliability.
    * **`cost`**: A numerical value representing all costs associated with using this node.

* **`pods.csv`**: This file describes the characteristics and resource requirements of each workload instance (pod)
  to be scheduled. Parameters used for generation are defined at the beginning of each file.
    * **`id`**: Unique identifier for the pod.
    * **`app_id`**: Identifier for the application to which the pod belongs. Multiple pods can share the same `app_id`.
    * **`app_type`**: Classification of the application (e.g., request driven or event driven).
    * **`workload_id`**: Identifier of the workload (e.g., Deployment) to which the pod belongs.
    * **`replica_id`**: Identifier for a specific replica (pod) within a workload.
    * **`layer`**: The preferred layer (fog or cloud) for the pod, if specified.
    * **`cpu_request_cores`**: Requested CPU cores for the pod.
    * **`memory_request_GiB`**: Requested memory in Gigabytes for the pod.
    * **`ingress_bandwidth_request_Mbps`**: Requested incoming network bandwidth in Megabits per second.
    * **`egress_bandwidth_request_Mbps`**: Requested outgoing network bandwidth in Megabits per second.
    * **`uptime_30d_percentage_request`**: Desired uptime percentage over a 30-day period for the selected node,
      reflecting its
      reliability requirement.

* **`node-latencies.json`**: This JSON file specifies network latencies. It includes:
    * **Inter-cluster latencies**: Network latencies between different clusters (e.g., between a cloud cluster and a
      fog cluster, etc.).
    * **Intra-cluster latencies**: Network latencies between individual nodes within the same cluster.

* **`workload-latency-requests.json`**: This JSON file defines the latency constraints between different workloads. It
  specifies required maximum latency values for communication between workload X and workload Y, which is critical for
  interdependent application components.

* **`descheduler_mutations` (Present only for datasets with 1000 nodes)**: This subfolder is dedicated to evaluating
  descheduling algorithms under dynamic environmental changes. It contains data for 10 distinct mutation scenarios,
  where "mutation 0" represents the initial, unmutated state. For each mutation, two sets of data were generated: one
  for the "baseline" (no descheduler intervention) and one for the "proposed solution" (with descheduler active).
  For each mutation, the following files are provided, reflecting the altered node and latency characteristics:
    * **`node-latencies_<baseline or proposed>_<mutation>`**: Updated network latencies after the mutation, specific to
      either the baseline or proposed descheduler scenario.
    * **`nodes_<baseline or proposed>_mutation`**: Updated node resource configurations after the mutation, specific to
      either the baseline or proposed descheduler scenario. These mutations simulate real-world changes like node
      failures, resource fluctuations, or network degradation.

### `results` folder:

This folder stores the outcomes of the pod placement experiments, including the final placements and the calculated
Edge-Cloud Continuum Placement scores.

* **`placements` subfolder**: This subfolder mirrors the structure of the `datasets` folder, containing subfolders for
  each evaluated dataset (`<number_of_nodes>_nodes_<target_resource_utilization>_util_<id>`).
    * **`scheduler`**: This subfolder contains the placements generated by the scheduling algorithms.
        * **`baseline.csv`**: This file records the pod-to-node assignments using the baseline scheduling
          approach.
            * **`pod_name`**: The identifier of the pod.
            * **`node_name`**: The identifier of the node where the pod was placed.
        * **`proposed.csv`**: This file contains the pod-to-node assignments using the proposed scheduling solution (
          e.g., "CosmoSpan"). It follows the same structure as `baseline.csv`.
    * **`deschedulers` (Present only for datasets with 1000 nodes)**: This subfolder contains the pod-to-node
      re-assignments resulting from descheduler operations (or no operations for the baseline) across the 10 mutation
      scenarios.
      Each file represents the final placement after a specific mutation and descheduler evaluation.
        * **`<baseline or proposed>_mutation.csv`**: This file details the pod-to-node assignments after a particular
          mutation, distinguishing between the baseline and proposed descheduler solutions.
            * **`pod_name`**: The identifier of the pod.
            * **`node_name`**: The identifier of the node where the pod was placed after descheduling.

* **`scores` subfolder**: This subfolder aggregates and determines the quality of placements derived from the placement
  results.
    * **`desched_eval_placement_scores.csv`**: This file contains the evaluation scores for descheduling experiments.
        * **`name`**: A unique identifier for the evaluation run, following the format
          `<dataset>_mutation_<mutation_number>_<baseline or proposed>`.
        * **`scores for each criterion`**: A series of columns representing scores for various performance metrics, such
          as CPU request satisfaction, memory request satisfaction, network latency adherence, and other relevant
          criteria for an Edge-Cloud Continuum Placement Score.
    * **`sched_eval_placement_scores.csv`**: This file contains the evaluation scores for scheduling
      experiments.
        * **`name`**: A unique identifier for the evaluation run, following the format
          `<dataset>_<baseline or proposed>`.
        * **`scores for each criterion`**: Similar to `desched_eval_placement_scores.csv`, this includes scores for
          various performance metrics achieved by the scheduling algorithms.
    * **`sched_eval_placement_times.csv`**: This file records the time taken for the scheduling process.
        * **`name`**: A unique identifier for the scheduling run, following the format
          `<dataset>_<baseline or proposed>_<dataset_id>`.
        * **`size`**: The total number of nodes considered in the scheduling problem (corresponds to `<number_of_nodes>`
          in the dataset folder name).
        * **`total_scheduling_time_minutes`**: The total time, in minutes, required for the scheduler to complete the
          pod placements for the given dataset.
