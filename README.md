A custom iperf 
================================================================
This repository is a fork of the original iperf maintained by esnet: https://github.com/esnet/iperf

Any change made to the repository is marked similar to this:

    //[HEMA]====...
    [...]
    //==========...


Therefore, a simple

    grep -r HEMA

will show all changes made from the original repository.

Some features have been added to aid my personal research.

NAPI ID Support
---------------
This feature adds the information "napi_id" to the JSON output when running iperf.
The NAPI ID is reported for every stream in the intervals output.
If the NAPI ID cannot be retrieved, the value in the JSON is set to the negative return value of getsockopt. This will also print a message to stderr.

This information is helpful because it can provide insights into which connections are mapped to the same RX queue.
Each RX queue has a unique NAPI ID.
If the napi ID of two connections is the same, they were received by the same RX queue.
There is no way - to my knowledge - of mapping the NAPI ID to RX queue ids. 
It is only guaranteed that the IDs are assigned in ascending order.
If you have Rx queues 0, 1, 2, and 3 for example, they will be mapped to NAPI IDs like 8234, 8235, 8236, 8237.
Whether this behavior is stable, I do not know.

The iperf implementation does not make any necessary checks on whether your kernel supports the SO_INCOMING_NAPI_ID.
It is supported since Linux 4.12 according to https://man7.org/linux/man-pages/man7/socket.7.html .


Server RX Packet Timestamps (Work in Progress)
---------------------------

This introduces a new flag:

    --server-rx-timestamp        Activate RX timestamp collection on server

As the description says, it activates timestamping RX timestamps using SOF_TIMESTAMPING_RX_SOFTWARE.
These timestamps are taken just before a packet enters the network stack (Check for net_timestamp_check() in your kernel for the exact positions).

More on timestamping: https://docs.kernel.org/networking/timestamping.html

So far, the timestamps are not yet collected. Work in Progress.





iperf3:  A TCP, UDP, and SCTP network bandwidth measurement tool
================================================================

Summary
-------

iperf is a tool for active measurements of the maximum achievable
bandwidth on IP networks.  It supports tuning of various parameters
related to timing, protocols, and buffers.  For each test it reports
the measured throughput / bitrate, loss, and other parameters.

This version, sometimes referred to as iperf3, is a redesign of an
original version developed at NLANR/DAST.  iperf3 is a new
implementation from scratch, with the goal of a smaller, simpler code
base, and a library version of the functionality that can be used in
other programs. iperf3 also has a number of features found in other tools
such as nuttcp and netperf, but were missing from the original iperf.
These include, for example, a zero-copy mode and optional JSON output.
Note that iperf3 is *not* backwards compatible with the original iperf.

Primary development for iperf3 takes place on Ubuntu Linux, FreeBSD,
and macOS.  At this time, these are the only officially supported
platforms, however there have been some reports of success with
OpenBSD, NetBSD, Android, Solaris, and other Linux distributions.

iperf3 is principally developed by ESnet / Lawrence Berkeley National
Laboratory.  It is released under a three-clause BSD license.

For more information see: https://software.es.net/iperf

Source code and issue tracker: https://github.com/esnet/iperf

Discussion forums: https://github.com/esnet/iperf/discussions

Reporting security vulnerabilities: iperf@es.net

Obtaining iperf3
----------------

Downloads of iperf3 are available at:

    https://downloads.es.net/pub/iperf/

To check out the most recent code, clone the git repository at:

    https://github.com/esnet/iperf.git

Building iperf3
---------------

### Prerequisites: ###

None.

### Building ###

    ./configure; make; make install

(Note: If configure fails, try running `./bootstrap.sh` first)

Invoking iperf3
---------------

iperf3 includes a manual page listing all of the command-line options.
The manual page is the most up-to-date reference to the various flags and parameters.

For sample command line usage, see:

https://fasterdata.es.net/performance-testing/network-troubleshooting-tools/iperf/

Using the default options, iperf is meant to show typical well
designed application performance.  "Typical well designed application"
means avoiding artificial enhancements that work only for testing
(such as splice()'ing the data to /dev/null).  iperf does also have
flags for "extreme best case" optimizations, but they must be
explicitly activated.

These flags include:

    -Z, --zerocopy            use a 'zero copy' sendfile() method of sending data
    -A, --affinity n/n,m      set CPU affinity

Bug and Security Reports
------------------------

Before submitting a bug report, please make sure you're running the
latest version of the code, and confirm that your issue has not
already been fixed.  Then submit to the iperf3 issue tracker on
GitHub:

https://github.com/esnet/iperf/issues

In your issue submission, please indicate the version of iperf3 and
what platform you're trying to run on (provide the platform
information even if you're not using a supported platform, we
*might* be able to help anyway).  Exact command-line arguments will
help us recreate your problem.  If you're getting error messages,
please include them verbatim if possible, but remember to sanitize any
sensitive information.

If you have a question about usage or about the code, please do *not*
submit an issue.  Please use one of the mailing lists for that.

If you suspect there is a potential security issue, please contact the
developers at:

iperf@es.net

Relation to iperf 2.x
---------------------

Although iperf2 and iperf3 both measure network performance,
they are not compatible with each other.
The projects (as of mid-2021) are in active, but separate, development.
The continuing iperf2 development
project can be found at https://sourceforge.net/projects/iperf2/.

Known Issues
------------

A set of known issues is maintained on the iperf3 Web pages:

https://software.es.net/iperf/dev.html#known-issues

Links
-----

This section lists links to user-contributed Web pages regarding
iperf3.  ESnet and Lawrence Berkeley National Laboratory bear no
responsibility for the content of these pages.

* Installation instructions for Debian Linux (by Cameron Camp
  <cameron@ivdatacenter.com>):

  http://cheatsheet.logicalwebhost.com/iperf-network-testing/

Copyright
---------

iperf, Copyright (c) 2014-2024, The Regents of the University of
California, through Lawrence Berkeley National Laboratory (subject
to receipt of any required approvals from the U.S. Dept. of
Energy).  All rights reserved.

If you have questions about your rights to use or distribute this
software, please contact Berkeley Lab's Technology Transfer
Department at TTD@lbl.gov.

NOTICE.  This software is owned by the U.S. Department of Energy.
As such, the U.S. Government has been granted for itself and others
acting on its behalf a paid-up, nonexclusive, irrevocable,
worldwide license in the Software to reproduce, prepare derivative
works, and perform publicly and display publicly.  Beginning five
(5) years after the date permission to assert copyright is obtained
from the U.S. Department of Energy, and subject to any subsequent
five (5) year renewals, the U.S. Government is granted for itself
and others acting on its behalf a paid-up, nonexclusive,
irrevocable, worldwide license in the Software to reproduce,
prepare derivative works, distribute copies to the public, perform
publicly and display publicly, and to permit others to do so.

This code is distributed under a BSD style license, see the LICENSE
file for complete information.
