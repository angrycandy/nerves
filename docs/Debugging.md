# Debugging Nerves Systems

## Obtaining a core dump from vm crashes

To obtain a core dump if beam crashes, you'll need a few things first:

* core dumps enabled in the Linux Kernal (this is on by default for all officially supported targets)
  * `CONFIG_ELF_CORE` and `CONFIG_COREDUMP` both must be `=Y` in the kernel config
* process core size is greater than zero
  * this can be checked with `cmd("ulimit -c")` and set with `cmd("ulimit c unlimited")`
* core dumps must be configured to save to a writable location
  * `File.write!("/proc/sys/kernel/core_pattern", "/data/core-%e-%p-%h")`

Once these conditions are met, if the VM crashes for any reason, it will create a file
that can be loaded into a debugger to analyse what happened.

First grab the file from the filesystem. If your target boots, you can pull it with `sftp`:

```shell
sftp nerves.local
sftp> cd /data
sftp> get core-beam.smp-41-1622824880
```

Next, there's a helper mix task that will generate a small wrapper around gdb:

```shell
mix nerves.firmware.gen.gdb
./gdb.sh [core dump file you pulled]
```
