# rust-study

Rust를 처음 공부하며 남기는 학습 기록 저장소

## 기반 코스

Google의 [Comprehensive Rust (한국어판)](https://google.github.io/comprehensive-rust/ko/)를 순서대로 따라가며 공부

### 코스 구성

- Rust Fundamentals (4일 분량): 가장 먼저 진행하는 기본 과정
- 심화 과정 (Deep Dives)
  - Concurrency in Rust (1일): 고전적 동시성과 `async`/`await`
  - Idiomatic Rust (2일): Rust 관용구와 패턴, Fundamentals 선수 필요
  - Unsafe (2일, 작성 중): 안전성 보장의 기초와 `unsafe` Rust
  - Bare-Metal Rust (1일): 임베디드 개발, BBC micro:bit v2 보드 필요
  - Rust in Android (반나절): AOSP 체크아웃 필요
  - Rust in Chromium (반나절): Chromium 빌드 필요
- 코스 자료의 저작권: Google LLC, CC-BY-4.0

## 이 저장소에 담기는 것

Claude CLI(Claude Code)와 토론하며 공부한 내용을 아카이브
- 코스를 따라가며 생긴 질문과 그에 대해 claude에게 확인하며 받은 답변 및 이를 정리한 개념 노트
- 실습 코드

## 폴더 구조

```
rust-study/
├── Cargo.toml
├── README.md
├── notes/                      # 토론·질문·개념 정리
│   ├── before/                 # 코스 시작 전 준비(설치, 환경)
│   │   └── 0_setting.md
│   └── fundamentals/
│       ├── 01-hello-world.md
│       └── 02-types-and-values.md
└── src/
    ├── main.rs                 # 수업 중 잠깐 실험하는 연습장
    └── bin/                    # 코스의 연습문제 풀이
        ├── 01_fibonacci.rs
        └── 02_collatz.rs
```

- 노트: 코스의 단원 순서대로 번호를 붙여 `notes/<과정>/`에 보관
- 연습문제: 문제 하나당 파일 하나, `cargo run --bin 01_fibonacci`처럼 파일명으로 골라 실행
- `src/main.rs`: 남길 필요 없는 짧은 실험용, `cargo run`으로 실행
