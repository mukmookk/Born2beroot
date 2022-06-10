
##  cat /proc/cpuinfo | grep "physical id" |  uniq –dc | wc –l

```
$ cat /proc/cpuinfo
...(생략)
processor       : 7
vendor_id       : GenuineIntel
cpu family      : 6
model           : 44
model name      : Intel(R) Xeon(R) CPU           E5620  @ 2.40GHz
stepping        : 2
cpu MHz         : 2400.212
cache size      : 12288 KB
physical id     : 1
siblings        : 8
core id         : 10
cpu cores       : 4
apicid          : 53
fdiv_bug        : no
hlt_bug         : no
f00f_bug        : no
coma_bug        : no
fpu             : yes
fpu_exception   : yes
cpuid level     : 11
wp              : yes



$ cat /proc/cpuinfo | grep "physical id"
physical id     : 1
physical id     : 1
physical id     : 1
physical id     : 1
physical id     : 1
physical id     : 1
physical id     : 1
physical id     : 1


$ cat /proc/cpuinfo | grep "physical id" |  uniq –dc | wc –l
>  물리적 CPU 갯수
```
"cpu cores : 4” 물리적 CPU 1개당 코어수 입니다.

cpuinfo 에서는 총 8개에 processor -> siblings가 8개 라는 건 HT ( HyperThread )가 되어 있는 것

