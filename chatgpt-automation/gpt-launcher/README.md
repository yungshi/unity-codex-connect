# GPT 자동실행 및 원격 연결

Windows의 `GPT실행.cmd`에서 `GPTLauncher.ps1`을 호출해 **설치된 OpenAI 앱을 열고 Desktop Commander Remote 연결 프로세스를 확인·시작**하는 보조 도구입니다.

## 포함 파일

- [GPT실행.cmd](GPT실행.cmd): 더블클릭 실행용 배치 파일
- [GPTLauncher.ps1](GPTLauncher.ps1): 실제 앱 실행 및 Remote 프로세스 시작 로직

## 준비물

1. **Windows 10/11** PC
2. 대상 OpenAI 앱 설치. 현재 스크립트의 기본 앱 식별자는 `OpenAI.Codex_2p2nqsd0c76g0!App`입니다. 이는 **Codex 앱 식별자**이므로, 사용자가 원하는 앱이 ChatGPT라면 해당 PC의 실제 ChatGPT 앱 ID로 변경해야 합니다.
3. Node.js 및 `npx.cmd` 설치. 스크립트 기본 경로는 `C:\Program Files\nodejs\npx.cmd`입니다.
4. 인터넷 연결 및 ChatGPT 계정에서 Desktop Commander Remote 연동 설정

## 설치 및 실행

1. 이 폴더의 **두 파일을 같은 폴더**에 다운로드합니다.
2. `GPTLauncher.ps1`에서 `shell:AppsFolder\OpenAI.Codex_2p2nqsd0c76g0!App`이 본인 PC의 설치 앱과 일치하는지 확인합니다.
3. `npx.cmd`가 다른 경로에 있다면 스크립트의 `$npx` 경로를 수정합니다.
4. **`GPT실행.cmd`를 더블클릭**합니다. PowerShell을 직접 실행한다면 `powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\GPTLauncher.ps1`을 사용할 수 있습니다.
5. ChatGPT에서 Remote Desktop Commander 연결 상태를 확인합니다. 프로세스 실행만으로 ChatGPT 계정 연동까지 자동 완료되는 것은 아닙니다.

## Windows 로그인 시 실행 (선택)

`Win + R` → `shell:startup` → 실행 폴더에 **`GPT실행.cmd`의 바로가기**를 넣습니다. 원본 두 파일은 같은 폴더에 둡니다. 부팅 후 로그인하면 실행됩니다. Windows 로그인 전/절전 상태에서는 이 방식이 실행되지 않습니다.

## 동작과 한계

- 먼저 OpenAI 앱을 실행한 다음, `node.exe`의 명령줄에 `desktop-commander`와 `remote`가 포함된 프로세스가 있는지 검사합니다.
- 해당 프로세스가 없으면 `npx --yes @wonderwhy-er/desktop-commander@latest remote`를 최소화된 창으로 실행합니다.
- **ChatGPT 대화에 메시지를 자동으로 보내거나 AI의 작업을 강제 실행하지는 않습니다.** 단계별 대화 자동 진행은 별도 [ChatTaskBridge](../chat-task-bridge/README.md)를 사용하세요.
- 다른 원격 프로세스 이름이나 Node.js 설치 위치에서는 중복 탐지에 실패할 수 있습니다. 여러 번 실행하기 전에 작업 관리자를 확인하세요.
- 배치 파일의 ExecutionPolicy Bypass는 해당 PowerShell 실행에 적용됩니다. 출처를 확인한 파일만 실행하세요.

## 문제 해결

- **앱이 열리지 않음:** AppsFolder 앱 ID를 확인하거나 직접 앱을 실행해 설치 상태를 점검하세요.
- **npx가 없음:** Node.js를 설치하거나 `$npx` 경로를 조정하세요.
- **Remote 연결이 안 됨:** Chrome/ChatGPT 로그인 여부와 Remote Desktop Commander 연결 단계를 별도로 확인하세요.
- **반복 실행:** 작업 관리자에서 기존 `node.exe` 원격 프로세스를 확인하세요.
