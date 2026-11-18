# ChatTaskBridge 자동 이어쓰기: Chrome 연결 안내

ChatTaskBridge의 로컬 작업 관리자와 **사용자가 허용한 특정 ChatGPT 대화 탭**을 Chrome 확장 프로그램으로 연결합니다.

## 최초 설치

1. Windows PC에 Python과 Chrome을 설치하고 ChatGPT에 로그인합니다.
2. `chat-task-bridge` 폴더에서 `python .\test_manager.py`를 실행해 상태 관리자 회귀 테스트를 통과했는지 확인합니다.
3. `python .\dashboard.py`에서 프로젝트와 단계별 작업을 등록합니다. 현재 프로젝트가 `ready`이면 다음 단계가 준비된 것입니다.
4. **별도 PowerShell 창**에서 `python .\companion.py`를 실행합니다. 창을 닫지 마세요. 로컬 서버가 `127.0.0.1:18776`에서 동작합니다.
5. 처음 실행 시 같은 폴더에 생성되는 `extension-secret.txt`의 **토큰 값만** 확인합니다. 이 파일은 GitHub에 올리거나 타인에게 공유하지 않습니다.
6. Chrome에서 `chrome://extensions` 열기 → **개발자 모드** 켜기 → **압축해제된 확장 프로그램을 로드합니다** → `chat-task-bridge/chrome-extension` 폴더 선택.
7. 확장 아이콘 클릭 → 토큰 입력 → **토큰 저장 / 연결 확인**.
8. 자동으로 이어 쓸 ChatGPT 대화 (`https://chatgpt.com/c/...`)를 한 탭에서 엽니다. 확장 아이콘 → **이 ChatGPT 대화에서 자동 이어쓰기 시작** → 확인창 승인.
9. **현재 탭 진단 / 스크립트 연결** 버튼으로 URL 일치, 허용 탭, 입력창 인식과 자동 전송 활성 상태를 점검합니다.

## 실행 흐름

1. PC 프로젝트가 `phase=ready`, `next_ready=true` 상태입니다.
2. 확장 프로그램이 현재 허용된 탭에서 입력창이 비어 있고 ChatGPT 응답 생성 중이 아닐 때만 다음 프롬프트를 가져옵니다.
3. 서버가 작업을 선점해 `running`으로 바꾸고, 확장 프로그램은 메시지 입력·보내기를 시도합니다.
4. **실제 새 대화 메시지 확인**에 성공하면 `delivery-verified`가 PC에 기록됩니다. 확인 실패는 `paused`와 오류 기록으로 이어집니다.
5. ChatGPT가 연결된 PC에서 작업을 수행하고 실제 결과를 검증한 다음 `manager.py complete --verified --note "검증 내용"`을 실행합니다.
6. 다음 단계가 있으면 `ready`로, 모두 완료했으면 `finished=true`로 종료합니다.

자동 이어쓰기는 **Chrome 대화 탭이 화면에 보이고 PC 연결이 살아 있을 때만** 시도합니다. ChatGPT가 Remote Desktop Commander에 연결되지 않으면 PC 단계를 직접 수행할 수 없습니다.

## 멈춤과 재개

- 언제든 확장 팝업의 **자동 이어쓰기 중지** 버튼으로 허용을 해제할 수 있습니다.
- 현재 프로젝트가 `paused`여도 사용자가 **대화방 연결만 먼저** 할 수 있습니다. 그 상태에서는 다음 메시지를 보내지 않습니다.
- 실패 시 PC 상태와 현재 ChatGPT 대화 기록을 확인하고, **기존 메시지가 실제 전송되지 않았는지 확인한 뒤** `python .\manager.py resume`으로 명시적으로 재개하세요.
- Chrome 확장 코드를 수정하거나 업데이트한 후에는 `chrome://extensions`의 새로고침 버튼과 ChatGPT 탭의 `F5`를 실행하세요.
- PC 프로젝트가 `completed`라면 `next_ready=false`이므로 다음 메시지를 보내지 않습니다.

## 안전 및 문제 해결

| 증상 | 확인할 사항 |
| --- | --- |
| PC 연결 실패 | `companion.py` 실행, 로컬 포트 18776, 토큰 입력 |
| URL 일치 false | 정확한 `/c/...` 대화에서 **자동 이어쓰기 시작** 다시 승인 |
| 허용한 탭 false | 다른 탭에서 열지 않았는지 확인, 현재 탭 다시 승인 |
| 보내기 버튼 준비 false | 입력창이 비어 있으면 정상일 수 있음 |
| 입력창에 내용이 남음 | 사용자가 입력하던 내용은 덮어쓰지 않음; 확인 후 직접 정리 |
| 전송 확인 실패 | 대화 기록과 확장 진단 확인; 자동 재전송 금지 |
| 작업 진행 안 됨 | PC 연결 도구 상태와 `manager.py status` 확인 |

토큰은 로컬 인증 비밀값입니다. **토큰 파일, 작업 기록, 개인 데이터는 GitHub에 포함하지 않습니다.** 다른 사이트나 익명 확장 프로그램에 토큰을 입력하지 마세요.
