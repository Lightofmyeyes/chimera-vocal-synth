# Português (Brasil)

Speak and Sing read Brazilian Portuguese once you choose it: **PT**, next to "Text is IPA" in
Speak and next to the lyrics in Sing. It is one setting for both tabs, kept on the device with
Sing's words. Portuguese is written in Latin letters, so the lyrics router can't tell it from
English or Turkish by script ([language-front-ends.md](language-front-ends.md)); English stays the
default until PT is chosen. Toned pinyin, Hangul, Cyrillic and Han characters in the same lyrics
still go to their own front ends.

`crates/chimera/src/phonetics/portuguese.rs` reads the spelling by rule, with no dictionary, onto
the existing inventory (**no new sounds** — see [O inventário](#o-inventário-measure-2026-11-25)).

> **Status: this is the recipe, not yet the code.** The rules below are the specification the
> implementation must satisfy; the tests listed at the end are the ones that prove it. A native
> speaker's check is still wanted on the stress rules — they are the part that most needs an ear.

## Writing it

- Type it as you'd write it: `á à â ã é ê í ó ô õ ú ç`. The accents are **meaningful** — they mark
  the stress and the vowel quality, and without them the reader can't know where the word falls.
- An apostrophe keeps a name apart from its suffixes (`D'Ávila`, `d'água`).
- In Sing, a vowel is a note: words split where Portuguese spelling splits them (`a-mo-rar`,
  `ca-sa`). `-` alone holds the previous vowel onto the next note, and hyphens split a word your
  way.
- Numbers are read out in Speak ("1.923,5" → "mil novecentos e vinte e três vírgula cinco").

## What it does

| | |
|---|---|
| v | [β] between vowels or after a vowel: você [βuˈsɛ], livro [ˈlivʁu] |
| r at the start of a word, after n or l, doubled, or at the end before a pause | the uvular/velar fricative [ʁ] or [ʀ]: real [ʁeˈaw], obrigado [obɾiˈɡaðu], Portuguese [poɾˈtuɡeʃ] |
| r between vowels | the tap [ɾ]: carioca [kaɾiˈoka] |
| lh | [ʎ]: filho [ˈʎiʎu] |
| nh | [ɲ]: unha [ˈuɲa] |
| ll, y | [ʝ] (the usual Brazilian reading; São Paulo's [ʒ] and Rio's [ʤ] are dialectal): yelled [ˈʝeʎidʒi] |
| ti, di | the palatal affricates [tʃ], [dʒ]: tia [ˈtʃia], dia [ˈdʒiɐ] |
| x | [ʃ]: chave [ˈʃavi] |
| ç | [s]: braço [ˈbɾasɨu] |
| h | silent: homem [oˈmẽŋ] |
| gu before e or i | [ɡ]: guerra [ˈɡɛʁa] |
| qu | [k]: quinze [ˈkinʒi] |
| ão, õe, ãi, õi, ûe, ûi | the nasal diphthongs [ɐ̃w̃], [õj̃], [ɐ̃j̃]: pão [pɐ̃w̃], mãe [ˈmɐ̃j̃], coração [koɾɐˈsɐ̃w̃] |
| final s | [s] (kept; the [ʃ] of São Paulo is dialectal and is not forced) |
| nasals before a consonant | the nasal is a coda and the consonant moves to the next syllable: bom dia [bõŋˈdʒiɐ] |

### The vowels

Brazilian Portuguese has seven pure vowels and three nasal ones:

| spelling | sound | in |
|---|---|---|
| a | [a] open, [ɐ] closed | open: lá [ˈla]; closed: casa [ˈkazɐ] (unstressed before a nasal or at the end) |
| e | [e] open, [ɛ] closed | mesa [ˈmezɐ], mesa [ˈmɛzɐ] |
| i | [i], [ɪ] unstressed | |
| o | [o] open, [ɔ] closed | |
| u | [u], [ʊ] unstressed | |
| ã | [ɐ̃] | pão, irmã |
| õ | [õ] | Então, coração |
| ã / õ / ê as diphthong nuclei | [ɐ̃], [õ], [ẽ] | mãe, coração, pão |

The **closed** vowels (ɐ ɛ ɔ) are the unstressed ones before a nasal or at the end of a word
(`casa`, `mesa`, `roxo`); the **open** ones are stressed or followed by another vowel (`lá`,
`mãe`). The distinction is audible and it is the difference between a Brazilian voice and a
Portuguese-from-Portugal one — so the front end should keep it.

**Nasal vowels need no new phoneme.** The engine already nasalises a vowel with a combining
tilde (`ipa.rs`): `a` + U+0303 is [ɐ̃]. The rule is the whole change.

### The stress

Portuguese stress is written, which makes this front end's job easier than English's and harder
than Turkish's: the accent tells the reader where the stress is, and the reader's job is to
respect it and to know the defaults when the accent is absent.

1. **A written accent wins.** `café` → [kaˈfe]; `coração` → [koɾɐˈsɐ̃w̃]; `único` → [ˈuniku].
2. **No accent: stress the penult** when the word ends in a vowel, a nasal vowel, -n or -s:
   `casa` → [ˈkazɐ], `pão` → [ˈpɐ̃w̃], `homem` → [oˈmẽŋ], `livros` → [ˈlivɾuʃ].
3. **No accent and a closed final syllable: stress the last**: `sapato` → [saˈpatu].
4. **The ending -mente keeps the stress of the stem and adds its own**: `rapidamente` →
   [ʁapiˈdamenti]; **-idade, -ário** are antepenultimate: `universidade` → [univeɾsiˈdaʓi].
5. **The verb endings carry their own stress**: -ei, -ou (falei [faˈlei], falou [faˈlou]);
   the preterite -á, -é, -í, -ó; the imperfect -ia, -iam (falava [faˈlavɐ], falariam
   [faˈlaɾiɐ̃]); the past participle -ado, -ido (falado [faˈladu]); -ão (falaram [faˈlaɾɐ̃ŋ]).
   The exceptions are the oxytone -ão words (`irmão` [iɾˈmɐ̃w̃], `mão` [ˈmɐ̃w̃], `órgão` [ˈoɾɡɐ̃w̃]).
6. **Monosyllables** take their default: `cor` [koɾ], `pôr` [poɾ], `saú` [saˈu].

### The syllables

- CV and CVC, with a single consonant between vowels going to the next syllable's onset:
  `a-mi-go` [aˈmiɡu], `ca-sa` [ˈkazɐ].
- **A nasal or liquid before another consonant is a coda**: `ad-mis-são` [admiˈsɐ̃w̃],
  `bom dia` [bõŋˈdʒiɐ].
- **A vowel-initial syllable takes the glides**: `pai` [paj], `pó` [po], `eu` [ˈew̃].
- The syllabic liquids in `-mente` and `-ado` behave like vowels: `a-mo` [aˈmu].

## The inventory (measured 2026-11-25)

The engine has **every sound Brazilian Portuguese needs** — the front end is rules, not engine
work:

| needed | in the engine |
|---|---|
| [ʁ] [ʀ] (the r of *Português*) | ✓ `inventory.rs` |
| [ʎ] (lh), [ɲ] (nh), [ʝ] (ll/y) | ✓ `inventory.rs` |
| [β] (v), [ɾ] [ɽ] (the tapping r) | ✓ `inventory.rs` |
| [tʃ] [dʒ] (ti, di) | ✓ `inventory.rs` |
| [ɐ] [ɛ] [ɔ] (the closed vowels) | ✓ the vowel set |
| nasal vowels | ✓ the combining tilde (U+0303) — a rule, not a new phoneme |

**Nothing needs to be added to the engine.** This is why the Portuguese front end is the same
kind of contribution as the Spanish one (#81) and the Polish one (#139), and not like the Korean
or Mandarin work, which needed new sounds.

## What to touch

| file | what |
|---|---|
| `crates/chimera/src/phonetics/portuguese.rs` | **new** — the front end: spelling → segments, stress, syllables (the model is `turkish.rs`) |
| `crates/chimera/src/phonetics/mod.rs` | `pub mod portuguese;` |
| `crates/chimera/src/phonetics/syllables.rs` | `Lang::Pt` in the enum; the `Lang::Pt` arm in `lyrics_in` (Portuguese in place of English and Turkish for Latin script) |
| `crates/chimera-cli/src/main.rs` | `Some("pt") => Lang::Pt` in the `syllables` command, and the error message that lists the front ends |
| `src/wasm/types.ts` | `export type Lang = "en" | "tr" | "pt";` |
| `src/ui/components/LangPick.tsx` | the picker entry `{ value: "pt", label: "PT", name: "Portuguese" }` |
| `src/ui/speech.ts`, `src/ui/singing.ts` | the Portuguese branch (`portuguesePhrases(text)`), next to the Turkish one |
| `crates/chimera/tests/portuguese.rs` | **new** — the tests (the model is `tests/turkish.rs`) |
| `docs/portuguese.md` | this file |

**English must not change.** The English path is the default, and `Lang::En` must keep its
current output byte for byte — the same promise the Turkish front end made.

## The tests to write

| what | example |
|---|---|
| the nasal diphthong | `pão` → [pɐ̃w̃] |
| the r of *Português* | `Português` → [poɾˈtuɡeʃ] |
| the tapping r | `carioca` → [kaɾiˈoka] |
| the closed vowels | `casa` → [ˈkazɐ] |
| ti/di | `tia` → [ˈtʃia] |
| lh and nh | `filho` → [ˈʎiʎu], `unha` → [ˈuɲa] |
| x and ç | `chave` → [ˈʃavi], `braço` → [ˈbɾasɨu] |
| gu before e/i | `guerra` → [ˈɡɛʁa] |
| the stress rules | `café` → [kaˈfe], `casa` → [ˈkazɐ], `sapato` → [saˈpatu], `rapidamente` → [ʁapiˈdamenti] |
| Latin script is Portuguese only when chosen | (the test `turkish.rs` has for Turkish) |
| English is untouched | the English tests still pass, byte for byte |

## The check that is still wanted

The **stress rules** are the part that most needs an ear: the difference between *café* and
*café* is not visible in the code, it is audible in the voice. A native speaker's check of the
stress table — and of the closed-vowel rule — is what this front end is missing before it can
sing a Brazilian lyric with pride.
