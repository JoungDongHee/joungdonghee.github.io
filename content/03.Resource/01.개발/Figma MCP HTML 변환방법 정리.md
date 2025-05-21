---
tags:
  - 개발
  - 기술정리
aliases: [Figma MCP HTML 변환 방법, Figma 디자인 HTML 코드 변환]
type: 기술정리
created: 2025-05-20
---

# 📘 Figma MCP HTML 변환방법

[[Figma]] MCP를 활용하면 완성된 피그마 디자인을 간단하게 [[HTML]] 코드로 변환할 수 있습니다. 이 문서는 변환을 위한 준비부터 실행까지 전반적인 과정을 설명합니다.

---

## 1. Figma API 키 발급

먼저 [[Figma]] MCP를 활용하려면 [[API]] 키를 발급받아야 합니다.

1. **Settings** 메뉴에 진입  
![Settings](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250520225529_image.png)

2. **Security** 탭에서 **Personal Access Tokens** 항목 선택  
![Security](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250520225731_image.png)

3. 키 발급 및 권한 설정

### 권한 설명

| 항목 | 설명 |
|------|------|
| **Comments** | 파일 내 댓글을 읽고(Read), 작성하고(Post), 삭제(Delete)할 수 있는 권한입니다. |
| **Current user** | 인증된 사용자의 이름, 이메일, 프로필 이미지를 읽는 권한입니다. |
| **Dev resources** | 개발 리소스를 읽고 쓸 수 있는 권한입니다. |
| **File content** | 파일의 내용을 읽고, 이미지를 렌더링할 수 있는 권한입니다. |
| **File metadata** | 파일 이름, 크기, 작성일 등 메타데이터를 읽는 권한입니다. |
| **File versions** | 파일의 버전 기록을 읽는 권한입니다. |
| **Library assets** | 개별 컴포넌트 및 스타일 데이터를 읽는 권한입니다. |
| **Library content** | 퍼블리시된 컴포넌트 및 스타일을 읽는 권한입니다. |
| **Projects** | 팀 프로젝트 구조를 읽는 권한입니다. |
| **Team library content** | 팀 라이브러리에 퍼블리시된 컴포넌트 및 스타일을 읽는 권한입니다. |
| **Webhooks** | 웹훅을 조회(List), 생성(Create), 수정(Update)할 수 있는 권한입니다. |

> ⚠️ **MCP에서 어떤 권한이 필요한지 명확하지 않기 때문에, 전체 권한을 Read 또는 Write로 부여하는 것이 안전합니다.**

---

## 2. MCP 세팅

[MCP GitHub 저장소](https://github.com/GLips/Figma-Context-MCP?tab=readme-ov-file)에서 한글 가이드를 확인할 수 있습니다.

`YOUR-KEY`는 발급받은 Figma API 키로 대체하면 됩니다.

### ✅ MacOS / Linux

```json
{
  "mcpServers": {
    "Framelink Figma MCP": {
      "command": "npx",
      "args": ["-y", "figma-developer-mcp", "--figma-api-key=YOUR-KEY", "--stdio"]
    }
  }
}
```

### ✅ Windows

```json
{
  "mcpServers": {
    "Framelink Figma MCP": {
      "command": "cmd",
      "args": ["/c", "npx", "-y", "figma-developer-mcp", "--figma-api-key=YOUR-KEY", "--stdio"]
    }
  }
}
```

---

## 3. 피그마 링크 복사

Figma에서 완성된 디자인의 프레임을 우클릭 후  
**Copy/Paste as → Copy link to selection**을 클릭해 링크를 복사합니다.  
![Copy link](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250520230353_image.png)

---

## 4. 요청하기

MCP 클라이언트에서 다음과 같은 형식으로 메시지를 작성하면 자동으로 [[HTML]] 코드가 생성됩니다.

```
@[https://www.figma.com/design/your-figma-link](링크)

위 피그마 디자인의 html 을 만들어주세요
```

> 링크는 프레임 단위의 링크여야 하며, 복사한 링크 그대로 사용합니다.

예시 화면:
![요청 예시](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250520230884_image.png)

---

## 💡 출력 예시 및 주의사항

* HTML 코드가 자동 생성되며 브라우저에서 바로 확인 가능합니다.
* 구조가 단순한 디자인은 한 번에 정확히 변환되지만, 복잡한 경우 일부 클래스명이나 구조에 대한 수정을 요청해야 합니다.
* HTML과 디자인 결과가 다를 경우, 명확한 설명을 함께 제공하면 더 정확한 변환이 가능합니다.

완성 화면:
![결과](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250520230891_image.png)

---

## 🔗 관련 문서

- [[Figma]]
- [[HTML]]
- [[API]]
- [[Model Context Protocol 정리|MCP]]
