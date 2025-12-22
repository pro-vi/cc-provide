# cc-iching 易經

> **Note**: Plugin hooks currently don't pass output to the agent ([#12151](https://github.com/anthropics/claude-code/issues/12151)). Use manual installation below until fixed.

A Claude Code hook for daily I Ching divination using authentic 3-coin casting with true entropy.

## Foreword

I Ching is the oldest binary system in existence, encoding wisdom through broken and unbroken lines (yin and yang) representing 0 and 1 millennia before Leibniz formalized binary mathematics in the 17th century, demonstrating that binary logic has been fundamental to human understanding for over three thousand years.

I built this simple hook as an attempt to demonstrate the I Ching's binary nature in modern code, while embedding this ancient wisdom into a contemporary medium. This aims to help anyone interested in dipping their toes into divination or exploring the philosophical roots of computing without breaking their flow in Claude Code.

## Features

- **Authentic casting**: Uses `crypto.randomBytes()` for OS-level entropy (hardware noise, interrupt timing)
- **Daily hexagram**: One cast per day, cached locally, with a historic log of past readings
- **5 commentary styles**: 大象傳, 彖傳, each with English translations, and a Wilhelm-inspired interpretation (experimental)
- **4 derived hexagrams**: 互卦 (nuclear), 錯卦 (shadow), 綜卦 (mirror), 之卦 (becoming)
- **Progressive reveal**: 30% chance to show commentary or derived hexagrams on subsequent prompts
- **Non-spammy**: 70% of prompts are silent after the first reading
- **Culturally grounded**: Traditional casting method with scholarly translations

## Installation

### Via Plugin Marketplace

