# PIE

* PIE(Position Independent Executable)는 위치 독립 코드로 이루어진 실행 가능한 바이너리로 실핼될 때마다 바이너리가 올려지는 메모리의 위치가 랜덤하게 달라진다.

---

## Address

* PIE가 적용되면 프로그램을 실행할 때마다 전역 변수와 사용자 정의 함수의 주소가 변경된다.

## Code

* PIE가 적용되지 않은 바이너리일 경우 코드 영역의 값이 고정된 주소 값이다.
* PIE가 적용된 바이너리일지라도 코드 영역의 offset 값들은 동일하다
  * 코드 영역의 값이 고정된 주소가 아닌 offset 값이다. 메모리에 영역에 동적으로 위치 시키는 것이 가능해진다.
  * -> 하나의 주소만 알면 나머지의 주소도 offset으로 알 수 있다.

## Detect PIE

- 'readelf' 명령어를 이용해 해당 파일의 ELF Header 정보를 가져와 PIE 설졍여부를 확인.
- "Type:"의 값이 "EXEC"일 경우 PIE가 적용되지 않았다고 판단.
- "Type:"의 값이 "DYN"일 경우 PIE가 적용되었을 가능성이 있다고 판단.
  - 'readelf' 명령어를 이용해 해당 파일의 "Dynamic section" 정보를 가져와 "DEBUG" section이 있으면 PIE가 적용되었다고 판단.

```shell
# check for PIE support
if readelf -h $1 2>/dev/null | grep -q 'Type:[[:space:]]*EXEC'; then
  echo -n -e '\033[31mNo PIE       \033[m   '
elif readelf -h $1 2>/dev/null | grep -q 'Type:[[:space:]]*DYN'; then
  if readelf -d $1 2>/dev/null | grep -q '(DEBUG)'; then
    echo -n -e '\033[32mPIE enabled  \033[m   '
  else  
    echo -n -e '\033[33mDSO          \033[m   '
  fi
else
  echo -n -e '\033[33mNot an ELF file\033[m   '
fi
```

