## Hello World
```rust
fn main() {
    println!("Hello 🌍!");
}
```
- 함수 선언 = fn
    - main 함수는 프르그램 진입점
    - Q. 이 말은 뭔가 python에서 init main으로 묶는거처럼 해당하는 부분을 기준으로 돌아간다는 약속 같은거라고 생각하면 되나? 
        - `<CLAUDE check>` "여기서부터 시작한다는 약속"이라는 점은 맞는 이해
        - `<CLAUDE check>` 다른 점 1: Python의 `if __name__ == "__main__":`은 선택 사항인 관습, Rust의 `main`은 언어가 강제하는 규칙 (실행 파일을 만드는데 `main`이 없으면 컴파일 에러)
        - `<CLAUDE check>` 다른 점 2: Python은 파일을 맨 윗줄부터 차례로 실행하고 `if __name__ ...`도 그 도중에 만나는 평범한 if문, Rust는 함수 바깥에 실행되는 코드를 둘 수 없고 실행은 항상 `main`의 첫 줄부터
        - `<CLAUDE check>` 실행 순서: 컴파일러가 바이너리에 "시작 위치"를 기록 → OS가 바이너리를 메모리에 올림 → 준비 코드가 잠깐 실행된 뒤 `main` 호출 → `main`이 끝나면 프로그램 종료
- C/C++과 마찬가지로 중괄호로 블록을 표시
- 똑똑한 매크로(hygienic macros) 시스템을 가지고 있음. 
    - ex) println!
    - Rust uses macros for situations where you want to have a variable number of arguments(no function overloading)
    - Q. macro가 뭘 의미하는거지? 함수랑은 다른건가? overloading은? variable number of arguments라는 것도 이해가 어려운게 python은 kawrgs? 같은걸 지원하지 않는다는 거고 overloading은 메모리 세팅 관련의 내용인건가? 
        - `<CLAUDE check>` 매크로 = "코드를 만들어내는 코드", 함수와 다른 것: 함수는 실행 중에 호출되고, 매크로는 컴파일 중에 실제 코드로 바뀌어(전개, expansion) 그 자리에 끼워 넣어짐
        - `<CLAUDE check>` 이름 끝의 `!`가 매크로 호출 표시: `println!`은 매크로, `!`가 없으면 함수
        - `<CLAUDE check>` variable number of arguments(가변 인자) = 인자 개수가 정해져 있지 않은 것, Python의 `print(a)`, `print(a, b, c)`가 모두 되는 것(`*args`, `**kwargs`)과 같은 개념
        - `<CLAUDE check>` Rust 쪽이 지원하지 않는 것: Rust 함수는 인자의 개수와 타입이 선언할 때 고정, `*args`·`**kwargs`·기본값 인자 없음
        - `<CLAUDE check>` overloading은 메모리와 무관(틀린 추측): 같은 이름의 함수를 인자 개수·타입만 다르게 여러 개 정의하는 기능(C++/Java에 있음), Rust에는 없음
        - `<CLAUDE check>` 결론: 함수로는 `println!("a")`, `println!("{} {}", a, b)`처럼 인자 개수가 달라지는 호출을 만들 수 없어서 매크로를 사용
        - `<CLAUDE check>` `println!` 처리 순서: 컴파일러가 형식 문자열의 `{}` 개수와 인자 개수를 대조(안 맞으면 컴파일 에러) → 출력하는 실제 코드로 전개 → 전개된 코드를 일반 코드처럼 타입 검사·번역
    - Q. hygienic macros(=똑똑한 매크로)의 의미는 매크로가 사용되는 스코프에서 의도치 않게 변수를 가로채지 않는다는거 같은데 이게 무슨 말인지 모르겠는데 하나의 변수를 여러 함수, 등에서 동시에 못쓴다는 건가? 그리고 "사실 러스트 매크로는 완전히 hygenic하지는 않습니다. [링크](https://lukaswirth.dev/tlborm/decl-macros/minutiae/hygiene.html)를 참고"라는 말은 또 어떤 의미인거지? 
        - `<CLAUDE check>` "하나의 변수를 여러 함수에서 동시에 못 쓴다"는 뜻이 아님(틀린 이해), 이름 충돌에 관한 이야기
        - `<CLAUDE check>` 문제 상황: 매크로는 코드를 내 코드 자리에 끼워 넣는 방식 → 매크로 안에서 임시로 만든 변수 이름이 내 변수 이름과 우연히 같으면, 단순 붙여넣기 방식(C의 매크로)에서는 내 변수를 덮어쓰거나 엉뚱한 변수를 읽게 됨 = "변수를 가로챈다"
        - `<CLAUDE check>` hygienic(위생적) = 매크로 안에서 만든 이름과 매크로를 부른 쪽의 이름을 철자가 같아도 서로 다른 것으로 취급 → 서로 오염시키지 않음
        - `<CLAUDE check>` 확인용 예: 아래에서 매크로 안의 `x`와 바깥의 `x`는 별개, 출력은 `1`
            ```rust
            macro_rules! make_x {
                () => { let x = 99; };
            }
            fn main() {
                let x = 1;
                make_x!();
                println!("{x}"); // 1
            }
            ```
        - `<CLAUDE check>` "완전히 hygienic하지는 않다"의 의미: 이 보호가 지역 변수 같은 일부 이름에만 적용되고, 매크로가 만들어내는 함수·타입 같은 것에는 적용되지 않아 바깥에서 보이고 충돌도 가능
        - `<CLAUDE check>` 링크 내용은 매크로를 직접 만들 때 필요한 세부 사항, 지금 단계에서는 "매크로를 써도 내 변수가 몰래 바뀌지 않는다" 정도로 충분
