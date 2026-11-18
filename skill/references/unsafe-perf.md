# unsafe·FFI·성능

## unsafe
- 쓸 정당한 이유는 새로운 안전 추상화, 벤치마크로 확인한 성능, FFI뿐이다. 코드를 줄이려고, `Send`나 수명을 우회하려고 쓰지 않는다. "위험해 보인다"는 이유로 함수에 `unsafe`를 붙이지 않는다.
- unsafe는 모듈 하나에 가두고 privacy로 경계를 만든다. 그 모듈의 safe 함수가 불변식을 지키게 한다.
- 블록마다 바로 위에 `// SAFETY:` 주석으로 지켜지는 조건을 적는다. 해당 crate에서 `unsafe_code = "forbid"`를 풀고 lint `undocumented_unsafe_blocks`를 켠다 (워크스페이스 템플릿에 들어 있음).
- edition 2024: `unsafe fn` 안에서도 연산마다 `unsafe {}` 블록, `static mut` 참조 금지, `env::set_var`는 unsafe, `#[unsafe(no_mangle)]`.
- Miri로 UB를 찾는다(`cargo miri test`, Windows는 `--target x86_64-unknown-linux-gnu`). 찾은 것이 없다고 건전하다는 뜻은 아니다.

## 성능
- 측정이 먼저다. 프로파일(Windows에서는 samply)에서 뜨거운 곳만 고친다. 가장 큰 개선은 알고리즘·자료구조 변경에서 나온다.
- release 빌드로 재야 한다. release 빌드는 요청이 있을 때만 한다 (rules.md).
- LTO, `codegen-units = 1`, `panic = "abort"`, `#[inline]`, allocator 변경은 바꿀 때마다 벤치마크한다. `#[inline]`은 느려질 수도 있다.
- 비용 없는 습관: 크기를 알면 `with_capacity`, 파일은 `BufReader`/`BufWriter`, 다시 순회만 할 거면 `collect` 대신 `impl Iterator` 반환.
- 차가운 코드의 clone은 괜찮다. 프로파일에서 뜨거운 clone만 없앤다.
