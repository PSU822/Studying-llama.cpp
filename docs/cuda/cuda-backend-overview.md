# CUDA 백엔드 전체 구조 간략 정리

## 핵심 아키텍처

- cpu <-> gpu 간의 흐름
- 백엔드 초기화 과정(사용할 구조체, 오프셋 설정)
- 디바이스 관리

## Cpp vs C

- 메모리 할당 전략(레거시 vs VMM)
- 실시간 성능 우선 설계

## 상세

1. CUDA 역할: CPU ↔ GPU 논리 버스 + 추상화 레이어

   - CUDA Runtime API → CUDA Driver API → GPU Driver → GPU Hardware

2. 메모리 관리 전략:

   - Legacy Pool: O(n) 선형 검색, 단순하지만 비효율적
   - VMM Pool: O(1) 할당, 빠르지만 순차 해제만 가능
   - Unified Memory vs 순수 GPU 메모리 선택 가능

3. 계층별 설계 철학:

   - High Level (C++): 복잡한 로직, STL, 스마트 포인터
   - Mid Level (C++): 메모리 관리, 스케줄링 최적화
   - Low Level (CUDA C): 단순하고 빠른 커널, 원시 포인터

4. 백엔드 초기화 과정:

   - 디바이스 유효성 검사 → Context 생성 → Backend 구조체 설정

5. CUDA 제약사항:

   - STL 사용 불가, 동적 할당 제한적
   - 예외 처리 없음, 단순한 C 기반 구조
   - → 우선순위 큐 같은 복잡한 자료구조 직접 구현 필요

6. 성능 vs 안정성 트레이드오프:
   - 실시간 성능 우선 (1초에 수천~수만 번 메모리 할당)
   - O(1) 할당이 O(log n)보다 GPU에서 유리
   - 단순할수록 디버깅과 유지보수 쉬움
