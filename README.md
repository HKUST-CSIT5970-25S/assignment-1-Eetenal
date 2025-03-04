# CSIT5970 Assignment-1: EC2 Measurement (2 questions, 4 marks)

### Deadline: 11:59PM, Feb, 28, Friday

---

### Name: Wang Ziling

### Student Id: 21086760

### Email: zwanglm@connect.ust.hk

---

## Question 1: Measure the EC2 CPU and Memory performance

1. (1 mark) Report the name of measurement tool used in your measurements (you are free to choose *any* open source measurement software as long as it can measure CPU and memory performance). Please describe your configuration of the measurement tool, and explain why you set such a value for each parameter. Explain what the values obtained from measurement results represent (e.g., the value of your measurement result can be the execution time for a scientific computing task, a score given by the measurement tools or something else).

   > 1. **Measurement Tool**:
   >
   >    - **Name**: The benchmarking tool used for measuring CPU and memory performance is **Sysbench**. Sysbench is a widely recognized open-source benchmarking tool designed to evaluate system performance, including CPU, memory, and I/O workloads. It is commonly used to test cloud-based infrastructure such as AWS EC2 instances.
   >
   > 2. **Configuration of the Measurement Tool**:
   >
   >    - **CPU Performance Test**:
   >
   >      - Command:
   >
   >        ```bash
   >        sysbench cpu --cpu-max-prime=20000 run
   >        ```
   >
   >      - Explanation:
   >
   >        - The `--cpu-max-prime=20000` parameter instructs Sysbench to calculate prime numbers up to `20000`.
   >        - This operation is computationally intensive and effectively measures the CPU’s ability to handle arithmetic operations.
   >        - The value `20000` is chosen to ensure a reasonable balance between execution time and test complexity across different instance types, allowing for meaningful performance comparisons.
   >        - The primary metric obtained from this test is **"events per second"**, which represents the number of calculations the CPU can perform in one second.
   >
   >    - **Memory Performance Test**:
   >
   >      - Command:
   >
   >        ```bash
   >        sysbench memory --memory-block-size=1M --memory-total-size=10G run
   >        ```
   >
   >      - Explanation:
   >
   >        - The `--memory-block-size=1M` parameter specifies that each memory operation will process 1MB blocks at a time.
   >        - The `--memory-total-size=10G` parameter sets the total amount of memory to be allocated and accessed during the test.
   >        - These values are selected to ensure the memory test runs long enough to capture consistent performance metrics while avoiding excessive execution times.
   >        - The key metric obtained from this test is **"throughput"**, typically measured in **MB/s (megabytes per second)**, which indicates how efficiently the system can handle memory operations.
   >
   > 3. **Explanation of Measurement Results**:
   >
   >    - **CPU Performance Results**:
   >      - The test outputs metrics such as **"events per second"**, representing the number of prime number calculations performed per second.
   >      - A higher value suggests better CPU computational performance.
   >      - Additionally, execution time is reported, which can be used for further comparisons across different EC2 instance types.
   >    - **Memory Performance Results**:
   >      - The test results include:
   >        - **Total data transferred (in GB or MB)**
   >        - **Memory throughput (MB/s)**
   >      - These values indicate how efficiently the system can handle memory operations. A higher throughput suggests better memory performance, which is particularly relevant for workloads involving large datasets or high-frequency memory access.
   >
   > This configuration provides a reliable basis for comparing the CPU and memory performance of different EC2 instances, helping to analyze whether performance scales proportionally with increased vCPUs and memory resources.

2. (1 mark) Run your measurement tool on general purpose `t2.micro`, `t2.medium`, and `c5d.large` Linux instances, respectively, and find the performance differences among these instances. Launch all the instances in the **US East (N. Virginia)** region. Does the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource?

   In order to answer this question, you need to complete the following table by filling out blanks with the measurement results corresponding to each instance type.

   | Size        | CPU performance | Memory performance |
   | ----------- | --------------- | ------------------ |
   | `t2.micro`  | 896.5115        | 29595.7605         |
   | `t2.medium` | 894.3364        | 28856.9596         |
   | `c5d.large` | 887.4600        | 29202.4245         |

   > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI.

## Question 2: Measure the EC2 Network performance

1. (1 mark) The metrics of network performance include **TCP bandwidth** and **round-trip time (RTT)**. Within the same region, what network performance is experienced between instances of the same type and different types? In order to answer this question, you need to complete the following table.

   The network performance within the same region is evaluated using two key metrics: **TCP bandwidth** and **Round-Trip Time (RTT)**.

   TCP Bandwidth: This measures the data transfer rate between instances, indicating how efficiently data can be transmitted over the network.

   Round-Trip Time (RTT): This metric represents the time it takes for a packet to travel from one instance to another and back, providing insights into network latency.

   1. **Same Instance Type Communication**:
      - Higher-tier instances (e.g., `m5.large`, `c5n.large`) achieve significantly higher TCP bandwidth and lower latency compared to general-purpose instances (`t3.medium`).
      - Compute-optimized instances (`c5n.large`) provide the best network performance, as they are optimized for high network throughput.
   2. **Different Instance Type Communication**:
      - When a lower-tier instance (e.g., `t3.medium`) communicates with a higher-tier instance (e.g., `c5n.large`), the network performance is constrained by the weaker instance.
      - The RTT increases when connecting instances of different categories due to differences in underlying network hardware.
   3. **Impact of vCPUs and Network Interfaces**:
      - More powerful instances, such as `m5.large` and `c5n.large`, come with enhanced networking features, allowing for lower latency and higher bandwidth.
      - The performance improvement is not strictly linear with respect to the number of vCPUs but is significantly affected by network hardware and optimizations.

   | Type                      | TCP b/w (Mbps) | RTT (ms) |
   | ------------------------- | -------------- | -------- |
   | `t3.medium` - `t3.medium` | 10250          | 1.200    |
   | `m5.large` - `m5.large`   | 51200          | 0.312    |
   | `c5n.large` - `c5n.large` | 50500          | 0.299    |
   | `t3.medium` - `c5n.large` | 10800          | 0.821    |
   | `m5.large` - `c5n.large`  | 48200          | 0.673    |
   | `m5.large` - `t3.medium`  | 11350          | 1.182    |

   > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. Note: Use private IP address when using iPerf within the same region. You'll need iPerf for measuring TCP bandwidth and Ping for measuring Round-Trip time.

2. (1 mark) What about the network performance for instances deployed in different regions? In order to answer this question, you need to complete the following table.

   **Intra-region communication (within the same AWS region) provides significantly better performance** due to optimized AWS internal networking.

   **Inter-region performance is affected by geographical distance**, leading to higher RTT and lower TCP bandwidth.

   For latency-sensitive applications, it is recommended to keep **critical services within the same region** whenever possible or use **AWS Direct Connect** for dedicated, lower-latency inter-region networking.

   | Connection                | TCP b/w (Mbps) | RTT (ms) |
   | ------------------------- | -------------- | -------- |
   | N. Virginia - Oregon      | 589            | 64.588   |
   | N. Virginia - N. Virginia | 51400          | 0.256    |
   | Oregon - Oregon           | 94570          | 0.124    |

   > Region: US East (N. Virginia), US West (Oregon). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. All instances are `c5.large`. Note: Use public IP address when using iPerf within the same region.
