Android Low Memory Killer Daemon
================================


Introduction
------------

Android Low Memory Killer Daemon (lmkd) is a process monitoring memory
state of a running Android system and reacting to high memory pressure
by killing the least essential process(es) to keep system performing
at acceptable levels.


Background
----------

Historically on Android systems memory monitoring and killing of
non-essential processes was handled by a kernel lowmemorykiller driver.
Since Linux Kernel 4.12 the lowmemorykiller driver has been removed and
instead userspace lmkd daemon performs these tasks.


Android Properties
------------------

lmkd can be configured on a particular system using the following Android
properties:

  - `ro.config.low_ram`:         choose between low-memory vs high-performance
                                 device. Default = false.

  - `ro.lmk.use_minfree_levels`: use free memory and file cache thresholds for
                                 making decisions when to kill. This mode works
                                 the same way kernel lowmemorykiller driver used
                                 to work. Default = false

  - `ro.lmk.low`:                min oom_adj score for processes eligible to be
                                 killed at low vmpressure level. Default = 1001
                                 (disabled)

  - `ro.lmk.medium`:             min oom_adj score for processes eligible to be
                                 killed at medium vmpressure level. Default = 800
                                 (non-essential processes)

  - `ro.lmk.critical`:           min oom_adj score for processes eligible to be
                                 killed at critical vmpressure level. Default = 0
                                 (all processes)

  - `ro.lmk.critical_upgrade`:   enables upgrade to critical level. Default = false

  - `ro.lmk.upgrade_pressure`:   max mem_pressure at which level will be upgraded
                                 because system is swapping too much. Default = 100
                                 (disabled)

  - `ro.lmk.downgrade_pressure`: min mem_pressure at which vmpressure event will
                                 be ignored because enough free memory is still
                                 available. Default = 100 (disabled)

  - `ro.lmk.kill_heaviest_task`: kill heaviest eligible task (best decision) vs.
                                 any eligible task (fast decision). Default = false

  - `ro.lmk.kill_timeout_ms`:    duration in ms after a kill when no additional
                                 kill will be done. Default = 100

  - `ro.lmk.debug`:              enable lmkd debug logs, Default = false

  - `ro.lmk.swap_free_low_percentage`: level of free swap as a percentage of the
                                 total swap space used as a threshold to consider
                                 the system as swap space starved. Default = 10

  - `ro.lmk.thrashing_limit`:    number of workingset refaults as a percentage of
                                the file-backed pagecache size used as a threshold
                                 to consider system thrashing its pagecache.
                                 Default for low-RAM devices = 30, for high-end
                                 devices = 100

  - `ro.lmk.thrashing_limit_decay`: thrashing threshold decay expressed as a
                                 percentage of the original threshold used to lower
                                 the threshold when system does not recover even
                                 after a kill. Default for low-RAM devices = 50,
                                 for high-end devices = 10

  - `ro.lmk.psi_partial_stall_ms`: partial PSI stall threshold in milliseconds for
                                 triggering low memory notification. Default for
                                 low-RAM devices = 200, for high-end devices = 70

  - `ro.lmk.psi_complete_stall_ms`: complete PSI stall threshold in milliseconds for
                                 triggering critical memory notification. Default =
                                 700
  - `ro.lmk.pressure_after_kill_min_score`: min oom_adj_score score threshold for
                                 cycle after kill used to allow blocking of killing
                                 critical processes when not enough memory was freed
                                 in a kill cycle. Default score = 0.
  - `ro.lmk.direct_reclaim_threshold_ms`: direct reclaim duration threshold in
                                 milliseconds to consider the system as stuck in
                                 direct reclaim. Default = 0 (disabled)
  - `ro.lmk.swap_compression_ratio`: swap average compression ratio to be used when
                                 estimating how much data can be swapped. Setting it
                                 to 0 will ignore available memory and assume that
                                 configured swap size can be always utilized fully.
                                 Default = 1 (no compression).
  - `ro.lmk.lowmem_min_oom_score`: min oom_score_adj level used to select processes
                                 to kill when memory is critically low. Setting it
                                 to 1001 will prevent any kills for this reason. Min
                                 acceptable value is 201 (apps up to perceptible).
                                 Default = 701 (all cached apps excluding the last
                                 active one).

lmkd will set the following Android properties according to current system
configurations:

  - `sys.lmk.minfree_levels`:    minfree:oom_adj_score pairs, delimited by comma

  - `sys.lmk.reportkills`:       whether or not it supports reporting process kills
                                 to clients. Test app should check this property
                                 before testing low memory kill notification.
                                 Default will be unset.
