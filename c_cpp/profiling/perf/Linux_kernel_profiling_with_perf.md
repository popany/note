# [Linux kernel profiling with `perf`](https://perf.wiki.kernel.org/index.php/Tutorial)

- [Linux kernel profiling with `perf`](#linux-kernel-profiling-with-perf)
  - [1 Introduction](#1-introduction)
    - [1.1 Commands](#11-commands)
    - [1.2 Events](#12-events)
      - [1.2.1 Hardware events](#121-hardware-events)
  - [2 Counting with `perf stat`](#2-counting-with-perf-stat)
    - [2.1 Options controlling event selection](#21-options-controlling-event-selection)
      - [2.1.1 Modifiers](#211-modifiers)

## 1 Introduction

Perf is a profiler tool for Linux 2.6+ based systems that abstracts away CPU hardware differences in Linux performance measurements and presents a simple commandline interface. Perf is based on the `perf_events` interface exported by recent versions of the Linux kernel. This article demonstrates the `perf` tool through example runs. Output was obtained on a Ubuntu 11.04 system with kernel 2.6.38-8-generic results running on an HP 6710b with dual-core Intel Core2 T7100 CPU). For readability, some output is abbreviated using ellipsis (`[...]`).

### 1.1 Commands

The perf tool offers a rich set of commands to collect and analyze performance and trace data. The command line usage is reminiscent of `git` in that there is a generic tool, `perf`, which implements a set of commands: `stat`, `record`, `report`, `[...]`

The list of supported commands:

    perf
    
     usage: perf [--version] [--help] COMMAND [ARGS]
    
     The most commonly used perf commands are:
      annotate        Read perf.data (created by perf record) and display annotated code
      archive         Create archive with object files with build-ids found in perf.data file
      bench           General framework for benchmark suites
      buildid-cache   Manage <tt>build-id</tt> cache.
      buildid-list    List the buildids in a perf.data file
      diff            Read two perf.data files and display the differential profile
      inject          Filter to augment the events stream with additional information
      kmem            Tool to trace/measure kernel memory(slab) properties
      kvm             Tool to trace/measure kvm guest os
      list            List all symbolic event types
      lock            Analyze lock events
      probe           Define new dynamic tracepoints
      record          Run a command and record its profile into perf.data
      report          Read perf.data (created by perf record) and display the profile
      sched           Tool to trace/measure scheduler properties (latencies)
      script          Read perf.data (created by perf record) and display trace output
      stat            Run a command and gather performance counter statistics
      test            Runs sanity tests.
      timechart       Tool to visualize total system behavior during a workload
      top             System profiling tool.

     See 'perf help COMMAND' for more information on a specific command.

Certain commands require special support in the kernel and may not be available. To obtain the list of options for each command, simply type the command name followed by `-h`:

    perf stat -h
    
     usage: perf stat [<options>] [<command>]
    
        -e, --event <event>   event selector. use 'perf list' to list available events
        -i, --no-inherit      child tasks do not inherit counters
        -p, --pid <n>         stat events on existing process id
        -t, --tid <n>         stat events on existing thread id
        -a, --all-cpus        system-wide collection from all CPUs
        -c, --scale           scale/normalize counters
        -v, --verbose         be more verbose (show counter open errors, etc)
        -r, --repeat <n>      repeat command and print average + stddev (max: 100)
        -n, --null            null run - dont start any counters
        -B, --big-num         print large numbers with thousands' separators

### 1.2 Events

The `perf` tool supports a list of measurable events. The tool and underlying kernel interface can measure events coming from different sources. For instance, some event are pure kernel counters, in this case they are called **software events**. Examples include: context-switches, minor-faults.

Another source of events is the processor itself and its Performance Monitoring Unit (PMU). It provides a list of events to measure micro-architectural events such as the number of cycles, instructions retired, L1 cache misses and so on. Those events are called **PMU hardware events** or **hardware events** for short. They vary with each processor type and model.

The `perf_events` interface also provides a small set of common hardware events monikers. On each processor, those events get mapped onto an actual events provided by the CPU, if they exists, otherwise the event cannot be used. Somewhat confusingly, these are also called **hardware events** and **hardware cache events**.

Finally, there are also **tracepoint events** which are implemented by the kernel `ftrace` infrastructure. Those are only available with the 2.6.3x and newer kernels.

To obtain a list of supported events:

    perf list
    
    List of pre-defined events (to be used in -e):
    
     cpu-cycles OR cycles                       [Hardware event]
     instructions                               [Hardware event]
     cache-references                           [Hardware event]
     cache-misses                               [Hardware event]
     branch-instructions OR branches            [Hardware event]
     branch-misses                              [Hardware event]
     bus-cycles                                 [Hardware event]
    
     cpu-clock                                  [Software event]
     task-clock                                 [Software event]
     page-faults OR faults                      [Software event]
     minor-faults                               [Software event]
     major-faults                               [Software event]
     context-switches OR cs                     [Software event]
     cpu-migrations OR migrations               [Software event]
     alignment-faults                           [Software event]
     emulation-faults                           [Software event]
    
     L1-dcache-loads                            [Hardware cache event]
     L1-dcache-load-misses                      [Hardware cache event]
     L1-dcache-stores                           [Hardware cache event]
     L1-dcache-store-misses                     [Hardware cache event]
     L1-dcache-prefetches                       [Hardware cache event]
     L1-dcache-prefetch-misses                  [Hardware cache event]
     L1-icache-loads                            [Hardware cache event]
     L1-icache-load-misses                      [Hardware cache event]
     L1-icache-prefetches                       [Hardware cache event]
     L1-icache-prefetch-misses                  [Hardware cache event]
     LLC-loads                                  [Hardware cache event]
     LLC-load-misses                            [Hardware cache event]
     LLC-stores                                 [Hardware cache event]
     LLC-store-misses                           [Hardware cache event]
    
     LLC-prefetch-misses                        [Hardware cache event]
     dTLB-loads                                 [Hardware cache event]
     dTLB-load-misses                           [Hardware cache event]
     dTLB-stores                                [Hardware cache event]
     dTLB-store-misses                          [Hardware cache event]
     dTLB-prefetches                            [Hardware cache event]
     dTLB-prefetch-misses                       [Hardware cache event]
     iTLB-loads                                 [Hardware cache event]
     iTLB-load-misses                           [Hardware cache event]
     branch-loads                               [Hardware cache event]
     branch-load-misses                         [Hardware cache event]
    
     rNNN (see 'perf list --help' on how to encode it) [Raw hardware event descriptor]
    
     mem:<addr>[:access]                        [Hardware breakpoint]
    
     kvmmmu:kvm_mmu_pagetable_walk              [Tracepoint event]
    
     [...]
    
     sched:sched_stat_runtime                   [Tracepoint event]
     sched:sched_pi_setprio                     [Tracepoint event]
     syscalls:sys_enter_socket                  [Tracepoint event]
     syscalls:sys_exit_socket                   [Tracepoint event]
    
     [...]

An event can have sub-events (or unit masks). On some processors and for some events, it may be possible to combine unit masks and measure when either sub-event occurs. Finally, an event can have modifiers, i.e., filters which alter when or how the event is counted.

#### 1.2.1 Hardware events

PMU hardware events are CPU specific and documented by the CPU vendor. The `perf` tool, if linked against the `libpfm4` library, provides some short description of the events. For a listing of PMU hardware events for Intel and AMD processors, see

- Intel PMU event tables: Appendix A of manual [here](http://www.intel.com/Assets/PDF/manual/253669.pdf)

- AMD PMU event table: section 3.14 of manual [here](http://support.amd.com/us/Processor_TechDocs/31116.pdf)

## 2 Counting with `perf stat`

For any of the supported events, `perf` can keep a running count during process execution. In **counting modes**, the occurrences of events are simply aggregated and presented on standard output at the end of an application run. To generate these statistics, use the `stat` command of `perf`. For instance:

    perf stat -B dd if=/dev/zero of=/dev/null count=1000000
    
    1000000+0 records in
    1000000+0 records out
    512000000 bytes (512 MB) copied, 0.956217 s, 535 MB/s
    
     Performance counter stats for 'dd if=/dev/zero of=/dev/null count=1000000':
    
                5,099 cache-misses             #      0.005 M/sec (scaled from 66.58%)
              235,384 cache-references         #      0.246 M/sec (scaled from 66.56%)
            9,281,660 branch-misses            #      3.858 %     (scaled from 33.50%)
          240,609,766 branches                 #    251.559 M/sec (scaled from 33.66%)
        1,403,561,257 instructions             #      0.679 IPC   (scaled from 50.23%)
        2,066,201,729 cycles                   #   2160.227 M/sec (scaled from 66.67%)
                  217 page-faults              #      0.000 M/sec
                    3 CPU-migrations           #      0.000 M/sec
                   83 context-switches         #      0.000 M/sec
           956.474238 task-clock-msecs         #      0.999 CPUs
    
           0.957617512  seconds time elapsed

With no events specified, `perf stat` collects the common events listed above. Some are software events, such as `context-switches`, others are generic hardware events such as `cycles`. After the hash sign, derived metrics may be presented, such as 'IPC' (instructions per cycle).

### 2.1 Options controlling event selection

It is possible to measure one or more events per run of the perf tool. Events are designated using their symbolic names followed by optional unit masks and modifiers. Event names, unit masks, and modifiers are case insensitive.

By default, events are measured at both user and kernel levels:

perf stat -e cycles dd if=/dev/zero of=/dev/null count=100000
To measure only at the user level, it is necessary to pass a modifier:

perf stat -e cycles:u dd if=/dev/zero of=/dev/null count=100000
To measure both user and kernel (explicitly):

perf stat -e cycles:uk dd if=/dev/zero of=/dev/null count=100000

#### 2.1.1 Modifiers

















TODO perf pppppppppppppppppppppppp
