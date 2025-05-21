---
tags:
  - 개발
  - 기술정리
  - MCP
created: 2025-05-07
type: 기술정리
---

# 📘 

VSCode는 GitHub Copilot 의 에이전트 모드와 함께 MCP 지원을 도입하였습니다. 이로써 개발자들은 MCP 호환 서버를 VSCode에 통합하여 외부 도구 및 서비스와의 상호작용을 표준화할 수 있게 되었습니다. 이를 통해 코드 작성, 디버깅, 테스트 등의 작업을 보다 효율적으로 수행할 수 있습니다. 

![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250507195501_image.png)

scode 설치 시, 에이전트 모드는 기본적으로 비활성 상태입니다. 가장 먼저 에이전트 모드를 활성화합니다. `ctrl + ,` 단축키 또는 메뉴창에서 `File - Preference - Settings`를 클릭합니다. 그리고 검색창에 `agent`라고 입력 합니다. `Chat > Agent: Enabled` 항목을 체크해서 에이전트 모드를 활성시킵니다.

![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250507195617_image.png)

## MCP 서버추가

VS Code에서 MCP 서버를 추가하는 데에는 여러 가지 방법이 존재합니다.
1. **Workspace settings** : `.vscode/mcp.json`작업 공간에 파일을 추가하여 작업 공간에 대한 MCP 서버를 구성하고 팀 구성원과 구성을 공유합니다.
2. **User settings** : 사용자 설정에서 서버를 지정하여 모든 작업 공간에서 MCP 서버를 활성화합니다.
3. **Automatic** : Claude Desktop 등 다른 도구에 정의된 MCP 서버를 자동으로 검색할 수 있습니다.
