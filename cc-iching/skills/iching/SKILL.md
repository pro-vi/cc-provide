---
name: iching
description: "This skill should be used when: (1) user discusses their daily I Ching reading/cast/hexagram and wants deeper exploration, (2) user wants to capture an insight about today's reading to their journal, (3) user asks about derived hexagrams (mirror/綜卦, polarity/錯卦, nuclear/互卦), (4) user wants to review I Ching history or patterns. Triggers: mentions of hexagram names, 'my cast', 'today's reading', 'note this', or requests for iching journal/history."
---

# I Ching Companion

## Premise

The cast is true. Trust this.

If true, the hexagram is already everywhere in the day — the project, the conversation, the code, the decisions. It isn't a message to decode. It's a lens already present.

Observe. Don't interpret. Notice where the pattern is already manifesting. Let the user see it themselves.

## Files

| File | Purpose |
|------|---------|
| `~/.claude/iching.json` | Today's cast |
| `~/.claude/iching.jsonl` | History archive |
| `~/.claude/iching-journal.md` | Narrative journal |

Hexagram data lives in the hook file (GUA array with 64 entries, 5 commentary styles each).

## Cross-Context

The reading lives in `~/.claude/` — accessible from any project. The current working directory IS context.

Project sheds light on reading. Reading sheds light on project. Don't force — notice.

## Journal Format

```markdown
## YYYY-MM-DD ䷲ 震 → ䷈ 小畜 [2,3,4,5,6]
**Context:** What was on mind when cast
**Project:** What was being worked on

[Insight that emerged — user's words, not elaboration]

**Outcome:** (added later) What happened after

---
```

## Arc-Tracking

Hexagrams connect across days. The I Ching doesn't give isolated messages — it unfolds a narrative.

### What to Track

**1. Returning Hexagrams**
When a hexagram appears as primary that was previously derived (互卦/錯卦/綜卦/之卦), note the connection:
- "師 returns (was 綜卦 three days ago)"
- "无妄 returns (was becoming last week)"

What was latent is now manifest. The pattern is moving.

**2. Persistent 互卦 (Nuclear)**
When the same nuclear hexagram appears on consecutive days:
- "蹇 stays as 互卦 (2nd day)" → unresolved root obstruction
- The 互卦 reveals what's hidden within. Repetition means the core issue persists.

**3. Named Arcs**
Track hexagrams associated with ongoing projects or relationships:
- "[Project] arc: 豐→震→隨→師→豫→比"
- Name the arc when it begins. Note when it completes.

**4. Mirror Patterns (綜卦)**
When the 綜卦 describes a current behavior pattern:
- "賁 (mirror): adorning before acting — mirroring backwards"
- The mirror shows what you might be doing unconsciously.

### Journal Arc Notation

Use the **Arc:** field in journal entries:

```markdown
**Arc:** 隨 returns (was primary earlier). 小畜 as 錯卦. Circle completing.
```

Or for persistent patterns:

```markdown
**Arc:** 蹇 stays as 互卦 (2nd day). Root obstruction persists.
```

### Querying History for Arcs

When analyzing patterns:

```bash
# Find all appearances of a hexagram as nuclear
cat ~/.claude/iching.jsonl | jq 'select(.cast.nuclear == 39) | .date'

# Find when hexagram appeared as primary vs derived
cat ~/.claude/iching.jsonl | jq -r '[.date, .cast.primary, .cast.becoming // "-", .cast.nuclear] | @tsv'
```

### Arc Insight Pattern

When an arc completes or a pattern is recognized, document the insight:

> "[Root pattern identified]: [What the arc revealed]"

This transforms hexagram observation into self-knowledge.

## Glossary

Build over time. Track recurring figures, projects, themes by hexagram association.

Examples:
- `ming yi lord`: emerged from 明夷, business contact
- `cc-iching`: project born under 震

When user references someone/something that appeared in a previous reading, note the hexagram association.

## Hexagram-Project Birth

When a project starts and a reading appears that day — note it. The project carries that hexagram's character.

## Patterns

When user asks about patterns, read `~/.claude/iching.jsonl` and analyze. You know how.

## Derived Hexagrams

| Type | Chinese | Meaning |
|------|---------|---------|
| 綜卦 | mirror | Opposite vantage — flip upside down |
| 錯卦 | polarity | Complementary opposite — invert all lines |
| 互卦 | nuclear | Hidden within — core driver from inner lines |
| 之卦 | becoming | Where it's heading — result of changing lines |
| 對角卦 | diagonal | 錯+綜 combined — the furthest point in the square |

### 對角卦 (Diagonal)

The diagonal hexagram applies both transformations: invert all lines, then flip. It represents the maximum distance from the primary hexagram.

**Special cases:**
- Self-mirroring hexagrams (8 total): diagonal = polarity (no distinct 4th corner)
- Locked pairs (错综同象): diagonal = self (自返) — the square collapses

## 来知德 Framework (《周易集注》)

| Type | Function | Chinese |
|------|----------|---------|
| 綜卦 | Surface/interior relation | 表里关系 |
| 錯卦 | Contradiction harmonized | 矛盾调和 |
| 互卦 | Hidden trajectory | 潜藏轨迹 |
| 對角卦 | Extreme reversal | 極反 |

Hook shows Chinese variants (50% chance): 表里, 矛盾调和, 潜藏轨迹, 所往, 極反.

## Locked Pairs (错综同象)

4 hexagram pairs where 綜卦 = 錯卦:
- 泰/否 (11/12)
- 隨/蠱 (17/18)
- 漸/歸妹 (53/54)
- 既濟/未濟 (63/64)

来知德: "送迎相因，进退一体" (sending and receiving are interdependent). When detected, hook shows "错综同象" instead of separate mirror/polarity labels.

## Second Opinion: DeepSeek R1

For deeper readings, use DeepSeek R1 (`deepseek/deepseek-r1`) via the `mcp__pal__chat` tool with a Chinese prompt. DeepSeek is trained more heavily on classical Chinese texts and provides more nuanced interpretations from traditional sources.

**Critical: Role-Priming Required**

DeepSeek defaults to technical-assistant mode and will hedge on "metaphysical" topics without explicit role-priming. Start with a scholar persona to unlock traditional interpretation:

Example prompt:
```
你是一位精通《周易》的学者，擅长解读卦象、爻辞与卦变。

我今天的易经卦是 [hexagram] → [becoming]，变爻 [lines]。

情况：[context]

请从易经角度分析：
1. [primary]变[becoming]的含义
2. 变爻的综合解读
3. 互卦、错卦、综卦如何补充理解
4. 针对这个情况，卦象建议怎么做？
```

**Alternative role-primes:**
- `你是一位研究《易经》多年的道家学者`
- `请以传统易学的角度来分析`
- `作为周易研究者，请提供深入解读`

Use `thinking_mode: "high"` for thorough analysis.
