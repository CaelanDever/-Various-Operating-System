# Various-Operating-System

# Tier 3 Task: Kernel Tuning for Performance Optimization

Scenario:

Fine-tune the CentOS kernel to enhance the performance and stability of a critical server hosting a high-traffic web application. The server experiences occasional performance bottlenecks during peak usage periods, impacting user experience and application responsiveness. Your goal is to optimize kernel parameters to mitigate these issues and ensure smooth operation of the web application.

# Overview

This guide provides detailed steps to fine-tune the CentOS kernel to enhance the performance and stability of a critical server hosting a high-traffic web application. The goal is to optimize kernel parameters to mitigate performance bottlenecks and ensure smooth operation.

# 1. Identify Key Performance Metrics

Before making any changes, gather insights into the system’s performance. Use the following tools:

sar (System Activity Report): Monitor various system activities.

sar -u 1 3   # CPU usage every 1 second for 3 iterations
sar -r 1 3   # Memory usage every 1 second for 3 iterations
sar -b 1 3   # Disk I/O every 1 second for 3 iterations
sar -n DEV 1 3  # Network usage every 1 second for 3 iterations

vmstat: Provides information about processes, memory, paging, block I/O, traps, and CPU activity.

vmstat 1 5  # Display system performance every 1 second for 5 iterations


<img width="442" alt="ken" src="https://github.com/user-attachments/assets/44f41d96-3186-488e-99e2-d77f3729c384">


iostat: Reports CPU statistics and I/O statistics for devices.

iostat -x 1 5  # Detailed I/O statistics every 1 second for 5 iterations


<img width="443" alt="krn" src="https://github.com/user-attachments/assets/2b2a461c-347b-448f-abec-a807125d7ee5">


2. Analyze Existing Kernel Configuration

Review the current kernel parameters in:

/etc/sysctl.conf: Contains sysctl settings for kernel parameters.

cat /etc/sysctl.conf

/boot/grub2/grub.cfg: Contains boot loader settings and kernel parameters.

cat /boot/grub2/grub.cfg


<img width="442" alt="krnn" src="https://github.com/user-attachments/assets/580a8183-063e-44c3-a4b1-8eca182cfe30">


3. Adjust CPU Scheduling and Process Management

Edit kernel parameters to optimize CPU scheduling and process management:

sched_min_granularity_ns: Adjust the minimum granularity of scheduling.

sched_latency_ns: Increase the latency to allow longer-running tasks.

sched_wakeup_granularity_ns: Modify the granularity of task wake-ups.

Edit /etc/sysctl.conf:

# CPU Scheduling

echo "kernel.sched_min_granularity_ns = 1000000" >> /etc/sysctl.conf
echo "kernel.sched_latency_ns = 20000000" >> /etc/sysctl.conf
echo "kernel.sched_wakeup_granularity_ns = 1500000" >> /etc/sysctl.conf

<img width="442" alt="krnn" src="https://github.com/user-attachments/assets/d2129e63-34fc-4385-9f9a-e0881c4b6ffb">


Apply changes:

sysctl -p

4. Optimize Memory Management

Modify memory management parameters:

vm.swappiness: Controls the tendency of the kernel to swap.
vm.dirty_ratio: Maximum percentage of system memory that can be filled with dirty pages.
vm.dirty_background_ratio: Percentage of memory to start writing dirty pages to disk.

Edit /etc/sysctl.conf:

#Memory Management
echo "vm.swappiness = 10" >> /etc/sysctl.conf
echo "vm.dirty_ratio = 20" >> /etc/sysctl.conf
echo "vm.dirty_background_ratio = 10" >> /etc/sysctl.conf

<img width="275" alt="krnnn" src="https://github.com/user-attachments/assets/42ec3906-f364-4c58-9915-43f0460dacea">


Apply changes:

sysctl -p

5. Disk I/O Optimization

Tune disk I/O settings:

elevator: Set to deadline for rotational disks.

nr_requests: Number of I/O requests per device.

read_ahead_kb: Size of read-ahead buffer.

Edit /etc/default/grub to set the elevator:

GRUB_CMDLINE_LINUX_DEFAULT="elevator=deadline"

Update GRUB configuration:

