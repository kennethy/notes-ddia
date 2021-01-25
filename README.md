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

