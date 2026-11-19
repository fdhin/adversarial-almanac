---
name: "sgx-step-install"
categories:
  - hardware
---

# Installing sgx-step

Requirements: an [SGX-capable](https://github.com/ayeks/SGX-hardware) Intel processor and an off-the-shelf Linux kernel (original evaluation: i7-6500U/6700, Ubuntu 18.04, kernel 4.15; recent kernels also supported). Optionally disable C-States and SpeedStep in the BIOS for stabler timing.

1. Kernel boot parameters — edit `/etc/default/grub`, then `sudo update-grub && reboot`:

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash iomem=relaxed no_timer_check clearcpuid=308,295,514 pti=off isolcpus=1 nmi_watchdog=0 rcupdate.rcu_cpu_stall_suppress=1 msr.allow_writes=on vdso=0"
```

(Disables SMAP/SMEP/UMIP, KPTI and the NMI watchdog so user-space IRQ handlers survive; `nox2apic` is an alternative for memory-mapped APIC mode — set X2APIC to 0 in libsgxstep/config.h if you use it.)

2. Driver and patched SDK, from the repo root:

```sh
cd kernel/ && ./install_SGX_driver.sh && make clean load
cd ../sdk/intel-sdk/ && ./install_SGX_SDK.sh
source /opt/intel/sgxsdk/environment
```

The install scripts are tested on Ubuntu 20.04/22.04; other distributions follow the linux-sgx project's own build instructions. A local SDK install is supported by pointing the `SGX_SDK` environment variable at it.

Verification: `sudo ./check_sys.sh` reports OK on all recommended parameters.

Backlinks: [[hardware]] · [[sgx-step]]
