# RDL_構造圧縮プロトコル

*T2：運用層 / OP / DRAFT v0.1*  
*依存：RDL_Core*  
*対：RDL_構造圧縮プロトコル_構造抽出版*  

---

## ■ 0. 問い

文書は、文章として読むには豊かであるほどよい場合がある。

しかし、文書同士を比較したいとき、翻訳したいとき、別形式へ移植したいとき、あるいは破壊検査をかけたいときには、その豊かさがかえって構造を見えにくくすることがある。

たとえば一つの文書には、

- 主張
- 前提
- 例示
- 修辞
- 比喩
- 用語定義
- 因果関係
- 文書間参照
- 未確定部分
- 破断条件

が同時に含まれる。

これらを通常の要約で短くすると、重要な関係まで消えることがある。
逆に、原文をそのまま保持すると、複数文書を機械的・比較的に扱いにくい。

そこで本稿では、文書を「短くする」のではなく、

> **再構成可能性を残したまま、表層表現を落とし、関係構造を中間表現へ移す操作**

を **構造圧縮** と呼ぶ。

```text
Source Document
      ↓ structural compression
Structural IR
      ↓ comparison / test / translation / reconstruction
Reconstructed Document
```

本プロトコル自身もこの操作の対象である。
したがって本稿には、対となる構造抽出版を持たせる。

---

## ■ 1. 最短定義

```text
StructuralCompression_B(D)
:=
  D から、Bにおける再構成・比較に必要な
  関係構造を抽出して保持する操作
```

ここで、

```text
D = source document
B = compression purpose / observation boundary
IR_B(D) = structural intermediate representation
```

とする。

構造圧縮の目的は、原文と同じ文章を作ることではない。

```text
D = Reconstruct(IR_B(D))
```

を要求しない。

要求するのは、少なくとも、

```text
Invariant_B(D)
≈
Invariant_B(Reconstruct(IR_B(D)))
```

である。

つまり保存対象は、文章表面ではなく、Bにおいて重要とされた構造的不変量である。

---

## ■ 2. 要約との違い

要約と構造圧縮は似て見えるが、目的が異なる。

```text
要約
= 読解コストを下げるために情報量を減らす

構造圧縮
= 再構成・比較・検査に必要な関係を保存しながら
  表層表現を減らす
```

要約では、例示を一つにまとめたり、似た段落を統合したりしてよい。
構造圧縮でもそれは可能だが、次のような変換は避ける。

```text
候補 → 定義
局所命題 → 普遍命題
並置 → 因果
仮説 → 結論
未確定 ξ → 確定値
複数モデル → 一モデル
```

したがって、構造圧縮は情報削減ではあるが、**主張強度の変更を許可する操作ではない**。

---

## ■ 3. 構造圧縮の入力 B

構造圧縮は無目的には行わない。
同じ文書でも、何のために圧縮するかによって保存すべきものが変わる。

例：

```text
B_translation
  = 他言語へ再構成するための構造抽出

B_compare
  = 複数文書を比較するための構造抽出

B_break
  = 破断点を検査するための構造抽出

B_version
  = 版間差分を比較するための構造抽出

B_port
  = 別媒体・別分野へ移植するための構造抽出
```

したがって、最初に、

```text
B_compress := 「何を失ってはいけないか」
```

を明示する。

Bが不明確なまま圧縮すると、何を残すべきかを後から判断できず、単なる短縮へ崩れやすい。

---

## ■ 4. 出力IRの最小構造

標準IRは、少なくとも次を保持する。

```text
IR_B(D) := {
  B,
  source-role,
  labels,
  propositions,
  relations,
  flow,
  ξ,
  break-conditions,
  residual-structure,
  invariants,
  external-connections
}
```

各項目は次を意味する。

### 4.1 B

この文書が何を検査・説明しているか。
何を入力条件にせず、何を出力としているか。

### 4.2 source-role

シリーズ・体系内での役割。

```text
observation
破壊検査
reconstruction
application
branch
adapter
meta
...
```

など。

### 4.3 labels

文書内で比較的高耐久に使われている主要ラベル。
略号を使う場合は、既存RDL記号との衝突を避ける。