- 문자열을 UTF-8로 인코딩되며 이모지와 같은 유니코드 문자를 포함할 수 있음
    - 유니코드 지원과 같은 현대 언어의 특정을 전부 지원
- 러스트는 절차적 언어로 정말로 필요한 경우가 아니라면, 이미 존재하는 것을 새로 구현하려고 하지 않음
    - 러스트는 멀티 패러다임 언어로 예를 들어, 강력한 객체 지향 프로그래밍 기능을 지원하기도 하며, 함수형 언어로 분류되지는 않지만 폭넓은 범위의 함수형 컨셉을 지원하기도 함
    - `<CLAUDE check>` 위 문장은 원문에서 별개인 두 가지가 합쳐진 것: (1) Rust는 절차적 언어 (2) 언어를 설계할 때 꼭 필요하지 않으면 기존 언어(C/C++/Java 계열)의 익숙한 방식을 그대로 따름, "절차적 언어라서 재구현하지 않는다"는 인과 관계가 아님
    - Q. 절차적 언어라는 말과 함수형 프로그래밍 언어라는 말이랑은 다른건가? 
        - `<CLAUDE check>` 다른 것이 맞음, 프로그램을 짜는 방식(패러다임)의 서로 다른 종류
        - `<CLAUDE check>` 절차적(원문 imperative, 명령형) = "이걸 하고, 다음에 저걸 해라" 식으로 명령을 순서대로 나열하며 변수의 값을 바꿔 나가는 방식
        - `<CLAUDE check>` 함수형 = 값을 바꾸는 대신, 입력을 받아 결과를 돌려주는 함수들을 이어 붙여 계산하는 방식
        - `<CLAUDE check>` Python 비교: `total = 0` 후 `for`문에서 `total += x*x`가 절차적, `sum(map(lambda x: x*x, xs))`가 함수형 스타일, Python도 Rust처럼 절차적 바탕의 멀티 패러다임 언어
        - `<CLAUDE check>` Rust의 위치: 바탕은 절차적, 여기에 함수형에서 온 기능(변수는 기본적으로 값 변경 불가, 클로저, 반복자의 `map`/`filter` 등)을 많이 가져온 언어 → 뒤 단원에서 차례로 등장

## 토론: overloading, 매크로 전개, 컴파일과 실행의 순서
- Q. overloading은 덮어씀 정도로 이해를 하면 되나?
    - `<CLAUDE check>` 틀린 이해: 덮어씀이 아니라 공존
        - 덮어씀: 새것이 옛것을 없애고 하나만 남는 것
        - overloading: 같은 이름의 함수 여러 개가 동시에 존재, 호출할 때 넘긴 인자의 개수·타입을 보고 그중 하나가 선택됨
    - `<CLAUDE check>` 같은 이름의 함수를 두 번 정의했을 때 언어별 동작
        - Python: 뒤의 정의가 앞의 정의를 덮어씀 (overloading이 아닌 쪽의 예)
        - C++/Java: 인자 개수·타입이 다르면 둘 다 남음 = overloading
        - Rust: 덮어쓰지도 공존시키지도 않고 컴파일 에러
