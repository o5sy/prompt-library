# Claude Code 전역 환경 셋업 (상태줄 + Context7 MCP + oh-my-claudecode)

> **용도:** Claude Code 전역(`~/.claude`) 환경에 상태줄 컨텍스트 표시(ccstatusline), 최신 라이브러리 문서 조회(Context7 MCP), 에이전트/스킬 번들(oh-my-claudecode)을 설치하고 각 단계를 검증
> **모델 추천:** Claude Code (Sonnet/Opus 계열)

## 📋 Prompt
```text
아래 3개를 내 Claude Code 환경에 설치해줘. 각 단계마다 실제로 동작하는지 검증하고,
검증 결과를 보여준 뒤 다음으로 넘어가.
1) ccstatusline — 상태줄에 컨텍스트 포화도 표시
   - npx -y ccstatusline@latest 로 TUI 설치가 정석이지만 대화형이라 자동화가 안 되면,
     전역 설치 후 ~/.claude/settings.json 의 statusLine 을 직접 배선할 것.
     (전역 설치만 하면 아무것도 안 뜬다. 배선이 핵심이다.)
   - 컨텍스트가 "xxx/200k (100%)" 로 나오면 판독 오류가 아니라 윈도우 크기 오인이다.
     statusLine.command 앞에 CCSTATUSLINE_CONTEXT_SIZE_FALLBACK=1000000 을 붙일 것.
   - 위젯 사이에 separator 위젯을 명시적으로 넣지 않으면 항목이 붙어서 출력된다.
   - 검증: 실제 트랜스크립트 경로를 담은 Status 페이로드를 만들어 stdin 으로 먹이고
     렌더 결과를 보여줄 것.
2) Context7 MCP — 최신 라이브러리 문서 조회
   - claude mcp add --transport http context7 https://mcp.context7.com/mcp -s user
   - API 키 없이 붙는다(키는 레이트 리밋용).
   - 검증: tools/list 로 inputSchema 를 읽고, 실제로 라이브러리 하나를 resolve 한 뒤
     query-docs 까지 호출해서 문서가 오는지 확인할 것.
     resolve-library-id 는 libraryName 과 query 를 둘 다 요구한다(v4 기준).
3) oh-my-claudecode — 에이전트/스킬 번들
   - /plugin marketplace add https://github.com/Yeachan-Heo/oh-my-claudecode
   - /plugin install oh-my-claudecode@omc
마지막에 claude mcp list 와 settings.json 을 보여주고, 무엇이 바뀌었는지 요약해줘.
설치 전 ~/.claude/settings.json 은 반드시 백업할 것.
```

## 💡 Usage Tip
- 프로젝트 단위가 아니라 **전역(`~/.claude`) 셋업**용 프롬프트입니다. 새 머신/새 계정을 세팅할 때 그대로 재사용하세요.
- `settings.json`을 직접 건드리는 작업이라 백업 지시가 프롬프트 안에 포함돼 있습니다 — 실행 전에 백업이 실제로 만들어졌는지 꼭 확인하세요.
- 각 단계마다 "검증 결과를 보여준 뒤 다음으로 넘어가"라고 명시해두면, 배선만 해놓고 동작 확인 없이 끝내는 실패를 방지할 수 있습니다.