### 4.4 propositions

文書の主要命題。
可能なら一命題一構造に分ける。

### 4.5 relations

主要ラベル間の関係。

```text
A → B
A ↛ B
A ≠ B
A ≈ B under B_local
A ⊂ B
A depends-on B
```

など。

### 4.6 flow

文書の推論・観測・変換の主線。

```text
Input
↓
Operation
↓
Intermediate result
↓
Output
```

として抽出する。

### 4.7 ξ

文書が未確定として残しているもの。
また、圧縮操作そのものによって新しく生じた未確定も分けて記録する。

```text
ξ_source
  = 原文自身が保持している未確定

ξ_compress
  = 圧縮によって生じた意味損失・判定不能
```

### 4.8 break-conditions

どの条件で文書の読み・仮設・モデルが破断するか。

### 4.9 residual-structure

個別命題が破断しても残る構造。

### 4.10 invariants

翻訳・再記述・再構成の際に保存すべき命題。

### 4.11 external-connections

他文書から何を受け取り、何を渡すか。

```text
D_i --W_ij--> D_j
```

を可能な範囲で保持する。

---

## ■ 5. 圧縮手順

### Step 0：原文固定

まず対象となる版を固定する。

```text
Source := D@version
```

途中で原文を更新した場合、IRの対応版も引き直す。

### Step 1：Bを抽出する

次を確認する。

```text
この文書は何を問うているか
何を先に真と置いていないか
何を観測・比較対象としているか
何を出力したいか
```

Bが複数ある場合は分離する。

### Step 2：主要ラベルを抽出する

単なる頻出語ではなく、関係構造の節点になるラベルを抽出する。

```text
labels := nodes required to reconstruct relations
```

### Step 3：命題強度を保存したまま命題化する

原文の文を、可能なら次に分ける。

```text
assertion
hypothesis
candidate
observation
inference
analogy
definition
negative claim
```

ここで最重要なのは、**種類を勝手に昇格させないこと**である。

```text
candidate ≠ definition
analogy ≠ identity
correlation ≠ causation
observation ≠ mechanism
```

### Step 4：W_ij と主線を抽出する

命題同士の接続を抽出する。

```text
P1 → P2
P2 + new B → P3
P4 is auxiliary to P2
P5 does not follow from P4
```

特に、文書中で隣り合っているだけの節を因果関係として圧縮しない。

### Step 5：ξを抽出する

原文が明示した ξ と、圧縮者が判断できない部分を分ける。

```text
ξ_source ≠ ξ_compress
```

圧縮者が理解できないからといって、原文の主張を削除してよいとは限らない。

### Step 6：破断条件と残存構造を抽出する

文書が自分の有効域を明示している場合、それをそのまま保持する。
明示していない場合、圧縮者が勝手に新しい破断条件を本文由来として追加しない。
必要なら、

```text
Break_candidate_by_compressor
```

として別枠にする。

### Step 7：不変量を設定する

再構成時に消えてはいけない構造を列挙する。

```text
INV-1 ...
INV-2 ...
...
```

数は固定しない。
重要なのは、再構成後に比較可能な形であること。

### Step 8：記号を正規化する

文書単体では問題のない略号も、共通IRでは衝突する場合がある。

例：

```text
H = Human
```

は、RDLで `H = heat` と衝突する。

したがって共通IRでは、圧縮率より記号安定性を優先する。

```text
Human
God_Bible
God_RDL
CreatorRole
World_local
```

のように、意味が明示されたラベルを使ってよい。

### Step 9：誤強化検査

圧縮前後を比較し、次が起きていないか検査する。

```text
candidate → definition ?
local → absolute ?
may → must ?
can → always ?
analogy → identity ?
question → claim ?
ξ → erased ?
```

一つでも起きていれば、IRを戻す。

### Step 10：再構成テスト

IRだけを見て、別の文体・言語で再構成してみる。

```text
D
↓ C_B
IR_B(D)
↓ R_B'
D'
```

その後、

```text
Invariant_B(D)
vs
Invariant_B'(D')
```

を比較する。

完全一致を要求しない。
不変量の消失・反転・強化を検査する。

---

