# Canaries

* Canaries 또는 Canary word는 bof를 방지하기 위해서 buffer와 제어 데이터 사이에 값을 넣은 것이다.
* bof가 발생하면 canary 값이 손상되고, canary 검증과정에서 실패하면 종료한다.

## Types of canaries

### Terminator canaries

* Canary의 값을 문자열의 끝을 나타내는 문자들을 이용해서 생성
* NULL(0x00), CR(0x0d), LF(0x0a), EOF(0xff)로 구성된다.
  * 공격자는 우회하기위해 ret전에 null문자를 써야한다.
  * null문자로 인해서 overflow가 방지된다.

### Random canaries

* 랜덤하게 값을 생성
* 프로그램 초기 설정 시에 전역 변수에 canary 값이 저장된다.
  * 이 값은 보통 매핑되지 않은 페이지에 저장되므로 이 페이지를 읽으려고 시도할 경우 segmentation fault가 발생하고 프로그램이 종료된다.
  * 그러므로 Canary값을 읽으려는 시도는 방지된다.



### Random Xor canaries

* Canary 값을 모든 제어 데이터 또는 일부를 사용해 XOR-scramble 하여 생성한다.
  * 이 방식은 canary의 값, 제어 데이터의 값이 오염되면 canary의 값이 변한다.
* Random canaries와 동일한 취약점을 가진다.
  * 단지 canary의 값을 stack에서 읽어오는 방법이 조금 더 복잡해진다.
  * 공격자는 canary를 다시 인코딩해서 얻어오기위해 다른 제어 데이터들이 필요해진다.

---

__Build command__

```shell
$ gcc -fstack-protector-al
```

## Detect Canary

* 파일의 심볼 테이블에 __stack_chk_fail이 있으면 canary가 적용된 것이다.

```shell
if readelf -s $1 2>/dev/null | grep -q '__stack_chk_fail'; then
  echo -n -e '\033[32mCanary found   \033[m   '
else
  echo -n -e '\033[31mNo canary found\033[m   '
fi
```



