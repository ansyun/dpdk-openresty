#### dpdk-openresty
--------------
dpdk-openresty fork from official openresty-1.13.6.2, and run on the dpdk user space TCP/IP stack(ANS). For detail function, please refer to openresty official website(http://http://openresty.org/).

#### Build and install
--------------
*  Download latest dpdk version from [dpdk website](http://dpdk.org/)
```
$ make config T=x86_64-native-linuxapp-gcc
$ make install T=x86_64-native-linuxapp-gcc
$ export RTE_SDK=/home/mytest/dpdk
$ export RTE_TARGET=x86_64-native-linuxapp-gcc
```
*  Build dpdk and ANS following the [ANS wiki](https://github.com/ansyun/dpdk-ans/wiki/Compile-APP-with-ans) 
```
$ git clone https://github.com/ansyun/dpdk-ans.git
$ export RTE_ANS=/home/mytest/dpdk-ans
$ ./install_deps.sh
$ cd ans
$ make
$ sudo ./build/ans -c 0x2 -n 1  -- -p 0x1 --config="(0,0,1)"
EAL: Detected lcore 0 as core 0 on socket 0
EAL: Detected lcore 1 as core 1 on socket 0
EAL: Support maximum 128 logical core(s) by configuration.
EAL: Detected 2 lcore(s)
EAL: VFIO modules not all loaded, skip VFIO support...
EAL: Setting up physically contiguous memory...
EAL: Ask a virtual area of 0x400000 bytes
EAL: Virtual area found at 0x7fdf90c00000 (size = 0x400000)
EAL: Ask a virtual area of 0x15400000 bytes
```
*  Download dpdk-openresty, build dpdk-openresty

```
$ git clone https://github.com/ansyun/dpdk-openresty.git
$ ./configure  --with-http_dav_module
$ make
$ make install   # default install dir is /usr/local/openresty
```
#### Testing
--------------
*  Setup DPDK Environment

Refer to [Getting Started Guide for Linux](http://dpdk.org/doc/guides/linux_gsg/quick_start.html)

*  Startup ANS TCP/IP stack
```
$ sudo ./build/ans -c 0x2 -n 1  -- -p 0x1 --config="(0,0,1)"
EAL: Detected lcore 0 as core 0 on socket 0
EAL: Detected lcore 1 as core 1 on socket 0
EAL: Support maximum 128 logical core(s) by configuration.
EAL: Detected 2 lcore(s)
EAL: VFIO modules not all loaded, skip VFIO support...
EAL: Setting up physically contiguous memory...
...
```


#### Notes
* Shall use the same gcc version to compile your application.
* ANS tcp stack support reuseport, so can enable openresty reuseport feature, multi openresty can listen on same port.
* proxy_pass is supported.
* In order to improve ANS performance, you shall isolate ANS'lcore from kernel by isolcpus and isolcate interrupt from ANS's lcore by update /proc/irq/default_smp_affinity file.
* You shall include dpdk libs as below way because mempool lib has __attribute__((constructor, used)) in dpdk-16.07 version, otherwise your application would coredump.
```
   $(RTE_ANS)/librte_anssock/librte_anssock.a \
  -L$(RTE_SDK)/$(RTE_TARGET)/lib \
  -Wl,--whole-archive -Wl,-lrte_mbuf -Wl,-lrte_mempool -Wl,-lrte_ring -Wl,-lrte_eal -Wl,--no-whole-archive -Wl,-export-dynamic -lnuma \

```

#### Support
-------
For free support, please use ANS team mail list at anssupport@163.com, or QQ Group:86883521, or https://dpdk-ans.slack.com.
