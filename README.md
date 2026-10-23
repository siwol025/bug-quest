# bug-quest ⚔️

에러를 RPG 몬스터로 등장시키고 디버깅을 턴제 전투로 진행하는 Claude 스킬.
연출만 게임이고 재현·원인 분석·수정·재검증은 실제로 한다. HP는 원인을 실제로 좁혔을 때만 줄고, 고친 뒤 다시 실행해서 통과해야만 승리한다.

```
⚔️ 야생의 TypeError(Lv.3)가 나타났다!
  출현 위치: bug2.py:2
  HP ██████████ 100%

▶ 🔁 재현: can only concatenate str (not "int") to str ✅
  HP ████████░░ 80%
▶ 🗡️ 공격: "나이: " + age → f"나이: {age}"
▶ 재실행: 나이: 20

💥 치명타! TypeError를 쓰러뜨렸다!  +30 EXP
```

## 사용법

| 입력 | 동작 |
|------|------|
| `/bug-quest app.py` | 파일을 실행해서 에러를 찾고 전투 시작 |
| `/bug-quest KeyError: 'conf' ...` | 붙여넣은 에러로 전투 시작 |
| `/bug-quest calc.py 90이 나와야 하는데 63이 나와` | 에러 없이 결과가 틀린 버그는 기대값을 같이 적는다 |
| "이 파일 버그 잡자", "RPG로 디버깅해줘" | `/` 없이 말해도 Claude가 판단해 스킬을 켤 수 있다 |

몬스터 레벨: Lv.1 문법 오류 · Lv.3 런타임 에러 · Lv.5 결과가 틀림 · Lv.8 가끔만 터짐 · 👑 BOSS 환경 문제

## 설치

### 1) skills CLI (Claude Code·Cursor·Codex 등 공용, Node.js 필요)

```bash
npx skills add siwol025/bug-quest
npx skills add siwol025/bug-quest -g -y   # 전역 설치, 확인 생략
```

### 2) Claude Code 플러그인

Claude Code 안에서:

```
/plugin marketplace add siwol025/bug-quest
/plugin install bug-quest@bug-quest
```

터미널에서:

```bash
claude plugin marketplace add siwol025/bug-quest
claude plugin install bug-quest@bug-quest
```

플러그인으로 설치하면 `/bug-quest:bug-quest` 로 호출된다. 설치 후 Claude Code를 재시작한다.

### 3) 직접 복사

```bash
git clone https://github.com/siwol025/bug-quest.git
cp -r bug-quest/skills/bug-quest ~/.claude/skills/
```

## 라이선스

MIT
