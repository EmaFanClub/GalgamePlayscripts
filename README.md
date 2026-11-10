# GalgamePlayscripts

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Visual Novels](https://img.shields.io/badge/Visual_Novels-41_Titles-purple.svg)](#catalog-of-playscripts)
[![Total Scenes](https://img.shields.io/badge/Total_Scenes-1,634-orange.svg)](#catalog-of-playscripts)
[![Total Lines](https://img.shields.io/badge/Total_Lines-1.45M+-brightgreen.svg)](#catalog-of-playscripts)
[![Format](https://img.shields.io/badge/Format-Structured_JSON-red.svg)](#schema-specification)
[![EverMemoryArchive Compatible](https://img.shields.io/badge/EMA-Benchmark_Ready-success.svg)](https://github.com/EmaFanClub/EverMemoryArchive)

**GalgamePlayscripts** is an open-access, machine-readable dataset archive containing **41 structured visual novel (Galgame) playscripts** encompassing **1,634 narrative scenes** and **1,453,801 atomic lines** of dialogue, monologue, and narration.

All scripts in this archive originate from historical visual novels produced by **defunct, dissolved, or inactive developer circles and studios** (such as *minori*, *戯画 (GIGA)*, *KID*, *elf*, *FlyingShine*, *feng*, *Lass*, *Studio e・go!*, *ALcot*, *TRUMPLE*, *PULLTOP*, *Clochette*, and others).

---

## Table of Contents

1. [Key Features & Motivation](#key-features--motivation)
2. [Dataset Structure](#dataset-structure)
3. [Schema Specification](#schema-specification)
4. [Discourse Taxonomy](#discourse-taxonomy)
5. [Catalog of Playscripts](#catalog-of-playscripts)
6. [Quickstart & Usage](#quickstart--usage)
   - [Python Quickstart](#python-quickstart)
   - [TypeScript / Node.js Quickstart](#typescript--nodejs-quickstart)
   - [Data Sample](#data-sample)
7. [Preprocessing & Normalization](#preprocessing--normalization)
8. [Ethical Considerations & Digital Preservation](#ethical-considerations--digital-preservation)
9. [Citation & License](#citation--license)

---

## Key Features & Motivation

### 1. Lifelong AI Companion & Narrative Memory Benchmarking
Visual novel playscripts represent a uniquely challenging domain for conversational AI and memory architectures:
- **Long-Horizon Episodic Trajectories**: Stories span tens of thousands of continuous dialogue turns across multiple days, weeks, and distinct narrative branches.
- **Deep Emotional & Relationship Dynamics**: Character personas evolve with affection, trust, conflict, and reconciliation over time.
- **Multi-Turn Grounding**: Ideal for evaluating long-context retrieval, episodic memory consolidation, persona consistency, and cognitive companion architectures like [EverMemoryArchive (EMA)](https://github.com/EmaFanClub/EverMemoryArchive).

### 2. Fine-Grained Discourse Classification
Every sentence is categorized into atomic discourse types (`dialogue`, `monologue`, `narration`, `choice`, `transition`), distinguishing spoken character dialogue from unspoken inner thoughts and world exposition.

### 3. Digital Cultural Preservation
Many classic visual novel studios have gone bankrupt or permanently dissolved over the past two decades. Physical media decays, and proprietary visual novel script engines become incompatible with modern operating systems. By standardizing these works into portable JSON formats linked with [VNDB (Visual Novel Database)](https://vndb.org) identifiers, this repository preserves interactive digital literature for future linguistic, narrative, and computational study.

---

## Dataset Structure

Each visual novel playscript is encapsulated inside its own directory under `playscripts/<id>/`:

```
GalgamePlayscripts/
├── playscripts/
│   ├── akaneiro-ni-somaru-saka/
│   │   ├── package.json      # Visual novel package metadata & VNDB links
│   │   └── script.json       # Structured playscript (metadata, scenes, classified lines)
│   ├── cross-channel/
│   │   ├── package.json
│   │   └── script.json
│   ├── ever17/
│   │   ├── package.json
│   │   └── script.json
│   └── ... (41 visual novels)
├── LICENSE                   # MIT License
└── README.md
```

### `package.json`
Provides package identification, title aliases, and external database references:
```json
{
  "name": "ever17",
  "fullName": "Ever17 -the out of infinity-",
  "version": "1.0.0",
  "description": "Ever17 -the out of infinity- playscript dataset",
  "vndbId": "v17",
  "vndbLink": "https://vndb.org/v17",
  "license": "MIT"
}
```

---

## Schema Specification

### Top-Level `script.json` Structure

| Field | Type | Description |
|---|---|---|
| `metadata` | `object` | General script metadata, language, character list, and summary statistics |
| `scenes` | `array` | Chronological list of narrative scenes |

### Metadata Schema (`metadata`)

| Field | Type | Description |
|---|---|---|
| `title` | `string` | Primary visual novel title |
| `language` | `string` | Script language code (e.g. `"zh-CN"`, `"ja-JP"`) |
| `source_url` | `string` | Origin repository or corpus identifier |
| `scene_count` | `integer` | Total number of scenes in this playscript |
| `line_count` | `integer` | Total number of parsed lines across all scenes |
| `character_list` | `string[]` | Complete roster of unique speaking characters |
| `stats` | `object` | Counts of line discourse types (`dialogue`, `monologue`, `narration`, `choice`, `transition`) |

### Scene Schema (`scenes[]`)

| Field | Type | Description |
|---|---|---|
| `scene_id` | `string` | Unique identifier for the scene (e.g. `"scene_001"`) |
| `title` | `string` | Scene title, chapter name, or setting identifier |
| `setting_time` | `string | null` | In-game time / period if recorded (e.g. `"昼"`, `"放課後"`, `"夜"`) |
| `order` | `integer` | 1-based sequential scene order |
| `lines` | `array` | Ordered list of atomic discourse lines within this scene |

### Line Schema (`lines[]`)

| Field | Type | Description |
|---|---|---|
| `index` | `integer` | 1-based index within the enclosing scene |
| `global_index` | `integer` | 1-based sequential index across the entire visual novel |
| `type` | `string` | Discourse type: `"dialogue"` | `"monologue"` | `"narration"` | `"choice"` | `"transition"` |
| `speaker` | `string | null` | Character name speaking the line; `null` for narration, choices, and transitions |
| `text` | `string` | Normalized, cleaned speech or narrative prose text |
| `raw` | `string` | Verbatim text as originally extracted from the raw engine dump |

---

## Discourse Taxonomy

| Discourse Type | Typical Speaker | Description & Usage |
|:---:|:---:|---|
| **`dialogue`** | Named character / NPC | Direct spoken utterance between characters. Speech turn consolidation combines consecutive multiline phrases spoken by the same character. |
| **`monologue`** | `null` or Protagonist | Internal soliloquy or unspoken thoughts, detected via Japanese brackets `（...）`, `「...」` in 1st-person point of view without external speaker designation. |
| **`narration`** | `null` | Descriptive scene exposition, sensory ambiance, physical actions, and environmental background text. |
| **`choice`** | `null` | Interactive branch decision options presented to the player during game progression. |
| **`transition`** | `null` | Structural delimiters, chapter headings, day/time transitions (e.g. *翌日*, *数日後*), or spatial scene cuts. |

---

## Catalog of Playscripts

The dataset covers **41 classic visual novels** from **28 defunct, dissolved, or inactive developer circles and studios**:

| # | Playscript ID | Title | Studio / Circle | VNDB | Scenes | Lines | Cast |
|:---:|---|---|---|:---:|:---:|:---:|:---:|
| 1 | [`11-gatsu-no-arcadia`](playscripts/11-gatsu-no-arcadia) | 11gatsu no Arcadia<br><sub>*11月のアルカディア*</sub> | **3rdEye** | [v16342](https://vndb.org/v16342) | 1 | 83,338 | 83 |
| 2 | [`2nd-love`](playscripts/2nd-love) | 2nd LOVE ~Chotto Kiken na Koi no Lesson~<br><sub>*2nd LOVE*</sub> | **チュアブルソフト (Chuablesoft)** | [v4373](https://vndb.org/v4373) | 1 | 7,049 | 2 |
| 3 | [`akaneiro-ni-somaru-saka`](playscripts/akaneiro-ni-somaru-saka) | Akaneiro ni Somaru Saka<br><sub>*あかね色に染まる坂*</sub> | **feng** | [v547](https://vndb.org/v547) | 120 | 74,188 | 132 |
| 4 | [`ambitious-mission`](playscripts/ambitious-mission) | AMBITIOUS MISSION | **SAGA PLANETS** | [v33036](https://vndb.org/v33036) | 1 | 46,641 | 438 |
| 5 | [`asairo`](playscripts/asairo) | Kitto, Sumiwataru Asairo Yori mo,<br><sub>*きっと、澄みわたる朝色よりも、*</sub> | **propeller** | [v1483](https://vndb.org/v1483) | 1 | 60,670 | 26 |
| 6 | [`castle-fantasia-erencia`](playscripts/castle-fantasia-erencia) | Castle Fantasia ~Erencia Senki~<br><sub>*キャッスルファンタジア ～エレンシア戦記～*</sub> | **Studio e・go!** | [v1150](https://vndb.org/v1150) | 1 | 23,464 | 0 |
| 7 | [`cross-channel`](playscripts/cross-channel) | CROSS†CHANNEL | **FlyingShine** | [v66](https://vndb.org/v66) | 1 | 41,922 | 36 |
| 8 | [`dasanteki-na-love-comedy`](playscripts/dasanteki-na-love-comedy) | Futari to Hajimeru Dasanteki na Love Come<br><sub>*二人と始める打算的なラブコメ*</sub> | **Campus** | [v22583](https://vndb.org/v22583) | 1 | 10,131 | 3927 |
| 9 | [`dousei-lover-able`](playscripts/dousei-lover-able) | Dousei Lover Able<br><sub>*同棲ラブラブル*</sub> | **SILLY WORKs** | [v7774](https://vndb.org/v7774) | 1 | 26,819 | 121 |
| 10 | [`ever17`](playscripts/ever17) | Ever17 -the out of infinity- | **KID** | [v17](https://vndb.org/v17) | 1 | 25,282 | 19 |
| 11 | [`fossette`](playscripts/fossette) | Fossette ~Cafe au Le Ciel Bleu~<br><sub>*フォセット ～Cafe au Le Ciel Bleu～*</sub> | **戯画 (GIGA)** | [v684](https://vndb.org/v684) | 1 | 3,994 | 1 |
| 12 | [`hare-tokidoki-otenkiame`](playscripts/hare-tokidoki-otenkiame) | Hare Tokidoki Otenkiame<br><sub>*天気雨*</sub> | **Colors** | [v5193](https://vndb.org/v5193) | 1 | 12,241 | 0 |
| 13 | [`haruka-kanata`](playscripts/haruka-kanata) | Haruka Kanata<br><sub>*はるかかなた*</sub> | **PULLTOP** | [v13628](https://vndb.org/v13628) | 1 | 61,055 | 171 |
| 14 | [`harumade-kururu`](playscripts/harumade-kururu) | Harumade, Kururu.<br><sub>*はるまで、くるる。*</sub> | **Clochette (クロシェット)** | [v9198](https://vndb.org/v9198) | 1 | 54,945 | 89 |
| 15 | [`hatsukoi-syndrome`](playscripts/hatsukoi-syndrome) | Hatsukoi * Syndrome<br><sub>*初恋＊シンドローム*</sub> | **Campus** | [v20432](https://vndb.org/v20432) | 1 | 10,302 | 1 |
| 16 | [`izumo2`](playscripts/izumo2) | Izumo 2<br><sub>*IZUMO2*</sub> | **Studio e・go!** | [v619](https://vndb.org/v619) | 1 | 43,489 | 6 |
| 17 | [`kajiri-kamui-kagura`](playscripts/kajiri-kamui-kagura) | Kajiri Kamui Kagura<br><sub>*神咒神威神楽 曙之光*</sub> | **Light** | [v5844](https://vndb.org/v5844) | 1 | 472 | 0 |
| 18 | [`kanojo-to-ore-to-koibito-to`](playscripts/kanojo-to-ore-to-koibito-to) | Kanojo to Ore to Koibito to.<br><sub>*彼女と俺と恋人と。*</sub> | **SMILE（美少女革命）** | [v10972](https://vndb.org/v10972) | 1 | 19,962 | 1 |
| 19 | [`kao-no-nai-tsuki`](playscripts/kao-no-nai-tsuki) | Kao no nai Tsuki<br><sub>*顔のない月*</sub> | **Root（オービット旗下）** | [v240](https://vndb.org/v240) | 343 | 48,677 | 76 |
| 20 | [`kazoku-keikaku`](playscripts/kazoku-keikaku) | Kazoku Keikaku<br><sub>*家族計画*</sub> | **D.O.（ディーオー）** | [v155](https://vndb.org/v155) | 1 | 2,593 | 1 |
| 21 | [`kisaku`](playscripts/kisaku) | Kisaku<br><sub>*鬼作*</sub> | **elf（エルフ）** | [v1131](https://vndb.org/v1131) | 1 | 57,823 | 58 |
| 22 | [`lover-able`](playscripts/lover-able) | Lover Able<br><sub>*ラブラブル～Lover Able～*</sub> | **SILLY WORKs** | [v5734](https://vndb.org/v5734) | 1 | 36,150 | 404 |
| 23 | [`mayoeru-futari`](playscripts/mayoeru-futari) | Mayoeru Futari to Sekai no Subete<br><sub>*迷える2人とセカイのすべて*</sub> | **Lass（ラズエル）** | [v14523](https://vndb.org/v14523) | 265 | 208,759 | 69 |
| 24 | [`men-at-work`](playscripts/men-at-work) | Men at Work! | **Studio e・go!** | [v109](https://vndb.org/v109) | 1 | 12,841 | 78 |
| 25 | [`mugen-kairou`](playscripts/mugen-kairou) | Mugen Kairou<br><sub>*夢幻廻廊*</sub> | **feng** | [v1333](https://vndb.org/v1333) | 32 | 18,943 | 27 |
| 26 | [`newton-to-ringo-no-ki`](playscripts/newton-to-ringo-no-ki) | Newton to Ringo no Ki<br><sub>*ニュートンと林檎の樹*</sub> | **Laplacian（拉普拉斯）** | [v20330](https://vndb.org/v20330) | 1 | 34,165 | 4 |
| 27 | [`onigokko`](playscripts/onigokko) | Onigokko!<br><sub>*鬼ごっこ！*</sub> | **ALcot** | [v5549](https://vndb.org/v5549) | 1 | 53,614 | 96 |
| 28 | [`real-imouto-ooizumi`](playscripts/real-imouto-ooizumi) | Real Imouto ga Iru Ooizumi-kun no Baai<br><sub>*リアル妹がいる大泉くんのばあい*</sub> | **ALcot** | [v3746](https://vndb.org/v3746) | 85 | 7,256 | 0 |
| 29 | [`reminiscence`](playscripts/reminiscence) | Reminiscence Re:Collect<br><sub>*レミニセンス Re：Collect*</sub> | **てぃ～ぐる (T-Gle)** | [v7773](https://vndb.org/v7773) | 1 | 44,600 | 223 |
| 30 | [`secret-game-2nd`](playscripts/secret-game-2nd) | Rebellions: Secret Game 2nd Stage<br><sub>*リベリオンズ Secret Game 2nd Stage*</sub> | **FLAT** | [v16862](https://vndb.org/v16862) | 1 | 36,335 | 48 |
| 31 | [`seisai-no-resonance`](playscripts/seisai-no-resonance) | Seisai no Resonance<br><sub>*星彩のレゾナンス*</sub> | **feng** | [v10613](https://vndb.org/v10613) | 403 | 27,682 | 15 |
| 32 | [`seven-days`](playscripts/seven-days) | Seven Days: Anata to Sugosu Nanokakan<br><sub>*セヴンデイズ あなたとすごす七日間*</sub> | **LIFE0** | [v19654](https://vndb.org/v19654) | 1 | 31,492 | 3 |
| 33 | [`shoujo-minority`](playscripts/shoujo-minority) | Shoujo Minority -Nagusame no Ai-<br><sub>*少女マイノリティ -凪咲 Another Day*</sub> | **Wonder fool** | [v19289](https://vndb.org/v19289) | 1 | 703 | 0 |
| 34 | [`soreyori-no-prologue`](playscripts/soreyori-no-prologue) | Soreyori no Prologue<br><sub>*ソレヨリノ前奏詩*</sub> | **minori** | [v15637](https://vndb.org/v15637) | 1 | 28,355 | 1 |
| 35 | [`suiheisen-made-nan-mile`](playscripts/suiheisen-made-nan-mile) | Suiheisen made Nan Mile? - Deep Blue Sky & Pure White Wings<br><sub>*水平線まで何マイル？*</sub> | **ABHAR** | [v972](https://vndb.org/v972) | 1 | 620 | 2 |
| 36 | [`suisou-ginka-no-istoria`](playscripts/suisou-ginka-no-istoria) | Suisou Ginka no Istoria<br><sub>*水葬銀貨のイストリア*</sub> | **ウグイスカグラ (Uguisu Kagura)** | [v20471](https://vndb.org/v20471) | 1 | 60,480 | 393 |
| 37 | [`swan-song`](playscripts/swan-song) | SWAN SONG | **FlyingShine** | [v914](https://vndb.org/v914) | 169 | 13,532 | 1 |
| 38 | [`trinoline`](playscripts/trinoline) | Trinoline<br><sub>*トリノライン*</sub> | **minori** | [v19644](https://vndb.org/v19644) | 1 | 27,960 | 0 |
| 39 | [`tsumi-no-hikari`](playscripts/tsumi-no-hikari) | Tsumi no Hikari Rendezvous<br><sub>*罪ノ光ランデヴー*</sub> | **minori** | [v17872](https://vndb.org/v17872) | 1 | 27,179 | 0 |
| 40 | [`ushinawareta-mirai`](playscripts/ushinawareta-mirai) | Ushinawareta Mirai o Motomete<br><sub>*失われた未来を求めて*</sub> | **TRUMPLE** | [v4880](https://vndb.org/v4880) | 184 | 43,398 | 79 |
| 41 | [`v-g-neo`](playscripts/v-g-neo) | V.G. Neo | **戯画 (GIGA)** | [v3984](https://vndb.org/v3984) | 1 | 24,680 | 6 |

---

## Quickstart & Usage

### Python Quickstart

Load a playscript, extract scenes, and stream dialogue turns:

```python
import json
from pathlib import Path

# Load Ever17 playscript
script_path = Path("playscripts/ever17/script.json")
with open(script_path, "r", encoding="utf-8") as f:
    data = json.load(f)

metadata = data["metadata"]
print(f"Title: {metadata['title']}")
print(f"Total Scenes: {metadata['scene_count']}, Total Lines: {metadata['line_count']}")
print(f"Main Cast: {', '.join(metadata['character_list'][:5])}...")

# Filter character dialogue in Scene 1
first_scene = data["scenes"][0]
for line in first_scene["lines"][:10]:
    if line["type"] == "dialogue":
        print(f"[{line['speaker']}]: {line['text']}")
    elif line["type"] == "monologue":
        print(f"(Thought): {line['text']}")
    else:
        print(f"  * {line['text']}")
```

### TypeScript / Node.js Quickstart

```typescript
import { readFile } from "fs/promises";
import { join } from "path";

interface PlayscriptLine {
  index: number;
  global_index: number;
  type: "dialogue" | "monologue" | "narration" | "choice" | "transition";
  speaker: string | null;
  text: string;
  raw: string;
}

interface PlayscriptScene {
  scene_id: string;
  title: string;
  setting_time: string | null;
  order: number;
  lines: PlayscriptLine[];
}

interface PlayscriptData {
  metadata: {
    title: string;
    language: string;
    scene_count: number;
    line_count: number;
    character_list: string[];
    stats: Record<string, number>;
  };
  scenes: PlayscriptScene[];
}

async function loadScript(playscriptId: string): Promise<PlayscriptData> {
  const filePath = join(__dirname, "playscripts", playscriptId, "script.json");
  const raw = await readFile(filePath, "utf-8");
  return JSON.parse(raw) as PlayscriptData;
}

// Example usage
loadScript("cross-channel").then((script) => {
  console.log(`Loaded ${script.metadata.title}: ${script.metadata.line_count} lines`);
});
```

### Data Sample

Below is an excerpt showing the structured representation of dialogue, monologue, and narration in `script.json`:

```json
{
  "scene_id": "scene_001",
  "title": "Prologue",
  "setting_time": null,
  "order": 1,
  "lines": [
    {
      "index": 1,
      "global_index": 1,
      "type": "narration",
      "speaker": null,
      "text": "澄み渡る青空の下、微風が校庭の桜を揺らしている。",
      "raw": "澄み渡る青空の下、微風が校庭の桜を揺らしている。"
    },
    {
      "index": 2,
      "global_index": 2,
      "type": "monologue",
      "speaker": null,
      "text": "またこの季節がやってきたのか……。",
      "raw": "（またこの季節がやってきたのか……）"
    },
    {
      "index": 3,
      "global_index": 3,
      "type": "dialogue",
      "speaker": "ヒロイン",
      "text": "おはよう！今日もいい天気だね！",
      "raw": "ヒロイン「おはよう！今日もいい天気だね！」"
    }
  ]
}
```

---

## Preprocessing & Normalization

1. **Speech Turn Consolidation**: Consecutive lines spoken by the same character without intervening narration or actions are merged into a single coherent turn, matching natural human conversational flow.
2. **Whitespace & Control Character Stripping**: Engine-specific tags (e.g., voice opcodes, color codes, wait tags `\w`, font formatting) and redundant whitespace are cleaned while retaining raw text in the `raw` attribute.
3. **Discourse Role Disambiguation**: Regular expressions and structural heuristics separate dialogue (`speaker「...」`), internal monologues (`（...）`), narrator prose, choices, and chapter markers.
4. **Character Speaker Normalization**: Speaker labels are trimmed and mapped into the visual novel's `character_list`.

---

## Ethical Considerations & Digital Preservation

- **Defunct Circle Archival**: The visual novels in this repository originate from companies and circles that have formally dissolved, ceased operations, or entered bankruptcy liquidation. These scripts are preserved for scholarly research, linguistic corpus analysis, and machine learning evaluation under fair use principles for out-of-commerce creative works.
- **Copyright & Intellectual Property**: Intellectual property, trademarks, and story rights remain with their respective original creators and historical copyright successors. If you are a copyright holder wishing to request modifications or removal, please open an issue or contact the maintainers.
- **Research Use Only**: This dataset is distributed solely for non-commercial educational, linguistic, and AI evaluation research.

---

## Citation & License

The dataset schema, tooling, and structured annotations are licensed under the **MIT License**.

If you use **GalgamePlayscripts** in your research or conversational AI evaluations, please cite:

```bibtex
@misc{galgameplayscripts2026,
  author = {EmaFanClub},
  title = {GalgamePlayscripts: Structured Visual Novel Playscripts Archive for Conversational AI and Memory Benchmarking},
  year = {2026},
  publisher = {GitHub},
  howpublished = {\url{https://github.com/EmaFanClub/GalgamePlayscripts}}
}
```
