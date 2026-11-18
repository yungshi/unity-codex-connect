# ChatGPT Automation Tools (Windows)

Windows에서 사용하는 두 가지 독립 도구를 보관한 폴더입니다. 기존 Unity–Codex 연결 도구와는 별개이며, 이 폴더의 파일만 설치하면 됩니다.

| 도구 | 설명 | 설치 및 사용법 |
| --- | --- | --- |
| **GPT 자동실행 / 원격 연결** | 설치된 OpenAI 앱을 열고 Desktop Commander Remote 프로세스가 없다면 실행 | [gpt-launcher/README.md](gpt-launcher/README.md) |
| **ChatTaskBridge** | PC에서 작업 단계를 관리하고 선택한 Chrome ChatGPT 대화에 다음 단계를 이어서 보내는 도구 | [chat-task-bridge/README.md](chat-task-bridge/README.md) |

## 주의

- **인증 토큰, API 키, 개인 대화, 작업 상태 파일은 포함하지 않습니다.** 특히 `extension-secret.txt`는 PC에서 생성해 개인적으로 보관해야 합니다.
- ChatTaskBridge는 Windows PC, Python, Chrome, ChatGPT 로그인, 연결된 PC 원격 제어 도구가 필요합니다. 설치만으로 ChatGPT가 무인으로 영구 동작하지는 않습니다.
- 대상 ChatGPT 대화 탭이 표시되어야 자동 전송을 시도합니다. 전송 결과 확인에 실패하면 자동 재시도하지 않고 일시 정지합니다.
- 다른 PC에서는 앱 식별자·Node.js 경로·로컬 파일 위치를 환경에 맞게 조정할 수 있습니다.
- 코드와 문서가 맞지 않을 때는 프로그램 상태와 실제 검증 결과를 우선합니다.

## 빠른 시작

1. 앱/원격 연결만 필요하면 [GPT 자동실행 안내](gpt-launcher/README.md)를 따릅니다.
2. 단계별 이어쓰기가 필요하면 [ChatTaskBridge 안내](chat-task-bridge/README.md)를 따릅니다.
3. 자동 전송 설치는 [Chrome 확장 연결 안내](chat-task-bridge/AUTO-SEND-SETUP.md)를 참고하세요.
