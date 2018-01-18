# RELRO

* RELRO(Relocation Read-Only)는 ELF 바이너리/프로세스의 데이터 섹션의 보안을 강화하는 기술이다.
* RELRO에는 두 가지 모드가 있다.
  * Partial RELRO
  * Full RELRO

|                                     | No RELRO           | Partial RELRO          | Full RELRO                     |
| ----------------------------------- | ------------------ | ---------------------- | ------------------------------ |
| Compiler command line               | gcc -Wl,-z,norelro | gcc -Wl,-z,relro       | gcc -Wl,-z,relro,-z,now        |
| Lazy binding                        | O                  | O                      | X                              |
| Write to GOT                        | O                  | O                      | X                              |
| PLTRELSZ include in Dynamic Section | O                  | O                      | X                              |
| PLTREL include in Dynamic Section   | O                  | O                      | X                              |
| JMPREL include in Dynamic Section   | O                  | O                      | X                              |
| Now binding                         | X                  | X                      | O                              |
| **RELRO in the Program header**     | X                  | O                      | O                              |
| **Section include in RELRO**        | X                  | INIT_ARRAY, FINI_ARRAY | INIT_ARRAY, FINI_ARRAY, PLTGOT |
| BIND_NOW include in Dynamic Section | X                  | X                      | O                              |

---

__Build command__

```
#No RELRO
$ gcc -Wl,-z,norelro -o RELRO-NoRelro RELRO.c
#Partial RELRO
$ gcc -Wl,-z,relro -o RELRO-Relro RELRO.c
#Full RELRO
$ gcc -Wl,-z,relro,-z,now -o RELRO-FullRelro RELRO.c
```

## Program header & Dynamic Section

RELRO 적용시 `Program header`와 `Dynamic Section`에 변화가 생긴다.

* Partial RELRO
  * `program header`에 RELRO영역이 생긴다.
    * 해당 영역은 Read only
  * __`GOT`를 덮어쓸 수 있다.\
  * Lazy binding을 사용하기 때문에 함수를 호출하지 않으면 동적라이브러리의 주소 값이 .got.plt에 저장되어 있지 않고 stub 코드가 저장되어 있다.
* Full RELRO
  * `program header`에 RELRO영역이 생긴다.
    * 해당 영역은 Read only
    * PLT제거
    * `GOT`덮어쓰기 불가능
    * Full RELRO에서는 Now binding을 사용하기 때문에 프로그램이 메모리에 로드될 때, 해당 프로그램에서 사용되는 모든 동적 함수의 주소를 `.got`영역에 저장한다.

__printf과 호출되는 과정__

|                    | Partial RELRO                           |                    | Full RELRO                   |
| ------------------ | --------------------------------------- | ------------------ | ---------------------------- |
| main()             | call <printf@plt>                       | main()             | call 0x4005c8                |
| .plt(printf@plt)   | jmp QWORD PTR [rip+0x200a6a] # 0x601020 | .plt.got(0x4005c8) | jmp QWORD PTR [rip+0x2009fa] |
| .got.plt(0x601020) | printf                                  | .got(0x600fc8)     | printf                       |

---

## Detect RELRO

- 'readelf' 명령어를 이용해 해당 파일의 프로그래 헤더와 Dynamic section 정보를 가져와 RELRO를 설졍여부를 확인.
- 파일의 프로그래 헤더에 'GNU_RELRO'가 있으면 RELRO가 적용되었다고 판단.
  - 그리고 Dynamic section에 BIND_NOW가 있으면 Full RELRO가 적용되었다고 판단. 
  - Dynamic section에 BIND_NOW가 없으면 Partial RELRO가 적용되었다고 판단.