## ■ 6. 構造圧縮で禁止する補完

構造圧縮は、原文にない穴を埋める操作ではない。

### 6.1 原文外の根拠追加

```text
Source does not support P
↓
compressor adds external knowledge
↓
P appears source-derived
```

は禁止する。

外部知識を使う場合は、

```text
External supplement
```

として分離する。

### 6.2 暗黙因果の確定

```text
A is followed by B
```

から、

```text
A causes B
```

へ移さない。

### 6.3 ξの自動消去

説明しにくい部分を落とすことで見かけ上の整合性を上げない。

### 6.4 ラベルの過剰統合

似た語を一つにまとめた結果、原文の区別が消える場合は統合しない。

### 6.5 理論への逆輸入

応用文書から抽出した構造を、追加検査なしにRDL Coreの一般命題へ昇格させない。

---

## ■ 7. 圧縮率は目的ではない

構造圧縮の品質を、文字数の減少率だけで評価しない。

```text
CompressionQuality
≠ shortest(IR)
```

むしろ、

```text
CompressionQuality
≈
  structural preservation
  + comparison usefulness
  + reconstruction usefulness
  + ξ visibility
  - false strengthening
  - notation collision
```

と考える。

必要なら原文より長いIRがあってもよい。
特に、暗黙だった関係を明示化すると文字数が増えることがある。

---

## ■ 8. 文書二重構造

本プロトコルでは、重要文書について次の二重構造を推奨する。

```text
Document.md
Document_構造抽出版.md
```

### 本文書

人間が読むための説明・例示・修辞・文脈を保持する。

### 構造抽出版

比較・翻訳・破壊検査・移植に使う中間表現を保持する。

二者は上下関係ではない。

```text
Prose Document
≠ Structural IR
```

それぞれ別のBに最適化された同一文書構造の異なる断面である。

更新時には、どちらか一方だけを絶対的正本とみなさず、対応関係を検査する。

```text
Document vX
↔ Structural IR vY
```

同期が崩れた場合、その差分自体を ξ_sync として記録できる。

---

## ■ 9. 文書群への適用

単一文書だけでなく、シリーズ全体を圧縮できる。

```text
D1 → IR1
D2 → IR2
D3 → IR3
...
```

その後、

```text
IR1 --W_12--> IR2 --W_23--> IR3
```

として比較する。

この操作によって、たとえば、

- 前文書の ξ が次文書で勝手に確定されていないか
- 前文書の候補が次文書で定義へ昇格していないか
- 同じ記号が別の意味で使われていないか
- 本来は新しいBを導入した分岐が、演繹として圧縮されていないか
- 旧版で撤回した一般化が別文書から再侵入していないか

を見つけやすくなる。

したがって構造圧縮は、翻訳前処理だけでなく、**文書群の静的解析**としても機能する。

---

## ■ 10. 既存プロトコルとの位置関係

文書地図上の既存T2プロトコルと役割を分ける。

### RDL_中間設計図プロトコル

```text
ambiguous intent
↓
B / SILN / W_ij / M_B / ξ / break conditions
↓
intermediate design
```

すなわち、まだ文書・実装になる前の意図を構造化する。

### RDL_構造圧縮プロトコル

```text
existing document
↓
structural extraction
↓
IR
↓
comparison / translation / reconstruction
```

すなわち、すでに存在する文書を再利用可能な中間表現へ変換する。

両者は、

```text
intent → structure → document → structure IR → reconstruction
```

という循環の異なる位置を担当する。

---

## ■ 11. 翻訳への利用

翻訳では、言語ごとに語が持つ境界が異なる。

したがって、

```text
Japanese prose
↓ direct translation
English prose
```

だけでは、原文の曖昧さや区別が意図せず固定される可能性がある。

構造圧縮を挟むと、

```text
Japanese prose
↓
Structural IR
↓ preserve invariants
English reconstruction
```

とできる。

このとき翻訳の評価対象は、単語一致だけではなく、

```text
INV preservation
ξ preservation
claim-strength preservation
relation preservation
```

になる。

---

## ■ 12. 版間比較への利用

旧版と新版をそれぞれ圧縮する。

