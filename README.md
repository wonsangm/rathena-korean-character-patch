# rAthena Korean Custom

rAthena 기반 Ragnarok Online 개인 서버 커스텀 저장소입니다.

## 목적

한국어 클라이언트 환경에서 **한글 캐릭터명**을 사용할 수 있도록 수정한 내용을 보관하고, 향후 rAthena 업데이트가 이루어졌을 때 한글 캐릭터명 관련 커스텀 수정사항을 다시 적용하기 위한 저장소입니다.

## 현재 기준

* rAthena 기반
* rAthena commit: `e985006171d2eb320ee512a653f4c83aea3d81b6`
* 검증 환경: Linux / Ubuntu (Windows / Visual Studio는 미검증)
* Ragnarok Online 클라이언트: **2026-02-19**
* 한글 캐릭터명 지원
* 검증 완료: 한글 캐릭터 생성 / map-server 접속 / 캐릭터명 CP949 출력
* 검증 완료: 채팅 / 채팅방 입장·퇴장
* 검증 완료: 한글 몬스터명 / 이동 후 이름 유지 / @mobinfo
* 검증 완료: 23바이트 초과 한글 몬스터명 / @monster 소환

* CP949 ↔ UTF-8 변환
* MariaDB / utf8mb4

## 한글 캐릭터명 수정사항

### `src/char/char_clif.cpp`

클라이언트에서 전달되는 캐릭터명을 서버에서 사용할 수 있도록 **CP949 → UTF-8** 변환 처리.

### `src/char/char.cpp`

서버의 UTF-8 캐릭터명을 클라이언트가 사용할 수 있도록 **UTF-8 → CP949** 변환 처리.

### `conf/char_athena.conf`

```text
char_name_option: 0
```

한글 캐릭터명 사용을 위해 캐릭터명 제한을 해제.

### `conf/inter_athena.conf`

```text
default_codepage: utf8mb4
```

UTF-8 문자 저장을 위해 설정.

## 패치 적용 순서

1. `korean-character-name.patch`
2. `korean-monster-cp949.patch`
3. `korean-mobinfo-cp949.patch`
4. `korean-long-monster-name.patch`

각 패치는 적용 전에 `git apply --check 패치파일명`으로 확인합니다.
`korean-long-monster-name.patch`는 `korean-monster-cp949.patch` 적용 후 사용하는 것을 권장합니다.

## rAthena 업데이트 시

rAthena가 업데이트되면 한글 캐릭터명 관련 수정사항이 변경되거나 덮어써질 수 있습니다.

업데이트 후 다음 파일을 다시 확인해야 합니다.

```text
src/char/char.cpp
src/char/char_clif.cpp
conf/char_athena.conf
conf/inter_athena.conf
```

### 업데이트 후 확인 순서

1. rAthena 업데이트
2. 한글 캐릭터명 관련 수정사항 확인
3. CP949 ↔ UTF-8 변환 코드 확인
4. `char_name_option: 0` 확인
5. `default_codepage: utf8mb4` 확인
6. 서버 컴파일
7. 한글 캐릭터 생성 테스트
8. 게임에서 한글 캐릭터명 정상 표시 확인

## 중요 커밋

한글 캐릭터명 CP949 지원:

`f4344dc6d`

README 추가:

`be6da2aee`

## 향후 작업

* rAthena 업데이트 시 한글 캐릭터명 커스텀 수정사항 재적용 방법 정리
* 한글 캐릭터명 관련 호환성 및 안정성 확인


## 주의

이 저장소의 목적은 **rAthena 원본과 별도로 개인 서버의 커스텀 수정사항을 관리하는 것**입니다.

rAthena 업데이트 후에는 반드시 한글 캐릭터명 기능을 테스트합니다.

## 한글 몬스터명 지원

### `korean-monster-cp949.patch`

한국어 클라이언트에서 한글 몬스터명이 정상적으로 표시되도록 수정합니다.

주요 변경사항:

- `override_mob_names: 1` 설정
- `mob_db`의 `JName`을 몬스터 이름으로 사용
- 서버의 UTF-8 몬스터명을 클라이언트용 CP949로 변환하여 출력

변경 대상:

```text
conf/battle/monster.conf
src/map/clif.cpp
```

적용:

```bash
git apply korean-monster-cp949.patch
```

## 패치 저장소

이 저장소에는 rAthena 전체 소스를 포함하지 않습니다.

한국어 클라이언트 지원을 위한 커스텀 패치 파일만 관리합니다.

```text
korean-character-name.patch
korean-monster-cp949.patch
korean-mobinfo-cp949.patch
korean-long-monster-name.patch
```

개인 서버의 DB 접속 정보와 비밀번호 등의 보안 정보는 저장소에 포함하지 않습니다.


## @mobinfo 한글 몬스터명 지원

### `korean-mobinfo-cp949.patch`

한국어 클라이언트에서 `@mobinfo` 명령어 사용 시 한글 몬스터명이 깨져서 표시되는 문제를 수정합니다.

주요 변경사항:

- `src/map/atcommand.cpp`의 `@mobinfo` 출력 처리
- 몬스터 `Name` / `JapaneseName`을 UTF-8 → CP949로 변환하여 출력
- 긴 UTF-8 몬스터명을 불필요하게 `NAME_LENGTH`로 제한하지 않고 변환

변경 대상:

`src/map/atcommand.cpp`

적용:

`git apply korean-mobinfo-cp949.patch`


## 긴 한글 몬스터명 지원

### `korean-long-monster-name.patch`

UTF-8에서 23바이트를 초과하는 한글 몬스터명이 `NAME_LENGTH` 제한으로 잘리거나 깨지는 문제를 수정합니다.

주요 변경사항:

- 기존 `NAME_LENGTH`는 변경하지 않음
- 몬스터 내부 이름용 `MOB_NAME_LENGTH`를 64바이트로 분리
- `Name` / `JapaneseName`을 최대 64바이트 내부 버퍼에 보관
- 클라이언트 출력 직전에 UTF-8 전체 몬스터명을 CP949로 변환
- 캐릭터명 및 기존 클라이언트 패킷의 `NAME_LENGTH`에는 영향 없음

변경 대상:

`src/common/mmo.hpp`, `src/map/map.hpp`, `src/map/mob.hpp`, `src/map/mob.cpp`, `src/map/script.cpp`, `src/map/clif.cpp`

적용:

`git apply korean-long-monster-name.patch`
