# ChatTaskBridge

**Windows PC에서 여러 단계의 작업을 검증하면서 같은 ChatGPT 대화를 이어 진행**하기 위한 로컬 작업 관리자 + 선택형 Chrome 확장 프로그램입니다.

## 구성

| 파일 | 역할 |
| --- | --- |
| `manager.py` | 단계 등록·시작·완료·실패·중단 및 상태 기록 |
| `dashboard.py` | 작업 계획을 작성하고 진행 상태를 보는 Tkinter GUI |
| `companion.py` | `127.0.0.1:18776`에서 실행되는 로컬 인증 서버 |
| `chrome-extension/` | 지정한 ChatGPT 대화 탭에서 다음 메시지를 보내고 화면의 전송 결과를 확인 |
| `setup-safe-test.py` | 빈 프로젝트에만 2단계 안전 테스트 생성 |
| `test_manager.py` | 실제 상태를 변경하지 않는 임시 디렉터리 회귀 테스트 |
| `AUTO-SEND-SETUP.md` | Chrome 자동 전송 설치 및 연결 절차 |

## 준비물

- Windows 10/11, **Python 3.10 이상** (표준 라이브러리의 `tkinter` 포함)
- Chrome, ChatGPT 로그인 및 작업 중 열어둘 **대화 탭** (`https://chatgpt.com/c/...`)
- ChatGPT에서 PC를 제어하기 위한 **Remote Desktop Commander 등 승인된 PC 연결**
- 자동 전송을 원할 때: Chrome 확장 설치, 로컬 서버 실행

## 설치

1. 이 `chat-task-bridge` 폴더 전체를 PC의 한 폴더에 다운로드하거나 복제합니다.
2. PowerShell에서 해당 폴더로 이동합니다.
3. `python .\test_manager.py`를 실행하여 `PASS`를 확인합니다.
4. `python .\dashboard.py`를 실행합니다. 프로젝트명과 한 줄당 하나의 작업을 입력하고 **위 계획 저장**을 누릅니다.
5. **자동 전송 없이 사용**할 때는 **질문 복사** 버튼으로 ChatGPT에 직접 질문을 보냅니다.
6. **자동으로 다음 질문을 보내려면** [AUTO-SEND-SETUP.md](AUTO-SEND-SETUP.md)를 따라 연결합니다.

기존 PC에서 Python이 `python` 명령으로 실행되지 않으면 실제 Python 설치 경로로 대체하세요. 예: `C:\Python314\python.exe`.

## 콘솔 사용법

```powershell
python .\manager.py init --title "작업 검증" --steps "1단계 파일 생성" "2단계 내용 확인" "3단계 최종 점검"
python .\manager.py status
python .\manager.py next
# 수동으로 메시지를 보낸 경우에만:
python .\manager.py start
# 연결된 PC에서 실제 작업 결과를 검증한 다음에만:
python .\manager.py complete --verified --note "파일을 다시 읽고 결과 확인"
# 오류가 있을 때:
python .\manager.py fail --reason "실제 실패 원인"
# 오류 확인·정리 후 명시적으로 재개:
python .\manager.py resume
```

**주의:** Chrome 자동 연결에서는 확장 프로그램이 단계를 `start`로 선점합니다. 이때 사용자가 `start`를 중복 실행하면 안 됩니다. 브라우저 전송 확인이 필요한 작업은 확인 전에는 `complete`가 거부됩니다.

## 진행 상태

- `ready`: 전송할 다음 단계 존재 (`next_ready=true`)
- `running`: 현재 단계가 시작됨; PC에서 작업 실행과 검증이 필요
- `paused`: 오류/검토 필요; 자동 전송 중지
- `completed`: 모든 단계가 검증됨 (`finished=true`, `next_ready=false`)
- `stopped`: 사용자가 종료했지만 모든 단계 검증 완료를 뜻하지는 않음

메시지 전송은 **현재 허용된 ChatGPT 탭에만** 적용되며, 입력창에 다른 글이 있으면 덮어쓰지 않습니다. 실제 대화 메시지가 확인되지 않으면 실패로 일시 정지하며 자동으로 반복 전송하지 않습니다.

## 검증 및 복구

```powershell
python .\test_manager.py
python .\manager.py status
```

- 단계별 완료는 단순 응답 생성이 아니라 **PC 파일/출력/로그 등 실제 결과 확인** 후 기록합니다.
- 실패 시 원인을 검토한 뒤 `resume`합니다. 이전 메시지가 전송됐을 가능성을 먼저 확인하여 중복 작업을 피하세요.
- `dispute`와 `reconcile-delivery`는 잘못된 완료 판정을 검토·정정하는 고급 수동 명령입니다. **증거 없이 전송 성공으로 바꾸지 마세요.**
- 테스트 중에도 ChatGPT 웹 UI 변경에 따라 전송 인식이 깨질 수 있습니다. 로그인 세션, 열린 대화 탭, Chrome 확장 진단을 함께 확인하세요.

## 개인정보 및 보안

`extension-secret.txt`, `state.json`, `events.jsonl`, `state.lock`, 테스트 출력 파일 및 각종 API 키는 **저장소에 업로드하지 마세요**. 최초 서버 실행 시 비밀 토큰이 로컬에 생성됩니다.

로컬 연결 서버는 loopback 주소 `127.0.0.1`만 사용합니다. 타인에게 토큰을 주지 말고 확장 프로그램도 신뢰하는 PC에만 설치하세요.

**중요:** 이 도구는 ChatGPT의 사용량 한도를 우회하거나 모든 작업을 무인으로 성공시키는 기능이 아닙니다. PC 연결과 실제 검증이 유지될 때만 다음 단계가 진행됩니다.
