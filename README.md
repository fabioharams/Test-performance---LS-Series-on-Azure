# Test-performance---LS-Series-on-Azure
Test IOPS performance on LS Series (Storage optimized VMs) on Azure

Microsoft has a series of virtual machines indicated for customers who have NoSQL database (MongoDB, Cassandra, etc.). This series is called LS and has optimization for storage. Below you will find IOPS tests using local disks.

More information here: [LS-series on Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-windows-sizes-storage)

This article is based on procedures listed here: [How to benchmark disk I/O](https://www.binarylane.com.au/support/solutions/articles/1000055889-how-to-benchmark-disk-i-o)

## Requirements ##
* Virtual machine using Linux
* FIO (tool to measure IOPS on Linux)

## Installing FIO ##
``
cd /root
yum install -y make gcc libaio-devel || ( apt-get update && apt-get install -y make gcc libaio-dev  </dev/null )
wget https://github.com/Crowd9/Benchmark/raw/master/fio-2.0.9.tar.gz ; tar xf fio*
cd fio*
make
``

## Standard_L16S ##
This virtual machine has the following configuration:
* 16 CPU Cores
* 128 GiB Memory
* 2,807 Local SSD GiB
* 32 max data disks
* 20,000/500 (Max uncached disk throughput: IOPS/Mbps)
* 8 extremely high (Max NICs / Network bandwidth)

### 1) Random read/write performance
Executing the following command:

``
./fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=test --bs=4k --iodepth=64 --size=4G --readwrite=randrw --rwmixread=75
test: (g=0): rw=randrw, bs=4K-4K/4K-4K, ioengine=libaio, iodepth=64
``

Results

``
<p>fio-2.0.9
Starting 1 process
test: Laying out IO file(s) (1 file(s) / 4096MB)
Jobs: 1 (f=1): [m] [94.1% done] [240.5M/81164K /s] [61.6K/20.3K iops] [eta 00m:03s]
test: (groupid=0, jobs=1): err= 0: pid=7575: Tue Jun 13 18:21:30 2017</p>
  <p>read : io=3074.9MB, bw=65987KB/s, ==iops=16496== , runt= 47716msec
  write: io=1021.2MB, bw=21914KB/s, ==iops=5478== , runt= 47716msec
  cpu          : usr=1.62%, sys=9.51%, ctx=15300, majf=0, minf=17
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%</p>
    <p> submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%</p>
     <p>complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued    : total=r=787161/w=261415/d=0, short=r=0/w=0/d=0</p>
<p>Run status group 0 (all jobs):</p>
   READ: io=3074.9MB, aggrb=65987KB/s, minb=65987KB/s, maxb=65987KB/s, mint=47716msec, maxt=47716msec
  WRITE: io=1021.2MB, aggrb=21914KB/s, minb=21914KB/s, maxb=21914KB/s, mint=47716msec, maxt=47716msec
<p>Disk stats (read/write):</p>
  sda: ios=784110/260410, merge=0/6, ticks=1991106/1444250, in_queue=3435293, util=99.57%
``

==IOPS Read: 16,496==
==IOPS Write: 5478==

### 2) Random read performance ###
Executing the following command:

``
./fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=test --bs=4k --iodepth=64 --size=4G --readwrite=randread
e=test --bs=4k --iodepth=64 --size=4G --readwrite=randread
``

Results

``
<p>fio-2.0.9</p>
Starting 1 process
<p>Jobs: 1 (f=1): [r] [100.0% done] [317.5M/0K /s] [81.3K/0  iops] [eta 00m:00s]
test: (groupid=0, jobs=1): err= 0: pid=7578: Tue Jun 13 18:22:54 2017</p>
  <p>read : io=4096.0MB, bw=326405KB/s, iops=81601 , runt= 12850msec
  cpu          : usr=5.22%, sys=32.63%, ctx=18740, majf=0, minf=83
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%</P>
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued    : total=r=1048576/w=0/d=0, short=r=0/w=0/d=0
<p>Run status group 0 (all jobs):</p>
   READ: io=4096.0MB, aggrb=326404KB/s, minb=326404KB/s, maxb=326404KB/s, mint=12850msec, maxt=12850msec
<p>Disk stats (read/write):</p>
  sda: ios=1033857/2, merge=0/0, ticks=670969/3, in_queue=670900, util=98.18%
``

==IOPS Read: 81,601==

### 3) Random write performance ###
Executing the following command:

``
./fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=test --bs=4k --iodepth=64 --size=4G --readwrite=randwrite
test: (g=0): rw=randwrite, bs=4K-4K/4K-4K, ioengine=libaio, iodepth=64
``

Results

``
<p>fio-2.0.9</p>
Starting 1 process
<p>Jobs: 1 (f=1): [w] [98.2% done] [0K/318.5M /s] [0 /81.6K iops] [eta 00m:01s]</p>
test: (groupid=0, jobs=1): err= 0: pid=7581: Tue Jun 13 18:25:01 2017
  <p>write: io=4096.0MB, bw=75890KB/s, iops=18972 , runt= 55268msec
  cpu          : usr=1.70%, sys=9.79%, ctx=12788, majf=0, minf=17
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%</p>
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued    : total=r=0/w=1048576/d=0, short=r=0/w=0/d=0
<p>Run status group 0 (all jobs):</p>
  WRITE: io=4096.0MB, aggrb=75890KB/s, minb=75890KB/s, maxb=75890KB/s, mint=55268msec, maxt=55268msec
<p>Disk stats (read/write):</p>
  sda: ios=0/1044310, merge=0/1, ticks=0/3685294, in_queue=3686309, util=99.57%
``

==Random Write: 18,972==


## Standard_L32S ##
This virtual machine has the following configuration:
* 32 CPU Cores
* 256 GiB Memory
* 5,630 Local SSD GiB
* 64 max data disks
* 40,000/1,000 (Max uncached disk throughput: IOPS/Mbps)
* 8 extremely high (Max NICs / Network bandwidth)

### 1) Random read/write performance
Executing the following command:

``
./fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=test --bs=4k --iodepth=64 --size=4G --readwrite=randrw --rwmixread=75
test: (g=0): rw=randrw, bs=4K-4K/4K-4K, ioengine=libaio, iodepth=64
``

Results

``
<p>./fio-2.0.9</p>
Starting 1 process
<p>Jobs: 1 (f=1): [m] [100.0% done] [2309K/763K /s] [577 /190  iops] [eta 00m:00s]</p>
test: (groupid=0, jobs=1): err= 0: pid=1272: Tue Jun 13 19:15:43 2017
  <p>read : io=3072.3MB, bw=1565.9KB/s, iops=391 , runt=2009108msec
  write: io=1023.8MB, bw=534320 B/s, iops=130 , runt=2009108msec
  cpu          : usr=0.11%, sys=0.75%, ctx=274804, majf=0, minf=18
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%</p>
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued    : total=r=786489/w=262087/d=0, short=r=0/w=0/d=0
<p>Run status group 0 (all jobs):</p>
   READ: io=3072.3MB, aggrb=1565KB/s, minb=1565KB/s, maxb=1565KB/s, mint=2009108msec, maxt=2009108msec
  WRITE: io=1023.8MB, aggrb=521KB/s, minb=521KB/s, maxb=521KB/s, mint=2009108msec, maxt=2009108msec
<p>Disk stats (read/write):</p>
  sda: ios=786441/262597, merge=0/12, ticks=123455156/6146267, in_queue=129605932, util=100.00%

==IOPS Read: 391==

==IOPS Write: 130==

### 2) Random read performance ###
Executing the following command:

``
./fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=test --bs=4k --iodepth=64 --size=4G --readwrite=randread
e=test --bs=4k --iodepth=64 --size=4G --readwrite=randread
``

Results

``
<p>fio-2.0.9</p>
Starting 1 process
<p>Jobs: 1 (f=1): [r] [100.0% done] [635.6M/0K /s] [163K/0  iops] [eta 00m:00s]
test: (groupid=0, jobs=1): err= 0: pid=1300: Tue Jun 13 19:25:49 2017</p>
  read : io=4096.0MB, bw=641331KB/s, iops=160332 , runt=  6540msec
  cpu          : usr=8.21%, sys=55.96%, ctx=19689, majf=0, minf=83
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%
     <p>submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued    : total=r=1048576/w=0/d=0, short=r=0/w=0/d=0</p>
<p>Run status group 0 (all jobs):</p>
   READ: io=4096.0MB, aggrb=641330KB/s, minb=641330KB/s, maxb=641330KB/s, mint=6540msec, maxt=6540msec
<p>Disk stats (read/write):</p>
  sda: ios=1028032/0, merge=0/0, ticks=309448/0, in_queue=309419, util=97.23%

==IOPS Read: 160,332==

### 3) Random write performance ###
Executing the following command:

``
./fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=test --bs=4k --iodepth=64 --size=4G --readwrite=randwrite
test: (g=0): rw=randwrite, bs=4K-4K/4K-4K, ioengine=libaio, iodepth=64
``

Results

``
<p>fio-2.0.9</p>
Starting 1 process
<p>Jobs: 1 (f=1): [w] [92.6% done] [0K/256K /s] [0 /64  iops] [eta 00m:04s]
test: (groupid=0, jobs=1): err= 0: pid=1303: Tue Jun 13 19:27:21 2017</p>
  write: io=4096.0MB, bw=84694KB/s, iops=21173 , runt= 49523msec
  cpu          : usr=1.34%, sys=9.81%, ctx=14058, majf=0, minf=17
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%
     <p>submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued    : total=r=0/w=1048576/d=0, short=r=0/w=0/d=0</p>
<p>Run status group 0 (all jobs):</p>
  WRITE: io=4096.0MB, aggrb=84694KB/s, minb=84694KB/s, maxb=84694KB/s, mint=49523msec, maxt=49523msec
<p>Disk stats (read/write):</p>
  sda: ios=0/1035789, merge=0/0, ticks=0/3366634, in_queue=3366483, util=99.71%

==IOPS write: 21,173==


