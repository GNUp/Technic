# NX Bit(MS: DEP)

* NX bit(Never execute bit)
  *  프로세스 명령어나 코드 또는 데이터 저장을 위한 메모리 공간을 분리하여 메모리마다의 특성을 다르게 하는 기술
  * 데이터 저장을 위한 메모리 공간은 명령어 실행이 되지 않도록 한다.
* DEP(Data Execution Prevention)
  * 마이크로소프트 윈도우 운영체제에 포함된 보안기능으로 악의적인 코드 실행을 방지하기 위해서 메모리에 대한 추가적인 검사를 실행하는 기술
  * DEP는 두가지 모드로 실행된다
    * 하드웨어 DEP: 메모리에 명시적으로 실행 가능하다고 표시되어 있는 경우를 제외하고 메모리에서의 코드 실행을 금지한다.
      * 대부분의 최신 프로세서는 하드웨어 DEP를 지원한다.
    * 소프트웨어 DEP: 하드웨어 DEP를 사용하지 않을 경우 지원
* Example: Heap, Stack영역에 shellcode를 저장하고 실행하기 위해서는 해당영역에 실행권한이 있어야 하는데 NX, DEP가 걸려 있을 경우 프로그램이 예외처리 후 종료된다.

---

```
Build Command(DEP disable)
$gcc -z execstack
```

---

Checking permissions in memory

```
$cat /proc/pid/maps
```

을 통해 확인가능

---

## How to detect NX

```shell
# check for NX support
if readelf -W -l $1 2>/dev/null | grep 'GNU_STACK' | grep -q 'RWE'; then
  echo -n -e '\033[31mNX disabled\033[m   '
else
  echo -n -e '\033[32mNX enabled \033[m   '
fi
```

* readelf를 통해 파일의 세그먼트 헤더 정보에서 확인가능
* GNU_STACK의 flag 값이 RW면 NX가 적용된 것

### process

```shell
# fallback check for NX support
elif readelf -W -l $1/exe 2>/dev/null | grep 'GNU_STACK' | grep -q 'RWE'; then
  echo -n -e '\033[31mNX disabled\033[m   '
else
  echo -n -e '\033[32mNX enabled \033[m   '
fi
```

$1 = /proc/<PID>

## CPU

```
# check cpu nx flag
nxcheck() {
  if grep -q nx /proc/cpuinfo; then
    echo -n -e '\033[32mYes\033[m\n\n'
  else
    echo -n -e '\033[31mNo\033[m\n\n'
  fi
}
```

* /proc/cpuinfo에서 nx 문자가 있는 지 확인

