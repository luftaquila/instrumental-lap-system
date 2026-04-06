# 레이싱 트랜스폰더 시스템

차량이 오버헤드 와이어(125kHz AC)를 통과할 때 트랜스폰더 LC 코일에 유기된 신호로 웨이크업 → 2.4GHz ESB 패킷 전송 → 베이스가 TCXO 타이머로 us 정밀도 타임스탬프 기록. 모든 컴포넌트는 nRF52840 기반.

## 운용 모드

| 모드 | 구성 | 용도 |
|------|------|------|
| 랩타임 | 베이스 1개 + USB → PC | 동일 타이머로 t2-t1, 동기화 불필요 |
| 가속 측정 | 베이스 2개 + 메인 컨트롤러 + USB → PC | sync 비콘으로 1회 동기 후 각 베이스 로컬 타임스탬프 차이 계산 |

## 설계 원칙

- 트랜스폰더는 완전 범용 (채널/이벤트 구분 없음). 이벤트 분리는 베이스 + SW 담당
- 도로 시공 없음. 설치/철수 10분
- CR2032 하나로 100시간 이상 (실제 ~66,000시간 마진)
- 베이스 간 유선 연결 없음. 무선 독립 운용
- 타임스탬프는 감지 시점에 즉시 기록 → 릴레이 지연이 정밀도에 영향 없음

## 핵심 파라미터 퀵레퍼런스

| 항목 | 값 |
|------|-----|
| 자기장 | 125kHz AC, 와이어 전류 0.5~2A |
| 트랜스폰더 RF | ESB TX, 2Mbps, -20dBm, CH80, addr 0xE7E7E7E7E7, payload 3B |
| 베이스→메인 RF | ESB TX, +8dBm, CH40, addr 0xA1A1A1A1A1, payload 8B |
| 웨이크업 지연 | ~77us (LPCOMP→HFINT→TX), jitter ±5us |
| 검출 윈도우 | ~3.5ms (10cm@28m/s), ~40회 연속 TX |
| 가속 정밀도 | ±17us (30초 기준), ±0.1ms 이내 보장 |
| TCXO | ±0.5ppm, 16MHz (베이스만) |
| 트랜스폰더 슬립 전류 | ~3uA (LPCOMP 포함) |

## 상세 문서 (.claude/docs/)

| 파일 | 내용 |
|------|------|
| [detection-flow.md](/.claude/docs/detection-flow.md) | 통과 감지 흐름, 동기화 프로토콜, 라디오 스케줄링, 이벤트 분리 |
| [transponder-hw.md](/.claude/docs/transponder-hw.md) | 트랜스폰더 회로, 부품, LC 튜닝, 웨이크업, 동작 시퀀스, 전력, 외장 |
| [base-hw.md](/.claude/docs/base-hw.md) | 베이스 회로, 전원부, 드라이버, TCXO, 와이어, 픽업 코일 |
| [rf-protocol.md](/.claude/docs/rf-protocol.md) | RF 설정, 패킷 구조 (트랜스폰더/릴레이/sync), 채널 배치 |
| [main-controller-hw.md](/.claude/docs/main-controller-hw.md) | 메인 컨트롤러 (가속 모드 전용) |
| [timing-analysis.md](/.claude/docs/timing-analysis.md) | 지연 체인, 랩/가속 정밀도, 검출 신뢰도 |
| [bom.md](/.claude/docs/bom.md) | 프로토타입/양산 BOM |
| [prototype-plan.md](/.claude/docs/prototype-plan.md) | Phase 1~6 + 리스크/폴백 |
