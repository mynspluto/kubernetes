## make 명령어 실행

\_output 폴더에 결과물 생성

- make
  인자 없이
- make PRINT_HELP=y
  인자 포함, PRINT_HELP와 같이 인자로 받을 값은 ?=로 선언
  =로 선언하면 사용자의 입력이 덮어쓰기되어 ?=로 선언

## BASH_ENV

BASH_ENV는 Bash 쉘이 공식적으로 인정하는 특별한 환경변수

```shell
BASH_ENV := ./hack/lib/logging.sh
```
모든 Bash 명령어들이 시작될 떄마다 logging.sh를 자동으로 로드

## Makefile 파일 내용
