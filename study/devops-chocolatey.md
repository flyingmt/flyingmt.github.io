## Chocolatey

윈도우에서 쉽게 패키지 관리하기

메인 작업 운영체제를 윈도우를 사용하다보니 어떻게하면 설치된 패키지를 쉽게 관리할까 고민을 많이 했습니다. 

- 윈도우를 초기화한 후 내가 설치한 프로그램들을 한번에 쉽게 설치할 수 없을까?
- 설치된 패키지를 한번에 업데이트 쉽게 할 수 없을까?

저는 옛날에는 개발을 위해 항상 설치해야 할 목록들 따로 가지고 있었어요. 
최근에는 그냥 그때 그때 필요할때 설치했었습니다. 그리고 느꼈습니다. "한번에 할 수 없을까?" 또, 프로그램을 실행 할때만다 업데이트를 물어보는것을 한번에 할 수 없을까?


Chocolatey가 이런 문제들을 해결해줬습니다. 

### Chocolatey 설치

- [Chocolatey 홈페이지](https://chocolatey.org/)
- [Chocolatey 설치](https://chocolatey.org/install)

관리자 권한으로 PowerShell을 실행하여 다음을 입력해서 엔터를 누르면 설치가 완료된다.

```
PS C:\WINDOWS\system32> Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

그 후 다음 처럼 'choco'을 입력해서 다음 화면이 나오면 설치가 설공한 것이다.

```
PS C:\WINDOWS\system32> choco
Chocolatey v0.10.15
Please run 'choco -?' or 'choco <command> -?' for help menu.
```

### Chocolatey를 이용하여 패키지 설치

Chocolatey는 https://chocolatey.org/search 에 접속해 설치하고자하는 패키지가 있는지 확인할 수 있다. 

만약에 Git을 설치하고 싶으면 다음처럼 입력하면 된다.

```
PS C:\WINDOWS\system32> choco install git
```

### 설치되어 있는 패키지 확인하기

```
PS C:\WINDOWS\system32> choco list -l
```

### 설치되어 있는 패키지 삭제하기

```
PS C:\WINDOWS\system32> choco uninstall git
```

### 설치되어 있는 모든 패키지 업그레이드 하기

```
PS C:\WINDOWS\system32> choco upgrade all
```

### Config 파일을 이용하여 패키지 설치하기

```
PS C:\WINDOWS\system32> choco install pkg.config
```
