# Português (Brasil)

Speak and Sing read Brazilian Portuguese once you choose it: **PT**, next to "Text is IPA" in
Speak and next to the lyrics in Sing. It is one setting for both tabs, kept on the device with
Sing's words. Portuguese is written in Latin letters, so the lyrics router can't tell it from
English or Turkish by script ([language-front-ends.md](language-front-ends.md)); English stays the
default until PT is chosen. Toned pinyin, Hangul, Cyrillic and Han characters in the same lyrics
still go to their own front ends.

`crates/chimera/src/phonetics/portuguese.rs` reads the spelling by rule, with no dictionary, onto
the existing inventory (**no new sounds** — see [O inventário](#o-inventário-measure-2026-11-25)).

> **Status: the front end is here** — the rules below are what it does, and the tests in
> `crates/chimera/tests/portuguese.rs` are what hold it to them. A native speaker's check is still
> wanted on the vowel quality and on the stress of the endings: they are the parts that most need
> an ear.

## Writing it

- Type it as you'd write it: `á à â ã é ê í ó ô õ ú ç`. The accents are **meaningful** — they mark
  the stress and the vowel quality, and without them the reader can't know where the word falls.
- An apostrophe keeps a name apart from its suffixes (`D'Ávila`, `d'água`).
- In Sing, a vowel is a note: words split where Portuguese spelling splits them (`a-mi-go`,
  `ca-sa`). `-` alone holds the previous vowel onto the next note, and hyphens split a word your
  way.
- Numbers are read out in Speak ("1.923,5" → "mil novecentos e vinte e três vírgula cinco").

## What it does

| | |
|---|---|
| v | [β] between vowels, [v] elsewhere: você [voˈse], livro [ˈlivɾu] |
| r at the start of a word, after n or l, doubled, before a consonant or at the end of a word | the uvular fricative [ʁ]: real [ʁeˈaw], obrigado [obɾiˈɡadu], Português [poʁtuˈɡeʃ] |
| r in the onset before a vowel | the tap [ɾ]: carioca [kaɾiˈoka], braço [ˈbɾasɨu] |
| lh | [ʎ]: filho [ˈfiʎu] |
| nh | [ɲ]: unha [ˈuɲɐ] |
| ll | [ʎ], like lh (the usual Brazilian reading) |
| ti, di | the palatal affricates [tʃ], [dʒ]: tia [ˈtʃiɐ], dia [ˈdʒiɐ] |
| x | [ʃ]: chave [ˈʃavi] |
| ç | [s]: braço [ˈbɾasɨu] |
| h | silent: homem [oˈmẽj̃] |
| gu before e or i | [ɡ], the u silent: guerra [ˈɡɛʁɐ] |
| qu | [k]: quinze [ˈkĩzɪ] |
| ão, õe, ãi, õi | the nasal diphthongs, one nucleus: pão [pɐ̃w̃], mãe [ˈmɐ̃j̃], coração [koɾɐˈsɐ̃w̃] |
| s between vowels, and at the end of a word | [z]: casa [ˈkazɐ], livros [ˈlivɾuz] |
| s before a consonant | [s]: sapato [saˈpatu] |
| a final s after a front vowel | [s]: três [tɾɛs] |
| nasals before a consonant | the nasal is a coda and the consonant moves to the next syllable: bom dia [bõŋˈdʒiɐ] |

### The vowels

Brazilian Portuguese has seven pure vowels and three nasal ones:

| spelling | sound | in |
|---|---|---|
| a | [a] open, [ɐ] closed | open: lá [ˈla]; closed: casa [ˈkazɐ] (unstressed before a nasal or at the end) |
| e | [e] open, [ɛ] closed | mesa [ˈmezɐ], porta [ˈpɔʁtɐ] |
| i | [i] | |
| o | [o] open, [ɔ] closed | porta [ˈpɔʁtɐ] |
| u | [u], and [w] as a glide | água [ˈaɡwa] |
| ã | [ɐ̃] | pão, irmã |
| õ | [õ] | então, coração |

The **closed** vowels (ɐ ɛ ɔ) are the unstressed ones in a closed syllable (`mesa`), and the
word-final ones (`casa`); a stressed vowel stays open (`mar` [maʁ], `café` [kaˈfe]). An
unstressed final /o/ rises to [u] (`copo` [ˈkɔpu], `sapato` [saˈpatu]).

**Nasal vowels need no new phoneme.** The engine already nasalises a vowel with a combining
tilde (`ipa.rs`): `a` + U+0303 is [ɐ̃]. The rule is the whole change. A vowel before a nasal in
its coda is nasalised the same way (`homem` [oˈmẽj̃], `quinze` [ˈkĩzɪ]).

### The stress

Portuguese stress is written, which makes this front end's job easier than English's and harder
than Turkish's: the accent tells the reader where the stress is, and the reader's job is to
respect it and to know the defaults when the accent is absent.

1. **A written accent wins.** `café` → [kaˈfe]; `coração` → [koɾɐˈsɐ̃w̃]; `único` → [ˈuniku].
2. **A word ending in a nasal vowel falls on it.** `irmão` → [iʁˈmɐ̃w̃]; `manhã` → [mɐ̃ˈɲɐ̃];
   `coração` → [koɾɐˈsɐ̃w̃]. A circumflex before that nasal marks the nasalisation and not the
   stress (`bênção` → [bẽˈsɐ̃w̃]).
3. **No accent: stress the penult.** `casa` → [ˈkazɐ]; `Português` → [poʁtuˈɡeʃ]; `livros` →
   [ˈlivɾuz].
4. **The ending -mente keeps the stress of the stem and adds its own**: `rapidamente` →
   [ʁapiˈdamẽtʃi].
5. **The verb endings carry their own stress**: -ei, -ou (falei [faˈlei], falou [faˈlou]); the
   future -ia, -iam (falaria [faˈlaɾiɐ], falariam [faˈlaɾiɐ̃m]); and a word ending in a final r
   (falar [faˈlaʁ], andar [ɐ̃ˈdaʁ]). A participle (-ado, -ido) takes the penult like any other
   word (`falado` → [faˈladu], `obrigado` → [obɾiˈɡadu]).
6. **Monosyllables** take their default: `cor` [kɔʁ], `pôr` [poʁ], `saú` [saˈu].

### The syllables

- CV and CVC, with a single consonant between vowels going to the next syllable's onset:
  `a-mi-go` [aˈmiɡu], `ca-sa` [ˈkazɐ].
- **The onset is the longest one Portuguese allows**, and the rest of a cluster closes the
  syllable: `Português` is por-tu-guês (no "rt" onset), `admissão` is ad-mi-são.
- **A nasal or liquid before another consonant is a coda**: `bom dia` [bõŋˈdʒiɐ].
- **The nasal diphthong is one nucleus**: `pão` [pɐ̃w̃] is one note, not two.

## The inventory (measured 2026-11-25)

The engine has **every sound Brazilian Portuguese needs** — the front end is rules, not engine
work:

| needed | in the engine |
|---|---|
| [ʁ] [ʀ] (the r of *Português*) | ✓ `inventory.rs` |
| [ʎ] (lh), [ɲ] (nh) | ✓ `inventory.rs` |
| [β] (v), [ɾ] [ɽ] (the tapping r) | ✓ `inventory.rs` |
| [tʃ] [dʒ] (ti, di) | ✓ `inventory.rs` |
| [ɐ] [ɛ] [ɔ] (the closed vowels) | ✓ the vowel set |
| nasal vowels | ✓ the combining tilde (U+0303) — a rule, not a new phoneme |

**Nothing needs to be added to the engine.** This is why the Portuguese front end is the same
kind of contribution as the Spanish one (#81) and the Polish one (#139), and not like the Korean
or Mandarin work, which needed new sounds.

## What it touches

| file | what |
|---|---|
| `crates/chimera/src/phonetics/portuguese.rs` | **new** — the front end: spelling → segments, stress, syllables (the model is `turkish.rs`) |
| `crates/chimera/src/phonetics/mod.rs` | `pub mod portuguese;` |
| `crates/chimera/src/phonetics/syllables.rs` | `Lang::Pt` in the enum; the `Lang::Pt` arm in `lyrics_in` (Portuguese in place of English and Turkish for Latin script) |
| `crates/chimera-cli/src/main.rs` | `Some("pt") => Lang::Pt` in the `syllables` command, and the `render portuguese` command |
| `crates/chimera-cli/src/render.rs` | the `portuguese` command (the Turkish one is the model) |
| `crates/chimera-wasm/src/api.rs` | `ptPhrases` |
| `src/wasm/engine.ts` | `portuguesePhrases(text)` |
| `src/wasm/types.ts` | `export type Lang = "en" | "tr" | "pt";` |
| `src/ui/components/LangPick.tsx` | the picker entry `{ value: "pt", label: "PT", name: "Portuguese" }` |
| `src/ui/speech.ts`, `src/ui/singing.ts` | the Portuguese branch (`portuguesePhrases(text)`), next to the Turkish one |
| `crates/chimera/tests/portuguese.rs` | **new** — the tests (the model is `tests/turkish.rs`) |
| `docs/portuguese.md` | this file |

**English must not change.** The English path is the default, and `Lang::En` must keep its
current output byte for byte — the same promise the Turkish front end made. The whole suite
(176 tests) passes with the Portuguese front end in.

## The tests

`crates/chimera/tests/portuguese.rs` holds the front end to its rules:

| what | example |
|---|---|
| the nasal diphthong | `pão` → [pɐ̃w̃] |
| the r of *Português* | `Português` → [poʁtuˈɡeʃ] |
| the tapping r | `carioca` → [kaɾiˈoka] |
| the closed vowels | `casa` → [ˈkazɐ] |
| ti/di | `tia` → [ˈtʃiɐ] |
| lh and nh | `filho` → [ˈfiʎu], `unha` → [ˈuɲɐ] |
| x and ç | `chave` → [ˈʃavi], `braço` → [ˈbɾasɨu] |
| gu before e/i | `guerra` → [ˈɡɛʁɐ] |
| the stress rules | `café` → [kaˈfe], `casa` → [ˈkazɐ], `sapato` → [saˈpatu], `rapidamente` → [ʁapiˈdamẽtʃi] |
| the numbers | `1.923,5` → "mil novecentos e vinte e três vírgula cinco" |
| Latin script is Portuguese only when chosen | (the test `turkish.rs` has for Turkish) |
| every sound is in the inventory | the front end never asks for a sound the engine doesn't have |
| English is untouched | the English tests still pass, byte for byte |

## The check that is still wanted

The **vowel quality** and the **stress of the endings** are the parts that most need an ear: the
difference between *café* and *café* is not visible in the code, it is audible in the voice. A
native speaker's check of the closed-vowel rule — and of where the stress falls in the less
common endings — is what this front end is missing before it can sing a Brazilian lyric with pride.

Two known approximations, both left for that ear: a word-final nasal is [m] or [n] and not the
[j̃] most speakers use (`homem` [ˈomẽm]), and an intervocalic r is the tap [ɾ] (Rio) and not the
uvular [ʁ] (São Paulo).
