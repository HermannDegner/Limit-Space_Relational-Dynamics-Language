# RDL_構造圧縮プロトコル――構造抽出版

*T2：運用層 / OP / DRAFT v0.1*  
*依存：RDL_Core*  
*派生：RDL_構造圧縮プロトコル*  

---

## ■ 0. 目的

本稿は『RDL_構造圧縮プロトコル』自身を、そのプロトコルに従って構造圧縮した自己適用版である。

```text
Source
  = RDL_構造圧縮プロトコル

Source
  ↓ StructuralCompression_B
IR_protocol
```

目的は、本文の説明・修辞・例示を外しても、プロトコルの中核構造を再構成可能な形で保持できるか検査すること。

---

## ■ 1. B

```text
B_protocol-compress :=
  「RDL_構造圧縮プロトコルを、
    比較・翻訳・再構成・破壊検査へ持ち運べるIRとして抽出する」
```

保存対象：

```text
operation definition
claim strength
IR fields
workflow
prohibitions
ξ placement
break conditions
reconstruction invariants
self-application
```

保存対象外：

```text
文章表面の完全一致
例示の全保持
節順の絶対固定
修辞の完全再現
```

---

## ■ 2. 基本ラベル

```text
D
  = source document

B_compress
  = 構造圧縮の目的境界

IR_B(D)
  = B_compress下で抽出された構造中間表現

R
  = reconstruction operation

D'
  = reconstructed document

INV
  = reconstruction invariant

ξ_source
  = 原文自身が保持する未確定

ξ_compress
  = 圧縮操作が追加する未確定・損失

W_ij
  = 文書・命題間の接続
```

---

## ■ 3. 主命題

### P1：構造圧縮は要約ではない

```text
Summary(D)
≠ StructuralCompression_B(D)
```

```text
Summary
→ reduce reading cost

Structural compression
→ preserve reconstructable relational structure
```

### P2：保存対象は文章一致ではなく構造不変量

```text
D = D'
```

は要求しない。

要求候補：

```text
Invariant_B(D)
≈
Invariant_B'(D')
```

### P3：構造圧縮はB依存

```text
IR(D)
```

ではなく、

```text
IR_B(D)
```

とする。

```text
B_translation
B_compare
B_break
B_version
B_port
```

によって保持項目は変わり得る。

### P4：圧縮は主張強度を変更しない

禁止変換：

```text
candidate → definition
local → universal / absolute
may → must
can → always
analogy → identity
question → claim
hypothesis → conclusion
ξ → erased
```

### P5：圧縮によるξを原文のξと分離する

```text
ξ_source ≠ ξ_compress
```

### P6：圧縮率最小化は目的ではない

```text
CompressionQuality
≠ shortest(IR)
```

品質候補：

```text
structural preservation
+ comparison usefulness
+ reconstruction usefulness
+ ξ visibility
- false strengthening
- notation collision
```

### P7：重要文書は本文と構造抽出版の二重構造を持てる

```text
Document.md
↔
Document_構造抽出版.md
```

```text
Prose Document
≠ Structural IR
```

### P8：構造抽出版は文書群の静的解析に使える

```text
D1 → IR1
D2 → IR2
...

IR1 --W_12--> IR2
```

検査候補：

```text
ξ loss
false strengthening
notation collision
causal over-linking
old claim re-entry
B transition hidden as deduction
```

### P9：構造抽出版は翻訳前の中間表現として使える

```text
Source-language prose
↓
IR
↓ preserve INV / ξ / claim strength
Target-language reconstruction
```

### P10：プロトコル自身を圧縮対象にする

```text
Protocol
↓
Protocol_IR
```

自己適用が破断した場合、プロトコル自身を更新対象とする。

---

## ■ 4. 標準IR

```text
IR_B(D) := {
  B,
  source-role,
  labels,
  propositions,
  relations,
  flow,
  ξ_source,
  ξ_compress,
  break-conditions,
  residual-structure,
  invariants,
  external-connections
}
```

### 必須候補

```text
B
propositions
relations / flow
ξ
break conditions
residual structure
invariants
```

### 文書依存候補

```text
source-role
labels
external-connections
```

---

## ■ 5. 操作フロー

```text
S0  fix source version
↓
S1  extract B
↓
S2  extract structural labels
↓
S3  classify proposition strength
↓
S4  extract relations / flow / W_ij
↓
S5  separate ξ_source / ξ_compress
↓
S6  extract break conditions / residual structure
↓
S7  define reconstruction invariants
↓
S8  normalize notation
↓
S9  false-strengthening audit
↓
S10 reconstruction test
```

---

## ■ 6. 命題強度分類

圧縮時に区別する。

```text
observation
assertion
inference
hypothesis
candidate
definition
analogy
negative claim
```

保存規則：

```text
Type_source(P)
≈ Type_IR(P)
```

圧縮者による種類変更は、原則禁止。

---

## ■ 7. relations 抽出規則

許容例：

```text
P1 → P2
P1 + new B → P2
P2 auxiliary-to P1
A ≠ B
A ↛ B
A ≈ B under B_local
```