grub2-mkconfig -o /boot/grub2/grub.cfg

<img width="425" alt="vmcf" src="https://github.com/user-attachments/assets/472ea228-b59d-41cb-8cf8-ce6a0baeb084">


Modify other parameters dynamically:

# Disk I/O

echo "blockdev --setra 8192 /dev/sda"  # Adjust read-ahead size
echo "echo 512 > /sys/block/sda/queue/nr_requests"  # Set nr_requests

<img width="437" alt="gurb" src="https://github.com/user-attachments/assets/c41c4719-ba77-4615-b57f-43a8caea09c3">


# 6. Network Stack Configuration
Optimize network settings:

net.core.rmem_max: Maximum TCP receive buffer size.

net.ipv4.tcp_window_scaling: Enable TCP window scaling.

net.core.netdev_max_backlog: Maximum number of packets in the input queue.

Edit /etc/sysctl.conf:

# Network Settings

echo "net.core.rmem_max = 16777216" >> /etc/sysctl.conf
echo "net.ipv4.tcp_window_scaling = 1" >> /etc/sysctl.conf
echo "net.core.netdev_max_backlog = 5000" >> /etc/sysctl.conf


<img width="398" alt="nter" src="https://github.com/user-attachments/assets/c7419e41-694f-4b3b-b97d-597e60b9e709">


Apply changes:

sysctl -p

<img width="237" alt="nett" src="https://github.com/user-attachments/assets/217ede6e-6897-4415-a06f-4797fb7a2b7e">


# 7. Implement Changes Gradually
Make changes one at a time and monitor the system’s performance after each adjustment. This approach helps identify which parameter changes have the most impact.

# 8. Benchmark Performance
Use benchmarking tools to evaluate the server’s performance before and after tuning:

Apache Bench for web performance.

ab -n 1000 -c 10 http://your-server-url/
sysbench for various system tests.

sysbench --test=cpu --cpu-max-prime=20000 run

Compare results to ensure the changes have improved performance.

<img width="376" alt="sysb" src="https://github.com/user-attachments/assets/127345e5-16bf-4d9f-b601-106f3b647ee7">


# 9. Ongoing Monitoring and Maintenance

Regularly monitor system performance and review the kernel parameters. 

Adjust as necessary based on changes in traffic patterns or application requirements.

Use monitoring tools like Nagios, Zabbix, or Prometheus.

Review logs and performance metrics regularly.

<img width="433" alt="vmvm" src="https://github.com/user-attachments/assets/8072a9e2-0cb9-48c2-82a4-33bbb34c4ae3">


# Summary

This documentation provides a comprehensive record of the kernel tuning process undertaken to enhance the performance and stability of a CentOS server hosting a high-traffic web application. It details the parameters adjusted, the rationale behind each change, the observed impact on performance, and any issues encountered.

# 1. Parameters Adjusted
CPU Scheduling and Process Management
sched_min_granularity_ns

New Value: 1000000 (1 millisecond)

Rationale: Finer scheduling granularity allows the CPU to switch between tasks more frequently, which can improve responsiveness for latency-sensitive applications.
sched_latency_ns

New Value: 20000000 (20 milliseconds)

Rationale: By increasing the latency, processes have more time to run before being preempted, which can reduce context switching and improve throughput.
sched_wakeup_granularity_ns

New Value: 1500000 (1.5 milliseconds)

Rationale: More efficient task wake-ups can reduce the overhead of managing task transitions and improve performance under high load.
Memory Management

vm.swappiness

New Value: 10

Rationale: Reducing swappiness decreases the likelihood of swapping to disk, which is beneficial for applications requiring high memory performance.

vm.dirty_ratio

New Value: 20

Rationale: Increasing the dirty ratio allows the system to accumulate more dirty data in memory before writing it to disk, potentially improving write performance.

vm.dirty_background_ratio

New Value: 10

Rationale: Raising this ratio allows more dirty data to accumulate before the background flush process starts, which can improve overall write performance.

Disk I/O Optimization
elevator

New Value: deadline

Rationale: The deadline I/O scheduler provides better performance for rotational disks by prioritizing I/O requests to reduce latency.
nr_requests

New Value: 512

Rationale: Increasing the number of I/O requests allows for higher throughput and reduced I/O bottlenecks.
read_ahead_kb

