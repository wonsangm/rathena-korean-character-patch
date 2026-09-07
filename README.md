# rAthena Korean CP949 Patch

한국어 Ragnarok Online 클라이언트용 rAthena CP949 통합 패치입니다.
rAthena 전체 소스는 포함하지 않고 패치 파일만 관리합니다.

## 검증 환경

- rAthena: e985006171d2eb320ee512a653f4c83aea3d81b6
- Client: 2026-02-19
- Linux / Ubuntu
- MariaDB / utf8mb4
- git apply --check 성공
- login-server / char-server / map-server 컴파일 성공

## 패치

korean-cp949-full.patch

## 지원 기능

- 한글 캐릭터 생성
- 캐릭터명 CP949 ↔ UTF-8 변환
- map-server 접속 및 캐릭터명 표시
- 일반 채팅 및 채팅방 입장/퇴장 한글 이름 처리
- 한글 몬스터명 표시
- 몬스터 이동 후 이름 깨짐 방지
- 몬스터 이름 재요청 CP949 처리
- @monster 소환
- 긴 한글 몬스터명 (MOB_NAME_LENGTH 64)
- @mobinfo 한글 출력
- NPC 머리 위 한글 이름
- 긴 NPC 이름 안전 처리
- mes / select / menu
- message / dispbottom / npctalk
- announce / mapannounce / areaannounce
- showscript
- 퀘스트 목표 문자열 CP949 처리
- default_codepage: utf8mb4

## 적용

git apply --check korean-cp949-full.patch
git apply korean-cp949-full.patch

실제 한글 NPC 번역 파일과 개인 서버 계정/비밀번호는 포함하지 않습니다.