禁止例：

```text
adjacency(A,B)
→ causation(A,B)
```

また、

```text
new B branch
```

を、

```text
deduction from previous output
```

へ変えない。

---

## ■ 8. 記号正規化

```text
Common IR priority:
semantic stability > compression ratio
```

RDL既存記号との衝突を避ける。

```text
H
W
B
F
ξ
```

など既存意味が強い記号を、別概念に再利用しない。

必要なら長いラベルを許容する。

```text
God_Bible
Human
CreatorRole
World_local
```

---

## ■ 9. 禁止補完

```text
external knowledge
→ source-derived claim
```

は禁止。

```text
implicit adjacency
→ explicit causality
```

は禁止。

```text
hard-to-compress ξ
→ omission
```

は禁止。

```text
application result
→ Core universal claim
```

は禁止。

外部補足・圧縮者仮説は別ラベルで保持する。

---

## ■ 10. 再構成テスト

```text
D
↓ C_B
IR_B(D)
↓ R_B'
D'
```

比較：

```text
INV(D) vs INV(D')
ξ(D) vs ξ(D')
claim-strength(D) vs claim-strength(D')
relations(D) vs relations(D')
```

目標：

```text
preserve structure
```

非目標：

```text
verbatim reproduction
```

---

## ■ 11. 二重文書構造

```text
Prose
  = human-oriented / context-rich

Structural IR
  = comparison-oriented / reconstruction-oriented
```

```text
Prose ≠ IR
```

同期差分：

```text
ξ_sync := unresolved difference(Document, IR)
```

片方だけを絶対正本としない。

---

## ■ 12. 既存プロトコルとの位置

```text
RDL_中間設計図プロトコル:
intent → structured design

RDL_構造圧縮プロトコル:
existing document → structural IR
```

全体候補：

```text
intent
↓
intermediate design
↓
document
↓
structural IR
↓
comparison / translation / reconstruction
```

---

## ■ 13. 利用先

```text
translation
cross-document comparison
break test
version structural diff
cross-format reconstruction
large-document static analysis
```

---

## ■ 14. ξ

```text
ξ_protocol = {
  invariant selection,
  proposition granularity,
  compression granularity,
  IR equivalence criteria,
  reconstruction quality criteria,
  rhetorical-content preservation,
  ξ_compress measurement,
  human/AI extraction variance,
  large-scale operating cost
}
```

---

## ■ 15. 破断条件

```text
B1:
IR provides no practical gain

B2:
meaning depends irreducibly on surface form

B3:
invariant selection is arbitrarily manipulable

B4:
IR becomes harder to inspect than source

B5:
IR is reified as the true / superior meaning of source
```

---

## ■ 16. 残存構造

```text
surface form ≠ relational structure
summary ≠ structural extraction
candidate ≠ definition
local ≠ absolute
hypothesis ≠ conclusion
ξ_source ≠ ξ_compress
textual identity ≠ invariant preservation
```

最小残存操作：

```text
before transformation:
explicitly state what must be preserved
```

---

## ■ 17. 最小圧縮

```text
D
↓ B_compress

IR_B(D) = {
  B,
  propositions,
  relations,
  ξ_source,
  ξ_compress,
  break conditions,
  residual structure,
  INV
}

↓ audit

no false strengthening
no ξ erasure
no relation invention
no notation collision

↓ reconstruct

D'

Target:
INV(D) ≈ INV(D')
```

---

## ■ 18. 再構成時に保存すべき不変量

```text
INV-1  Structural compression ≠ summarization
INV-2  Compression is B-dependent
INV-3  Preserve claim strength; do not upgrade candidate/hypothesis/local claims
INV-4  ξ_source ≠ ξ_compress
INV-5  Preserve relations without inventing causality
INV-6  IR must record break conditions and residual structure when source supports them
INV-7  Common IR should avoid notation collisions
INV-8  Compression ratio is not the optimization target
INV-9  Reconstruction aims at invariant preservation, not textual identity
INV-10 Prose document ≠ structural IR; both are B-dependent views
INV-11 External supplementation must be separated from source-derived structure
INV-12 Protocol is self-applicable and may fail its own reconstruction test
```

---

## ■ 19. 自己適用判定

本稿は、本文プロトコルから以下を保存している必要がある。

```text
definition
B-dependence
IR schema
workflow
false-strengthening prohibition
ξ separation
notation normalization
reconstruction test
dual-document structure
break conditions
self-application
```

これらが本文に存在し、本稿に残っていれば、自己適用は暫定的に成立する。

ただし、

```text
IR_self-test = pass
```

は絶対保証ではない。
本稿と本文の差分には常に、

```text
ξ_self-compress
```

が残り得る。

---

*v0.1：『RDL_構造圧縮プロトコル』を同プロトコル自身で構造抽出した自己適用版。B、標準IR、主命題、操作フロー、命題強度、relations、記号正規化、禁止補完、再構成テスト、二重文書構造、既存プロトコルとの位置、ξ、破断条件、残存構造、不変量を抽出。*