- Q. macro는 결국 함수랑은 다른거고? 코드를 만들어내는 코드라는게 무슨 말인지 모르겠어
    - `<CLAUDE check>` 함수와 다른 것이 맞음
    - `<CLAUDE check>` 매크로 = 컴파일러에게 주는 "바꿔 쓰기 규칙" ("이런 모양으로 쓰면 저런 코드로 바꿔 써라"), 휴대폰 상용구에서 "ㅈㅅ"이 "죄송합니다"로 바뀌는 것과 비슷
    - `<CLAUDE check>` `println!("x: {x}");`가 처리되는 순서
        - 1. 컴파일러가 번역 초반에 `!`가 붙은 곳을 찾음
        - 2. 그 한 줄을 지우고 규칙에 따라 더 긴 Rust 코드를 그 자리에 써넣음 (x를 글자로 바꾸고, 앞에 `x: `를 붙이고, 화면 출력 통로에 쓰고, 줄바꿈을 붙이는 코드)
        - 3. 바뀐 코드를 기계어로 번역
        - 2번에서 Rust 코드가 새로 만들어지므로 "코드를 만들어내는 코드", 바이너리에는 `println!`이 남지 않고 바꿔 써진 결과만 들어감
    - `<CLAUDE check>` 함수와 매크로의 차이
        - 일하는 시점: 함수는 프로그램 실행 중, 매크로는 컴파일 중
        - 하는 일: 함수는 값을 받아 계산하고 결과를 돌려줌, 매크로는 내가 쓴 코드를 다른 코드로 바꿔 씀
        - 인자 개수: 함수는 고정, 매크로는 자유 (개수에 맞는 코드를 그때그때 써넣으면 되기 때문)
- Q. 컴파일이 binary 파일을 만드는 개념이었나?
    - `<CLAUDE check>` 맞는 이해: 소스 코드를 실행 전에 기계어로 번역해 바이너리 파일을 만드는 과정
- Q. 실행의 개념이랑 컴파일이 계속 헷갈리는데 결론적으로 실행은 컴파일에 넣기전? rustc가 검사하는 단계라면 실행시에 발견되는건가?
    - `<CLAUDE check>` 둘 다 반대: 실행은 컴파일 뒤, rustc가 검사하는 단계는 컴파일
    - `<CLAUDE check>` 순서
        - 1. 작성: `main.rs`에 소스 코드를 씀 (글자 파일)
        - 2. 컴파일: `rustc`가 소스를 검사(문법, 타입, 매크로 인자 개수 등) → 문제가 있으면 에러를 내고 멈춤(바이너리 없음) → 통과하면 기계어로 번역해 바이너리 생성
        - 3. 실행: OS가 바이너리를 메모리에 올리고 CPU가 명령을 하나씩 수행, 화면에 글자가 찍히는 것은 이때
        - `rustc`는 2번에서만 일함, 3번에서는 `rustc`가 없어도 됨
    - `<CLAUDE check>` 헷갈리기 쉬운 이유
        - `cargo run`이 2번과 3번을 한 번에 수행: 출력의 `Compiling`~`Finished`가 컴파일, `Running` 아래부터가 실행
        - 에러가 `Running` 줄보다 위에 나오면 컴파일 에러, 그 경우 `Running` 줄은 나오지 않음
        - Python은 `python a.py` 하나로 읽기와 수행이 섞여 진행되어 구분이 겉으로 보이지 않음
    - `<CLAUDE check>` 비유: 컴파일은 번역가가 책 한 권을 통째로 번역해 번역본을 만드는 일, 실행은 독자가 번역본을 읽는 일, Python은 통역사가 한 줄씩 읽으며 바로 전달하는 방식
