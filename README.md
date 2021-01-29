# Designing Data-Intensive Applications (WIP)

Reading Notes for [Designing Data-Intensive Applications (Kleppmann)](https://www.oreilly.com/library/view/designing-data-intensive-applications/9781491903063/)

## Chapter 1 - Reliable, Scalable, and Maintainable Applications

### Thinking About Data Systems

New tools for data storage and processing have emerged. They are optimized for a variety of different use cases, and they are combined to form a *data system* and *to provide a service*.

The data system should be:
- **Reliable:** The system should continue to work *correctly* even in the face of *adversity*.
- **Scalable:** As the system *grows*, there should be reasonable ways of dealing with that growth.
- **Maintainable:** Over time, different people will work on the system, and they should all be able to work on it *productively*.

### Reliability

A system is reliable if it works correctly even when things go wrong. Things that can go wrong are called *faults.* Systems that anticipate faults and can cope with them are called *fault-tolerant* or *resilient.*

Fault is usually defined as one component of the system deviating from its spec, whereas a failure is when the system as a whole stops providing the required service to the user. Fault-tolerance is exercised by deliberately triggering faults.

**Hardware Faults**

Instances such as hard disks crash, faulty RAM, power grid blackout, and etc are considered hardware faults. The prevention steps include adding redundancy to hardware components, dual power supplies, hot swappable CPUS, and datacenters with backup generators.

**Software Errors**

Systematic error that is harder to anticipate, and tend to cause more system failure than uncorrelated hardware faults. Prevention steps include carefully thinking about assumptions and interactions in the system; through testing; process isolation; process isolation; allowing processes to crash and restart; measuring/monitoring/analyzing system behaviour in production.

**Human Errors**

Humans are error-prone. Study has shown that configuration errors by operators were the leading causes of outages. 

- Prevent errors by designing admin interfaces that are easy to "do the right thing" and discourage "the wrong thing."
- Introduce *sandbox environments* where people can explore and experiment safely, using real data, without effecting real users.
- Test the system at all levels using unit tests, integration tests, and manual tests.
- Make it easy to rollback changes. Roll-out new code gradually and to a small subset of users.
- Clear monitoring in place with the use of performance metrics and error rates.

### Scalability

The system's ability to cope with increased load. We should consider our options of coping with the growth, and how we can add computing resources to handle the additional load.

**Describing Load**

Load parameters: describe the current load on the system. They vary based on the architecture of the system. (e.g. requests per second, ratio of reads to writes, active users count, cache hit rate, and etc)

Fan-out: it is used to describe the number of requests to other services that need to be made in order to serve one incoming requests.

**Describing Performance**

Investigate what happens when the load increases:
- How the system performs with an increase in a load parameter while keeping system resources unchanged?
- As the load parameter increases, how much do the system resources need to increase in order to keep the performance unchanged?

Throughput: the nunber of records we can process per second, or the total time it takes to run a job on a dataset of a certain size.

Latency: not to be confused with response time. It is the duration that a request is waiting to be handled.

Response time: the time between a client sending a request and receiving a response. It varies as the system handles a variety of requests. It should not be treated as a single number, but as a distribution of values that can be measured. Percentiles are preferred over arithmetic mean to gauage the average response time of a service.

50th percentile: or *p50*, is the *median*. For example, 200ms median response time means half of requests return in less than 200ms, and the other half take longer than that.

Tail latencies: high percentiles of response time. For example, 95th or p95. If the p95 is 1.5 seconds, then 95 out of 100 requests take less than 1.5 seconds, 5 out of 100 requests take 1.5 seconds or more.

Service level objects (SOL) and Service level agreements (SLAs): contracts that define the expected performance and availability of a service.

Head-of-line blocking: when a small number of slow requests hold up the processing of subsequent requests.

**Approaches for Coping with Load**

Vertical Scaling: moving to a more powerful machine. Tends to get expensive.

Hortizontal Scaling: distributing the load across multiple smaller machines.

Elastic System: when computing resources can be automatically be added when detecting a load increase.

### Maintainability

The majority of the cost of software is not in its initial development, but in its on-going maintenance.

- Operability: make it easy for operations team to keep the system running smoothly.
- Simplicity: make it easy for new engineers to understand the system.
- Evolvability: make it easy for engineers to make changes to the system in the future, adapting it for unanticipated use cases as requirements change.

**Operability: Making Life Easy For Operations**

Operations team is responsible for ensuring the system runs smoothly and resolve issues when they arise. Good operability means making routine tasks easy, allowing the operations team to focus their efforts on high-value activities.

- Good monitoring on system health
- Good support for automation and integration tools
- Good documentation on how the system behaves

**Simplicity: Managing Complexity**

Complexity grows and become difficult to understand as project gets larger. Manage complexity with good abstractions. Abstractions hide implementation details and encourage code reuse. Ulimately, the improve the quality of the software.

**Evolvability: Making Change Easy**

System requirements are constantly changing. *Evolvability*, or how easily a system can be modified, is closely linked to its simplicity and its abstractions. 