```text
D_v1 → IR_v1
D_v2 → IR_v2
```

比較対象：

```text
ΔB
Δlabels
Δpropositions
Δrelations
Δξ
Δbreak-conditions
Δinvariants
```

これにより、単なる文章差分ではなく、**構造差分**を取れる。

特に、文章上は小さな修正でも、

```text
may → must
candidate → definition
```

のような変更は構造差分として大きく扱える。

---

## ■ 13. 自己食的適用

本プロトコル自身も文書である。

したがって、

```text
RDL_構造圧縮プロトコル
↓ StructuralCompression
RDL_構造圧縮プロトコル_構造抽出版
```

を生成する。

この対は本プロトコルの最初の自己適用例である。

もし構造抽出版から本プロトコルの中心命題を再構成できないなら、

```text
Protocol fails its own reconstruction test
```

となる。

逆に、構造抽出版が本文より強い命題を持つ場合、

```text
false strengthening detected
```

として修正対象になる。

---

## ■ 14. 最小テンプレート

文書ごとの構造抽出版は、最小限なら次の形で作れる。

```text
# [文書名]――構造抽出版

## 0. 目的
## 1. B
## 2. 基本ラベル
## 3. 主命題
## 4. 主線 / relations
## 5. ξ
## 6. 破断条件
## 7. 残存構造
## 8. 他文書との接続
## 9. 最小圧縮
## 10. 再構成時に保存すべき不変量
```

文書の性質に応じて節を増減してよい。
テンプレートそのものを固定規範にはしない。

---

## ■ 15. 破断条件

本プロトコルの読みが破断する条件を置く。

### P1

構造抽出版を作っても、原文との比較・翻訳・破壊検査・再構成のいずれにも実用上の利得がない場合。

### P2

文書の意味が表層表現に強く依存し、関係構造へ圧縮した時点で主要内容が不可逆に消える場合。

例：詩、音響性そのものが主内容の文章、語呂・多義性が中心の作品など。

### P3

不変量の選択そのものが恣意的すぎて、圧縮者が好きな意味だけを保存できてしまう場合。

### P4

IRが独自の専門文書として肥大化し、原文よりも検査困難になる場合。

### P5

構造圧縮が「真の意味の抽出」と誤認され、原文より上位の正本として固定化された場合。

---

## ■ 16. ξ

本プロトコル自身にも未確定が残る。

```text
ξ_protocol = {
  何を不変量と数えるか,
  どこまでを主命題と数えるか,
  修辞が構造そのものになる文書の扱い,
  圧縮粒度の適正値,
  IR間の同値判定,
  再構成可能性の評価方法,
  ξ_compress の測定方法,
  AIと人間で抽出結果がどこまで一致するか,
  大規模文書群での運用コスト
}
```

---

## ■ 17. 残存構造

本プロトコルの細部が破断しても、次は残る。

```text
文章表面
≠ 関係構造

要約
≠ 構造抽出

候補
≠ 定義

局所
≠ 絶対

仮説
≠ 結論

原文のξ
≠ 圧縮によるξ

文書一致
≠ 構造不変量の一致
```

そして、

> **変換の前に、何を保存すべきかを一度明示化する。**

という操作は、翻訳・比較・再記述・版管理・破壊検査に広く残る。

---

## ■ 18. 最短圧縮

```text
D
↓ choose B_compress

{ B,
  labels,
  propositions,
  relations,
  flow,
  ξ_source,
  ξ_compress,
  break conditions,
  residual structure,
  invariants }

= IR_B(D)

↓ audit

no false strengthening
no ξ erasure
no relation invention
no notation collision

↓ reconstruct / compare / translate

D'

Target:
Invariant(D) ≈ Invariant(D')

not:
D = D'
```

---

*v0.1：初版。神・全知全能・完全性シリーズで行った構造抽出を一般化し、文書を再構成可能な中間表現へ変換するT2運用プロトコルとして整理。要約との差、B依存性、標準IR、ξ_source/ξ_compress、誤強化禁止、記号正規化、再構成テスト、文書二重構造、シリーズ横断静的解析、翻訳・版間比較への利用、自己食的適用、破断条件を定義。*