- Q. (확인 문제) 첫 줄은 정상 `println!`, 둘째 줄은 타입이 안 맞는 코드일 때 `cargo run`을 하면 첫 줄이 찍히나? → 안 찍히겠지? compiler할때 확인될테니?
    - `<CLAUDE check>` 맞는 이해: 컴파일의 검사에서 둘째 줄이 걸림 → 바이너리가 만들어지지 않음 → 실행이 시작되지 않아 첫 줄도 수행되지 않음
    - `<CLAUDE check>` Python과의 차이: Python은 문제 있는 줄에 도달하기 전까지는 실행됨, Rust는 파일 전체가 검사를 통과해야 첫 줄이라도 실행
    - `<CLAUDE check>` 모든 오류가 컴파일 때 잡히는 것은 아님: 실행해 봐야 알 수 있는 값에 달린 오류(예: 입력받은 수가 0인데 그 수로 나누기)는 실행 중에 발생

## Variables
```rust
fn main() {
    let x: i32 = 10;
    println!("x: {x}");
    // x = 20;
    // println!("x: {x}");
}
```

주석을 풀었을 시
```
   Compiling playground v0.0.1 (/playground)
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x: i32 = 10;
  |         - first assignment to `x`
3 |     println!("x: {x}");
4 |     x = 20;
  |     ^^^^^^ cannot assign twice to immutable variable
  |
help: consider making this binding mutable
  |
2 |     let mut x: i32 = 10;
  |         +++

For more information about this error, try `rustc --explain E0384`.
error: could not compile `playground` (bin "playground") due to 1 previous error
```
- 즉, x라는 int 32는 immutable이 기본이고 mutable == `mut`을 주어야 변경이 허용
    - `<CLAUDE check>` 맞는 이해, 다만 immutable은 `i32`라는 타입의 성질이 아니라 변수의 성질: 타입이 무엇이든 `let`으로 만든 변수는 전부 변경 불가가 기본
    - `<CLAUDE check>` 이 에러는 컴파일 에러: 출력이 `Compiling` 다음에 바로 `error`이고 `Running` 줄이 없음 → 바이너리가 만들어지지 않아 `x: 10`도 찍히지 않음
    - `<CLAUDE check>` Python과의 차이: Python 변수는 언제든 다시 대입 가능, Rust는 "바꿀 변수"를 `let mut`으로 미리 표시해야 하고 표시가 없는 변수는 끝까지 같은 값이라고 믿고 읽을 수 있음
    - `<CLAUDE check>` `mut`이 허용하는 것은 값의 변경뿐: `let mut x: i32 = 10;` 뒤에 `x = 20;`은 가능, `x = "a";`처럼 타입이 다른 값을 넣는 것은 불가
- 타입 추론(나중에 설명)을 사용하면 프로그래머가 이를 생략할 수 있는 경우가 많이 존재
    - Q. 타입 추론이라는게 무슨 말이지? python에서는 애초에 선언을 안해주는데 처음 이 변수가 사용될때를 기준으로 한다는 말인가? 
        - `<CLAUDE check>` 절반 맞는 이해: "코드에 타입을 안 적어도 된다"는 겉모습은 Python과 같지만 동작은 다름
        - `<CLAUDE check>` 타입 추론 = `: i32` 같은 타입 표기를 생략하면 컴파일러가 코드를 보고 타입을 알아내 대신 채워 넣는 것
        - `<CLAUDE check>` `let x = 10;`의 추론 순서 (전부 컴파일 중에 일어남)
            - 1. 타입 표기가 없음을 확인
            - 2. 넣는 값 `10`을 보고 정수 종류라고 판단
            - 3. 뒤 코드에서 `x`가 쓰이는 곳을 살펴 어떤 정수 타입인지 단서를 찾음
            - 4. 단서가 없으면 정수의 기본 타입인 `i32`로 결정
            - 5. 결정된 타입은 고정, 결과물은 `let x: i32 = 10;`이라고 직접 쓴 것과 완전히 같음
        - `<CLAUDE check>` "처음 사용될 때 기준"에서 고칠 점: 실행 중에 처음 쓰이는 순간에 정해지는 것이 아니라 실행 전에 컴파일러가 코드를 읽어서 결정, 기준은 주로 처음 넣는 값이고 이후 쓰임새도 참고
        - `<CLAUDE check>` Python과의 차이
            - Python: 변수 자체에는 타입이 없고 값에만 타입이 있음, `x = 10` 뒤에 `x = "a"` 가능, 타입 오류는 실행 중에 발견
            - Rust: 변수마다 타입이 하나로 고정(적지 않았을 뿐 정해져 있음), 다른 타입의 값을 넣으면 컴파일 에러