New Value: 8192

Rationale: A larger read-ahead buffer size improves performance by prefetching more data from the disk.

Network Stack Configuration

net.core.rmem_max

New Value: 16777216 (16 MB)

Rationale: Larger TCP receive buffers improve network performance by allowing more data to be buffered before processing.

net.ipv4.tcp_window_scaling

New Value: 1 (Enabled)

Rationale: Enabling TCP window scaling improves throughput by allowing TCP to use larger window sizes.

net.core.netdev_max_backlog

New Value: 5000

Rationale: Increasing the backlog size prevents packet loss during high network traffic periods.

File System Performance

commit

New Value: 100

Rationale: Reducing the commit interval to 100 milliseconds ensures more frequent updates to the file system journal, improving data integrity.
data

New Value: ordered

Rationale: Ensures data is written to disk in the correct order, maintaining file system integrity.

barrier

New Value: 1

Rationale: Enabling write barriers improves data consistency and protects against data corruption.

blocksize

New Value: 4096 (4 KB)

Rationale: Optimizing block size for better file system performance by aligning with typical disk sector sizes.

Kernel Security Hardening
kernel.randomize_va_space

New Value: 2

Rationale: Full randomization of the virtual address space enhances security by making it harder for attackers to predict memory locations.

kernel.stack_protector

New Value: 1 (Enabled)

Rationale: Enables stack protection to defend against stack buffer 
overflows.

Virtual Memory Management

vm.min_free_kbytes

New Value: 65536 (64 MB)

Rationale: Reserves memory for critical tasks, ensuring the system does not run out of memory during peak usage.

vm.vfs_cache_pressure

New Value: 50

Rationale: Adjusts the pressure on the inode and dentry caches, balancing memory usage between caching and other tasks.

Network Performance Optimization

net.ipv4.tcp_congestion_control

New Value: cubic

Rationale: cubic is a widely used congestion control algorithm that 
improves TCP performance under high-bandwidth conditions.

net.ipv4.tcp_fastopen

New Value: 3

Rationale: Enables TCP Fast Open to reduce latency in establishing TCP 
connections.

net.ipv4.tcp_rmem and net.ipv4.tcp_wmem

New Value: 4096 87380 6291456 (Receive) and 4096 65536 6291456 (Send)

Rationale: Adjusted TCP buffer sizes for better network throughput.

2. Rationale

Each parameter was adjusted to address specific performance issues identified through system monitoring:

CPU Scheduling: Enhanced responsiveness and reduced context switching.


<img width="444" alt="sar" src="https://github.com/user-attachments/assets/ef37bc7a-6880-49c5-a3e7-a9a0d98f5ae8">


Memory Management: Improved memory usage efficiency and reduced swapping.

Disk I/O: Increased throughput and reduced latency.


<img width="440" alt="ioi" src="https://github.com/user-attachments/assets/18431c90-0743-44ef-aacc-112c1d6377ba">


Network Stack: Improved network performance and packet handling.

<img width="433" alt="nets" src="https://github.com/user-attachments/assets/70661292-498f-4529-8c09-91d5b5ecbc58">



File System: Enhanced data integrity and file system performance.

Kernel Security: Increased system security through address space randomization and stack protection.

Virtual Memory: Ensured sufficient memory availability for critical tasks.

<img width="318" alt="vmem" src="https://github.com/user-attachments/assets/72b5a46b-0694-43d2-940a-5c7e7fd08ac7">


Network Performance: Improved TCP performance and reduced latency in connection setup.

3. Performance Impact

CPU Scheduling: Reduced context switching overhead, resulting in smoother 
application performance during peak loads.

Memory Management: Lowered swap usage, leading to faster application response times and reduced disk I/O.

Disk I/O: Increased read and write throughput, reducing latency and improving overall system responsiveness.

Network Stack: Improved network throughput and reduced packet loss, 
enhancing application performance and user experience.

File System: Improved data integrity and file system reliability with minimal impact on performance.

Kernel Security: Enhanced system security with no significant impact on performance.

Virtual Memory: Ensured stable operation with adequate memory availability.
Network Performance: Improved TCP performance and reduced connection setup latency, enhancing network responsiveness.

