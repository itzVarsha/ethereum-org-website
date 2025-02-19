---
title: プルーフ・オブ・オーソリティ(PoA)
description: ブロックチェーンエコシステムにおけるプルーフ・オブ・オーソリティのコンセンサスプロトコルとその役割について
lang: ja
---

**プルーフ・オブ・オーソリティ(PoA)** は、評判ベースのコンセンサスアルゴリズムで[プルーフ・オブ・ステーク](/developers/docs/consensus-mechanisms/pos/)を変更したバージョンです。 主にプライベートチェーン、テストネット、ローカル開発用ネットワークで使われています。 評判ベースのコンセンサスアルゴリズムであるプルーフ・オブ・オーソリティは、ブロックを生成するために、認証された署名者達のセットを信頼することを求めます。この点でステーキングベースのメカニズムであるプルーフ・オブ・ステークと違います。

## 前提条件{#prerequisites}

このページについて深く理解するには、事前に[トランザクション](/developers/docs/transactions/)、[ブロック](/developers/docs/blocks/)、[コンセンサスメカニズム](/developers/docs/consensus-mechanisms/)について読むことをお勧めします。

## プルーフ・オブ・オーソリティ(PoA)とは何か {#what-is-poa}

プルーフ・オブ・オーソリティは、**[プルーフ・オブ・ステーク](/developers/docs/consensus-mechanisms/pos/) (PoS)** を変更したバージョンです。プルーフ・オブ・ステークによるステークベースのメカニズムの代わりに、評判ベースのコンセンサスアルゴリズムを使います。 この用語は、ギャビン・ウッドによって2017年に初めて紹介されました。そして、このコンセンサスアルゴリズムは、テストネットやローカル開発ネットワークなどのプライベートチェーンで最も使われています。プルーフ・オブ・ワークのような高品質なリソースを必要しませんし、少数のノードがブロックチェーンを保存し、ブロックを生成するのでプルーフ・オブ・ステークのようなスケーラビリティの問題もありません。