Requires [Bun](https://bun.sh/) runtime (`brew install bun`).

```bash
/plugin marketplace add pro-vi/cc-provide
/plugin install cc-iching
```

### Manual Installation

1. **Prerequisite**: Install [Bun](https://bun.sh/) runtime

   ```bash
   brew install bun
   ```

2. **Copy hook file**:

   ```bash
   cp hooks/iching.ts ~/.claude/hooks/iching.ts
   ```

3. **Add to settings** (`~/.claude/settings.json`):

   ```json
   {
     "hooks": {
       "UserPromptSubmit": [
         {
           "type": "command",
           "command": "bun ~/.claude/hooks/iching.ts",
           "timeout": 5000
         }
       ]
     }
   }
   ```

4. **Test**:
   ```bash
   echo '{}' | bun ~/.claude/hooks/iching.ts
   ```

### Hook Details

Uses the `UserPromptSubmit` hook (exit 0). Output appears as a system reminder before Claude responds. Claude sees it and may reference it in reasoning.

## Output Example

**First prompt of day (primary reading):**

```
䷶ 豐 (Fēng) — Thunder and lightning arrive together; the noble one decides cases and applies consequences → ䷣ 明夷 [4]
```

**Sample subsequent prompts (30% chance):**

```
互卦 (hidden within) ䷼ 中孚 (Zhōng Fú) — Wind above the lake; the noble one deliberates on cases and delays executions
錯卦 (shadow) ䷺ 渙 (Huàn) — Wind moves over water; the ancient kings made offerings and established temples
綜卦 (mirror) ䷷ 旅 (Lǚ) — Fire upon the mountain; the noble one applies consequences wisely, resolves matters swiftly
綜卦 (自綜) ䷀ 乾 (Qián) — Heaven moves with vigor; the noble one strives ceaselessly
之卦 (becoming) ䷣ 明夷 (Míng Yí) — Brightness enters the earth; the noble one governs by concealing brilliance within
```

## Progressive Reveal

**First prompt each day:** Full reading with 大象傳 (Image commentary)

**Subsequent prompts:** 30% chance of showing one of the following:

| Type        | Description                          | Chance |
| ----------- | ------------------------------------ | ------ |
| 大象傳 (dx) | Image commentary (Chinese)           | 4%     |
| 彖傳 (tu)   | Judgment commentary (Chinese)        | 4%     |
| 大象傳 (en) | Image commentary (English)           | 4%     |
| 彖傳 (te)   | Judgment commentary (English)        | 4%     |
| Wilhelm (w) | Wilhelm/Jung inspired interpretation | 4%     |
| 互卦        | Nuclear hexagram (hidden within)     | 2.5%   |
| 錯卦        | Shadow hexagram (opposite path)      | 2.5%   |
| 綜卦        | Mirror hexagram (reversed view)      | 2.5%   |
| 之卦        | Becoming hexagram (transformation)   | 2.5%   |

70% of prompts remain silent after the initial reading.

## How It Works

### Casting Method (擲錢法)

- 3 coins × 6 lines
- Each coin: heads = 3, tails = 2
- Line values:
  - 6 (old yin, 1/8) — changing to yang
  - 7 (young yang, 3/8) — stable
  - 8 (young yin, 3/8) — stable
  - 9 (old yang, 1/8) — changing to yin

### Cryptographic Randomness

This hook uses `crypto.randomBytes()` instead of `Math.random()`. The difference matters.

**`Math.random()`** is a fast PRNG (pseudo-random number generator). Implementation-defined, not designed to resist prediction. Fine for games, not for anything where unpredictability matters.

**`crypto.randomBytes()`** is a CSPRNG (cryptographically secure PRNG) seeded by OS-provided entropy. Still deterministic under the hood, but the internal state is computationally infeasible to predict.

When you cast coins or yarrow stalks, the outcome emerges from your table's surface, air currents, the tremor in your hand, making the randomness inseparable from your present moment and bodily state, which is why traditional casting feels personally meaningful.

The CSPRNG's entropy pool works analogously. It draws from your machine: the hardware itself, timing variations in your system, the accumulated state, randomness emerging from the physical context you're already part of, only abstracted through layers of silicon and mathematics.

You get one cast per day.

### Storage & Cache

- **Daily cache**: `~/.claude/iching.json` — today's reading (delete to force new cast)
- **History**: `~/.claude/iching.jsonl` — append-only log of past readings (update on new cast)
- **Timezone**: Local system time determines when midnight resets the daily cast

## How I Use It

When a reading pops up that piques my interest, I open a new Claude Code session to investigate deeper. If I'm working in a project, Claude already has visibility into the process, so the reading becomes immediately applicable rather than abstract. Multiple angles are built in to achieve a balanced and nuanced perspective:

- **Primary hexagram** — the situation as it appears
- **Changing lines** — where the energy is active
- **互卦 (hidden within)** — what's gestating inside
- **錯卦 (shadow)** — what I should be aware of
- **綜卦 (mirror)** — how others might see it
- **之卦 (becoming)** — where it's heading

I particularly appreciated how the complete array of derived hexagrams, when viewed together, synthesized, illuminated both outer conditions and inner experience with remarkable intensity and nuance.

The hook is non-spammy by design as most prompts are silent. When a reading appears, I see it as an invitation to reflect and dive deeper into the practice. Of course, you may ask Claude to update the mechanism or adjust the frequency to suit your needs.

## Roadmap

- [x] **自綜 self-mirroring detection** — 8 hexagrams are vertically symmetric and therefore self-mirroring. Identify and label accordingly.
- [ ] **相綜 pair descriptions** — The 28 hexagram pairs represent opposite perspectives on the same situation. Understanding the pairing adds depth to mirror hexagram readings.
- [ ] **互卦 trigram breakdown** — Show the inner/outer trigrams that form the nuclear hexagram. The inner trigram reflects the internal landscape, the outer shows the external situation.
- [ ] **爻辭 line texts** — Specific counsel for each changing line. When lines transform, these texts address exactly where and how the energy is moving.
- [ ] **Wilhelm/Baynes commentary integration** — Update the classic I Ching interpretations alongside readings, with proper attribution to the 1950 Bollingen Series translation.
- [ ] **Commentary refinement** — Classical translations shall evolve as I explore each hexagram more deeply through practice.

## License

MIT
