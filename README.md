# qemu-system-aarch64-baremetal

The purpose of this document is to:
* Print "Hello world!" in qemu-system-aarch64 with a chunk of pure assembly codes.

## First attempt

Let's see our binary is actually running.  
For this, we will assign some numbers to registers.

boot.asm:
```as
mov x1, #1
mov x2, #2
b .
```

Compile this with `aarch64-ubuntu-linux-gnu-*` like so:
```bash
aarch64-ubuntu-linux-gnu-as -o boot.elf boot.asm
aarch64-ubuntu-linux-gnu-objcopy -O binary boot.elf boot.bin
```

Emulate this with QEMU:
```bash
qemu-system-aarch64 -machine virt -cpu cortex-a72 -nographic -kernel boot.bin -monitor telnet:127.0.0.1:55555,server,nowait
```

The `-kernel` option allows QEMU to run non-elf files. Also the document says:
> For guests using the Linux kernel boot protocol (this means any non-ELF file passed to the QEMU -kernel option) the address of the DTB is passed in a register (r2 for 32-bit guests, or x0 for 64-bit guests)

Access to qemu-monitor:
```bash
telnet localhost 55555
```

See the register values:
```
(qemu) info registers
```

Below shows `X01` and `X02` have changed.  
```
 PC=0000000040080008 X00=0000000044000000 X01=0000000000000001
X02=0000000000000002 X03=0000000000000000 X04=0000000040080000
X05=0000000000000000 X06=0000000000000000 X07=0000000000000000
X08=0000000000000000 X09=0000000000000000 X10=0000000000000000
X11=0000000000000000 X12=0000000000000000 X13=0000000000000000
X14=0000000000000000 X15=0000000000000000 X16=0000000000000000
X17=0000000000000000 X18=0000000000000000 X19=0000000000000000
X20=0000000000000000 X21=0000000000000000 X22=0000000000000000
X23=0000000000000000 X24=0000000000000000 X25=0000000000000000
X26=0000000000000000 X27=0000000000000000 X28=0000000000000000
X29=0000000000000000 X30=0000000000000000  SP=0000000000000000
PSTATE=400003c5 -Z-- EL1h    FPU disabled
```
