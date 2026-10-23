# bug-quest ⚔️

에러를 RPG 몬스터로 등장시키고 디버깅을 턴제 전투로 진행하는 Claude 스킬.

## 스킬 설명

연출만 게임이고 재현·원인 분석·수정·재검증은 **실제로** 한다.

- **턴제**: 한 턴에 행동 하나만 하고 메뉴를 띄운 뒤 멈춘다. 플레이어가 번호를 골라야 다음 턴으로 넘어간다
- 가설이 빗나가면 몬스터의 반격으로 🧠 집중력(❤️❤️❤️)이 깎인다. 0이 되면 힌트·자동 전투·후퇴 중에서 고른다
- HP는 원인을 실제로 좁혔을 때만 줄어든다. 가설이 틀리면 "빗나갔다!"
- 고친 뒤 원래 에러가 났던 실행을 **다시 돌려서 통과해야만** 승리
- 증상만 막은 수정은 승리가 아니라 "봉인(우회)"으로 표시하고 근본 원인을 남긴다
- 못 고치면 솔직하게 "후퇴"하고 알아낸 정보와 다음에 해 볼 것을 남긴다

### 몬스터 레벨

| 레벨 | 종류 | 예 |
|------|------|-----|
| Lv.1 | 문법·오타 | `SyntaxError`, `IndentationError` |
| Lv.3 | 런타임 단일 원인 | `KeyError`, `TypeError`, `FileNotFoundError` |
| Lv.5 | 에러 없이 결과가 틀림 | 좌표 어긋남, off-by-one |
| Lv.8 | 가끔만 터짐 | 타이밍, 네트워크, 스트림 끊김 |
| 👑 BOSS | 환경 문제 | "제 컴퓨터에선 되는데요", 버전·인코딩·경로 |

### 전투 스킬 (= 실제 디버깅 행동)

| 스킬 | 실제 행동 |
|------|-----------|
| 🔍 정찰 | 코드·트레이스백 읽기 |
| 🔁 재현 | 에러를 실제로 다시 일으키기 |
| 🧪 탐지 마법 | 가설 하나를 세우고 print·로그·작은 테스트로 확인 |
| 🗡️ 공격 | 원인 코드 수정 |
| 🛡️ 방어 | 같은 버그가 다시 안 생기게 하는 처리 |

전투가 끝나면 원인·수정 위치·검증 결과·교훈을 담은 **전투 기록** 카드를 남긴다.

## 설치

### 1) Claude Code 플러그인

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

설치 후 Claude Code를 재시작한다. 플러그인으로 설치하면 `/bug-quest:bug-quest` 로 호출된다.
업데이트: `claude plugin update bug-quest@bug-quest`

### 2) skills CLI (Claude Code·Cursor·Codex 등 공용, Node.js 필요)

```bash
npx skills add siwol025/bug-quest
npx skills add siwol025/bug-quest -g -y   # 전역 설치, 확인 생략
```

### 3) 직접 복사

```bash
git clone https://github.com/siwol025/bug-quest.git
cp -r bug-quest/skills/bug-quest ~/.claude/skills/
```

`/bug-quest` 로 호출된다.

## 사용 예시

### 부르는 방법

| 입력 | 동작 |
|------|------|
| `/bug-quest app.py` | 파일을 실행해서 에러를 찾고 전투 시작 |
| `/bug-quest KeyError: 'conf' ...` | 붙여넣은 에러로 전투 시작 |
| `/bug-quest calc.py 평균이 90.0이어야 하는데 63.33이 나와` | 에러 없이 결과가 틀린 버그는 **기대값을 같이** 적는다 |
| `/bug-quest app.py --auto` | 턴마다 멈추지 않고 결판까지 한 번에 진행 |
| `/bug-quest` | 어떤 에러를 잡을지 물어본다 |
| "이 파일 버그 잡자", "RPG로 디버깅해줘" | `/` 없이 말해도 Claude가 판단해 스킬을 켤 수 있다 (항상 켜지는 건 아님) |

### 턴제 전투 예시

```
/bug-quest bug2.py
```

```
⚔️ 야생의 TypeError(Lv.3)가 나타났다!
  출현 위치: bug2.py:2   print("나이: " + age)
  몬스터 HP ██████████ 100%  |  내 🧠 집중력 ❤️❤️❤️

── 턴 1 | 당신의 차례 ──
  1. 🔍 정찰       bug2.py 코드 읽기 ⭐추천
  2. 🔁 재현       다시 실행해 보기
  3. 🧪 탐지 마법  가설: "age가 int일 것이다"
  4. 🗡️ 공격       바로 고쳐 보기
  5. 🏃 후퇴
  0. ⚡ 자동 전투  끝까지 알아서
번호를 골라 주세요. 가설을 직접 적으셔도 돼요.
```

플레이어가 `3 가설: age가 None일 것이다` 를 입력하면:

```
▶ 🧪 탐지 마법: "age가 None일 것이다"
  → type(age), repr(age) 확인: <class 'int'> 20  ❌ 빗나갔다!
👾 TypeError의 반격! 없는 None을 찾아 헤매다 지쳤다… 🧠 ❤️❤️🖤
  몬스터 HP ██████████ 100%

── 턴 2 | 당신의 차례 ──
  ...
```

### 자동 전투 예시 (`--auto`)

아래 코드는 에러 없이 평균이 틀리게 나온다.

```python
# calc.py
scores = [80, 90, 100]
total = 0
for i in range(1, len(scores)):
    total += scores[i]
print("평균:", total / len(scores))
```

```
/bug-quest calc.py 평균이 90.0이어야 하는데 63.33이 나와 --auto
```

```
⚔️ 정체불명의 Off-by-one(Lv.5)이 나타났다!
  출현 위치: calc.py:3
  HP ██████████ 100%

▶ 🔁 재현: 출력 "평균: 63.333..." — 기대값 90.0과 다름 ✅
  HP ████████░░ 80%

▶ 🧪 탐지 마법: "반복문이 첫 번째 점수를 건너뛴다"
  → 더한 인덱스: [1, 2] | 더한 값: [90, 100] | total: 190  ✅ 적중!
  HP █░░░░░░░░░ 10%

▶ 🗡️ 공격: range(1, len(scores)) → range(len(scores))
▶ 재실행: 평균: 90.0

💥 치명타! Off-by-one을 쓰러뜨렸다!  +50 EXP
   드롭: 📜 교훈 "Python 인덱스는 0부터 시작한다"

📋 전투 기록
  몬스터: Off-by-one(Lv.5) | 결과: 승리 | 턴: 3
  원인: range(1, ...)로 시작해 scores[0](80)이 빠짐
  수정: calc.py:3  range(1, len(scores)) → range(len(scores))
  검증: python calc.py 재실행 → 평균: 90.0
  교훈: Python 인덱스는 0부터 시작한다
```

## 라이선스

MIT
