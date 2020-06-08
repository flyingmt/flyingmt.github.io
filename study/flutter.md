## Flutter

### 개요

Flutter는 구글이 만든 크로스플랫폼 개발 프레임워크임.
안드로이드의 마티리얼 디자인과 iOS의 쿠퍼티노 디자인을 모두 제공함.

### 설치

- http://flutter.dev 에서 SDK 다운받아서 설치
  - 다운받아 c:/src/flutter에 압축을 품
  - 환경 변수에 추가함 : c:/src/flutter/bin
  - 설치확인 : flutter --version
- 개발툴
  - 안드로이드 스튜디오 : developer.andriod.com/studio
    - Plugins에서 Flutter 설치
  - 비주얼 스튜디오 코드 : code.visualstudio.com
- 개발툴 확인
  - flutter doctor
  - 애뮬레이터 생성
  - 예제 프로그램 실행하여 확인

### Dart 문법

- 연습 : http://dartpad.dev
- num 타입 : int / double (자동 형변환 지원하지 않음, num은 됨)
- 타입 추론 : var
- 상수 final
- 타입 검사 : is, is!
- 문자열내 변수 : \$
- 익명함수, 람다식 지원
- 선택 매개변수 {}
- 객체 생성시 new를 생략할수 있음
- 접근자 : \_ 붙으면 접근 불가능
- 접근 불가능한 \_ 붙은 변수는 get와 set으로 함수로 설정해야 함
- 상속 : extends, @override
- 추상클래스 : abstract
- 믹스인 : with
- 컬렉션 : List [] , Map {}, Set
- 스프레드 연산자: ... (중속 제거됨)