プルーフ・オブ・オーソリティは、認証された署名者達のセットを信頼することを求めます。この署名者達のセットは、[ジェネシスブロック](/glossary/#genesis-block)に設定されます。 現在の最新の実装としては、認証された署名者のすべてが平等のパワーと権限を保持し、チェーンのコンセンサスに至ります。 評判ベースのステーキングは、すべての認証されたバリデータは本人確認手続き(KYC)などを通して周知されているか、有名な機関のみがバリデータとなるので、バリデータが何か不正行為を行った場合、そのバリデータの身元が判明するというアイデアに基づいています。

プルーフ・オブ・オーソリティには、複数の実装がありますが、イーサリアムの実装の標準は、**Clique**です。これは、[EIP-225](https://eips.ethereum.org/EIPS/eip-225)を実装しています。 Cliqueは、デベロッパーフレンドリーで、簡単に実装できる標準であり、クライアントの同期タイプのすべてをサポートしています。 他の実装としては、[IBFT 2.0](https://besu.hyperledger.org/stable/private-networks/concepts/poa) や[Aura](https://openethereum.github.io/Chain-specification)などがあります。

## 仕組みの説明{#how-it-works}

プルーフ・オブ・オーソリティでは、新しいブロックを作るために、認証された署名者達のセットが選ばれます。 署名者達は、評判ベースで選ばれ、これらの署名者達のみが新しいブロックを作成することを許されます。 ラウンドロビン方式で署名者達が選ばれます。選ばれた各署名者は、特定の時間枠内でブロックを作成することが許されます。 このブロック作成時間は固定されており、署名者達は時間枠内にブロックを作成しなければなりません。

このコンテクストにおける評判というのは、定量化されているものではなく、MicrosoftやGoogleなどのように知名度のある企業の評判です。そのため、この信頼のある署名者達の選定方法は、アルゴリズムではなく、むしろ通常の人間活動における _信頼_ です。たとえば、Microsoftが数百から数千ものスタートアップのプルーフ・オブ・オーソリティのプライベートネットワークを作成し、Microsoft自体が唯一の信頼された署名者としての役割りを果たして、将来的にGoogleのような知名度のある署名者達を追加する可能性があるならば、スタートアップ企業はMicrosoftが常に正直に行動することを疑うことなく信頼して、ネットワークを使います。 これにより、分散化を維持し機能させるために、異なる目的で構築された小規模またはプライベートなネットワークでステーキングする必要性や、大量の電力とリソースを消費するマイナーの必要性が解消されます。 VeChainのように一部のプライベートネットワークでは、プルーフ・オブ・オーソリティの標準を使用しています。また、一方でBinanceなどは[PoSA](https://academy.binance.com/en/glossary/proof-of-staked-authority-posa) という、プルーフ・オブ・オーソリティとプルーフ・オブ・ステーキングのカスタマイズバージョンを使っています。

署名者達自身によって、この投票プロセスが行われます。 各署名者は、ブロックの新規作成時にブロック内で署名者の追加または削除の投票を行います。 投票はノードによって集計され、署名者達が加えられるか削除されるかは、閾値である`SIGNER_LIMIT`の特定の値に投票が到達したかどうかを基準とします。

小さなフォークが起こる状況があり得えます。ブロックの難易度は、署名が順序か順序外かによって違います。 「順序」のブロックの難易度は2、「順序外」のブロックの難易度は1です。 小さなフォークが発生した場合、「順序」ブロックにシールした多数の署名者を持つチェーンに最も多い難易度が累積し、勝利します。

## 攻撃ベクトル{#attack-vectors}

### 悪意のある署名者達{#malicious-signers}

署名者リストに悪意のあるユーザーが追加されたり、署名鍵およびマシンが侵害されたりする可能性があります。 このようなシナリオにおいて、プロトコルには、再編成やスパムに対して防御する能力が必要になります。 提案された解決策では、N人の認証された署名者のリストが与えられた場合、各署名者はK回ごとに1つのブロックしかミントできません。これにより、被害が限定され、残りのバリデータが悪意のあるユーザーを排除するための投票を行うことが可能になります。

### 検閲{#censorship-attack}

もう1つの興味深い攻撃ベクトルは、署名者(あるいは署名者のグループ)が、認証者リストからそれらの署名者を削除することに投票したブロックを検閲しようとする場合です。 これに対処する方法は、署名者に許可されるミントの頻度をN/2の中から1に制限することです。 これにより、悪意のある署名者達は、署名するアカウントの最低51%をコントロールする必要があります。51%に達すれば、チェーンの新たな信頼できる情報源になることができます。

### スパム{#spam-attack}

他の小さな攻撃ベクトルは、悪意のある署名者達がミントする各ブロック内において新たな投票提案をすることです。 ノードは、実際の認証された署名者のリストを作成するために、投票のすべてを集計する必要があるため、常に投票のすべてを記録しなければなりません。 投票枠に制限を設けないと、投票はゆっくりと、しかし無制限に増える可能性があります。 これに対する解決策は、Wブロックの _移動_ 枠を設けて、そのWブロック後の投票が古いと見なすことです。 _妥当な枠は1～2エポックでしょう。_

### 同時発生ブロック{#concurrent-blocks}

PoAネットワークでは、N人の認証された署名者がいる場合、各署名者はK回ごとに1つのブロックをミントすることが許可されています。つまり、任意の時点でN-K+1人のバリデータがブロックをミントできることになります。 これらのバリデーターがブロックを競って生成するのを防ぐために、各署名者は新しいブロックをリリースする時間に小さなランダムな「オフセット」を追加する必要があります。 このプロセスにより、小規模なフォークが発生する可能性は低くなりますが、それでも時折フォークが発生することがあります。これはメインネットと同様です。 署名者が権限を悪用したり混乱を引き起こしたりした場合、他の署名者達は投票により排除することができます。

例えば、10人の認証された署名者がいて、各署名者が20回ごとに1つのブロックを作成できる場合、任意の時点で11人のバリデータがブロックを作成できることになります。 マイナー同士のブロック作成の競争を防ぐために、各署名者は
新しいブロックをリリースする時間に、小さなランダムな「オフセット」を追加します。 これにより、小さなフォークの発生を減らすことが出来ますが、イーサリアムのメインネットで見られるように、場合によってはフォークが発生する可能性があります。 署名者が権限を悪用したり、混乱を引き起こした場合、投票によりネットワークから排除することができます。

## メリットとデメリット{#pros-and-cons}

| メリット                                                                           | デメリット                                                                              |
| ------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------- |
| ブロック署名者の数を制限することをベースとしているため、他の一般的なメカニズムであるプルーフ・オブ・ステークやプルーフ・オブ・ワークなどよりもスケーラブル。 | プルーフ・オブ・オーソリティのネットワークでは、検証ノードの数が比較的少ない。 これにより、プルーフ・オブ・オーソリティのネットワークは、より集中化する。      |
| プルーフ・オブ・オーソリティは、実行と維持が安価。                                                      | ブロックチェーンは評判の確立したエンティティを要求するため、一般的な人は通常、認証された署名者になることができない。                         |
| トランザクションの承認が非常に速い。新しいブロックの検証に必要な署名者の数が限られているため、1秒未満で達成可能。                      | 悪意のある署名者達が、ネットワークにおいて再編成、二重支払い、トランザクションの検閲をする可能性がある。これらの攻撃を軽減することはできるが、完全に防止はできない。 |

## 参考リンク{#further-reading}

- [EIP-225](https://eips.ethereum.org/EIPS/eip-225) _Clique標準_
- [プルーフ・オブ・オーソリティの研究](https://github.com/cryptoeconomics-study/website/blob/master/docs/sync/2.4-lecture.md) _Cryptoeconomics_
- [プルーフ・オブ・オーソリティとは何か](https://forum.openzeppelin.com/t/proof-of-authority/3577) _OpenZeppelin_
- [プルーフ・オブ・オーソリティの説明](https://academy.binance.com/en/articles/proof-of-authority-explained) _binance_
- [ブロックチェーンにおけるプルーフ・オブ・オーソリティ](https://medium.com/techskill-brew/proof-of-authority-or-poa-in-blockchain-part-11-blockchain-series-be15b3321cba)
- [Cliqueの説明](https://medium.com/@Destiner/clique-cross-client-proof-of-authority-algorithm-for-ethereum-8b2a135201d)
- [非推奨のプルーフ・オブ・オーソリティであるAuraの仕様](https://openethereum.github.io/Chain-specification)
- [他のプルーフ・オブ・オーソリティの実装であるIBFT 2.0](https://besu.hyperledger.org/stable/private-networks/concepts/poa)

### 映像で学びたい場合 {#visual-learner}

プルーフ・オブ・オーソリティの映像学習教材:

<YouTube id="Mj10HSEM5_8" />

## 関連トピック{#related-topics}

- [プルーフ・オブ・ワーク](/developers/docs/consensus-mechanisms/pow/)
- [プルーフ・オブ・ステーク](/developers/docs/consensus-mechanisms/pos/)
