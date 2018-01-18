# ASLR

* ASLR(Address Space Layout Randomization)
  * 메모리 손상 취약점 공격 방지 기술
  * 스택, 힙, 라이브러리 등의 주소를 랜덤한 영역에 배치하여 공격에 필요한 target address를 구하기 어렵게 만든다
  * 프로그램 실행마다 주소가 바뀐다.

## Set ASLR

__command__

```
echo 0 >  /proc/sys/kernel/randomize_va_space
```

__options__

```
0 : ASLR 해제
1 : 랜덤 스택 & 랜덤 라이브러리 설정
2 : 랜덤 스택 & 랜덤 라이브러리 & 랜덤 힙 설정
```

## Memory map

* /proc/<PID>/maps 파일을 통해 프로세스의 메모리 구조 및 주소를 확인해본다.
* 프로그램 실핼 시 마다 스택과 힙, 라이브러리의 주소가 다른 것을 확인할 수 있다.

## .data

* ASLR이 2로 설정되더라도 .data영역의 주소는 변경되지 않는다.
* .data영역은 바이너리 파일 안에 저장되어 있는 영역이므로 바이너리 파일이 올려진 메모리 공간에 위치한다. 하드웨어에 저장되어 있는 바이너리를 메모리에 올린 것이므로 stack, heap, libc와는 다른 특성을 가진다.
* PIE가 설정되어 있으면 매번 새로운 주소에 할당된다.

## How to detect ASLR

```shell
$ sysctl -a
```

kernel.randomize_va_space ="의 값을 확인해 ASLR 설정을 판단한다.