## Installation
- rustc 1.100.0, cargo 1.100.0이 stable 툴체인으로 확보
- 설치 link: [The Cargo Book](https://doc.rust-lang.org/cargo/getting-started/installation.html)
    ```zsh
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
    ```
- `rustc`: .rs 확장자 파일을 바이너리 혹은 다른 중간 형식으로 변환해주는 Rust 컴파일러
- `cargo`: 러스트 의존성 관리자이자 빌드 시스템 
    - 프로젝트에 명시된 의존성들을 https://crates.io에서 자동으로 다운로드 받고, 그 소스코드를 rustc로 전달하여 빌드
    - 유닛 테스트를 실행하는 테스트 러너를 내장
- 이 폴더는 cargo로 만든 프로젝트(Cargo.toml + src/main.rs의 Hello world)

## Check and Create folder
- 툴 버전 확인
    ```
    > rustc --version
    rustc 1.100.0 (0ef1bc386 2026-11-09)
    > cargo --version
    cargo 1.100.0 (3acc79c0c 2026-11-05)
    ```
- 이 프로젝트는 `cargo new rust-study`로 만들어짐
- 그냥 run 실행시:
    ```
    > cargo run
    Compiling rust-study v0.1.0 (/home/begaram/projects/rust-study)
        Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.06s
        Running `target/debug/rust-study`
    Hello, world!
    ```

## Meaning of `bin`
- `bin`: binary = 실행할 수 있는 프로그램 파일
    - 소스 코드: 사람이 읽고 쓰는 텍스트. `.py`, `.rs`와 같은 파일 
    - 기계어: CPU가 직접이해하는 0과 1의 나열
    - 바이너리: 기계어로 번역된 결과가 담긴 파일 
    - 컴파일: 소스 코드를 바이너리로 번역하는 작업
- in ubuntu: 실행프로그램을 모아둔 곳
    - /usr/bin: 시스템에 설치된 프로그램(ls, python3, git, 등이 포함)
        - ls라는 cli 명령에 대해서 실행하면 정해진 bin 폴더 목록(=PATH)를 뒤져서 나온 ls라는 이름의 프로그램을 실행하는 형태
    - ~/.cargo/bin: Rust 설치 때 생긴 폴더로, rustc와 cargo가 여기에 위치
- vs python
    - `python *.py`: python이라는 프로그램이 소스를 읽으면서 그때그때 실행(나의 환경에서 실행하려면 python이라는 프로그램이 필요)
    - rust: 컴파일을 미리하여 바이너리를 만들어 두고 바이너리를 실행 = rust가 설치되지 않은 컴퓨터에서도 돌아가게 됨
        - 단, 같은 종류의 OS·CPU여야 함
            - 기계어는 CPU 종류마다 다름: x86_64에서 만든 바이너리는 ARM CPU(애플 실리콘 맥, 라즈베리 파이 등)에서 실행 불가
            - 바이너리가 OS에 일(파일 열기, 화면 출력 등)을 부탁하는 방식이 OS마다 다름: Linux용 바이너리는 Windows에서 실행 불가
        - 내 경험상은 docker img를 미리 만들어두고 이를 daemon에서 돌리는 느낌? 물론 바이너리 수준도 아니고 서버를 돌릴 수 있는 상태로 만들어내는 여러 규칙의 집합 같은 느낌이지만 버전 관리나 제약에서의 자유로움을 생각했을 때?
            - `<CLAUDE check>` "미리 만들어 두고 여러 번 실행"한다는 점에서는 맞는 비유
            - 차이: Docker 이미지는 실행 환경을 통째로 포함(Python 프로그램이면 `python` 자체도 이미지 안에 포함), Rust 바이너리는 번역이 이미 끝나서 번역기가 필요 없어진 것
- in rust: `src/bin`
    - 바이너리 자체가 아닌 바이너리가 될 소스코드가 들어가는 형태
    - 해당 경로의 파일들은 각각 실행프로그램 하나씩으로 만들자라는 Cargo와의 약속
    - ex) `cargo run --bin 01_fibonacci`
        1. Cargo가 src/bin/01_fibonacci.rs를 탐색
        2. 컴파일러(rustc)가 이 소스를 기계어로 번역 = 컴파일
            - Cargo는 컴파일할 때마다 target/debug/.fingerprint/ 폴더에 "지문" 기록을 남김
                - 어떤 소스 파일들로 만들었는지
                - 그 파일들의 마지막 수정 시각
                - 컴파일러 버전과 컴파일 옵션
                - 의존하는 라이브러리 목록과 버전
        3. 결과 바이너리를 target/debug/01_fibonacci에 저장
        4. 해당 바이너리를 실행
        5. 다음에 실행할때에는 
            1. 소스 파일들의 현재 수정 시각을 지문과 비교
            2. 컴파일러 버전, 옵션, 의존성이 바뀌었는지도 비교
            3. 전부 같으면 컴파일을 건너뛰고 기존 바이너리를 실행
            4. 하나라도 다르면 다시 컴파일 + 지문 업데이트 
            - Compiling ... 줄이 사라지는 걸 통해서 인지가 가능

## 명령어
- `cargo check`: 빠르게 에러를 확인
- `cargo build`: 실행없이 컴파일
    - 이 경우에 `target/debug/`폴더에서 output을 확인 가능 
- `cargo build --release`: 릴리즈 버전용 최적화를 켜서 컴파일하며 `target/release/`폴더에서 확인