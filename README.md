<img width="800" alt="無題" src="https://user-images.githubusercontent.com/58076642/131785398-90ff623b-3ea2-492c-a148-87d20cbfa3cd.png">

# Kaggle Optiver Realized Volatility Prediction

## コンペの目的
株のボラティリティ(価格変動の度合い)を時間(time id)ごとに予測する。

## 評価指標
平均二乗パーセント誤差の平方根（RMSPE：Root Mean Squared Percentage Error）    
全データの「正解値-予測値」を正解値で割った値の2乗の平均を計算し、最後に平方根を取ったもの。  

![CodeCogsEqn](https://user-images.githubusercontent.com/58076642/131785016-cb37749d-bf18-48e8-bd00-e06c60df471d.png)



### Overview(deepl)
ボラティリティは、トレーディングフロアで最もよく耳にする言葉の一つですが、それには理由があります。金融市場では、ボラティリティーは価格の変動量を表します。ボラティリティが高いということは、市場が乱高下し、価格が大きく変動していることを意味し、一方、ボラティリティが低いということは、より穏やかで落ち着いた市場を意味します。オプティバーのような取引会社にとって、ボラティリティを正確に予測することは、価格が原商品のボラティリティに直接関係するオプションの取引に不可欠です。

オプティバーは、世界有数の電子マーケットメーカーとして、金融市場の継続的な改善に取り組んでおり、世界中の数多くの取引所で、オプション、ETF、現物株式、債券、外国通貨へのアクセスと価格の向上を実現しています。オプティバーのチームは、数え切れないほどの時間をかけて、ボラティリティーを予測し、最終投資家にとってより公平なオプション価格を継続的に生成する高度なモデルを構築してきました。しかし、業界をリードする価格決定アルゴリズムは進化を止めることはできません。オプティバーがそのモデルを次のレベルに引き上げるために、Kaggleほど最適な場所はありません。

このコンペティションでは、最初の3ヶ月間で、様々なセクターの数百銘柄の短期的なボラティリティを予測するモデルを構築していただきます。何億行もの非常に詳細な財務データを手に入れ、それをもとに10分間のボラティリティーを予測するモデルを設計します。作成したモデルは、トレーニング後の3ヶ月間の評価期間に収集した実際の市場データと比較して評価されます。

このコンペティションを通じて、皆さんはボラティリティーと金融市場の構造に関する貴重な洞察を得ることができます。また、オプティバーが何十年にもわたって直面してきたデータサイエンスの問題についても、より深く理解することができるでしょう。私たちは、Kaggleコミュニティがこの複雑でエキサイティングなトレーディング課題にクリエイティブなアプローチを適用することを楽しみにしています。

### Data(deepl)
このデータセットには、金融市場での実際の取引の実行に関連する株式市場データが含まれています。特に、オーダーブックのスナップショットと約定した取引が含まれています。1秒の分解能で、現代の金融市場のミクロ構造を独特のきめ細かさで見ることができます。

### book_[train/test].parquet colomn infomaiton

市場に投入された最も競争力のある売買注文に関するオーダーブックデータを提供します。  
bookの上位2つのレベルが共有されます。bookの第1レベルは価格面でより競争力があり、第2レベルよりも実行が優先されます。  

Bid（売値） は、買い手が株を買う上で希望する価格で、Ask（買値）は、売り手が株を売る上で希望する価格のこと。

| name | Explanation |
| --- | --- |
| stock_id   | 株の銘柄（どの株か） 全ての株式がすべてのタイムバケットに存在するわけではない |
| time_id   | どの時間の情報かのid (submissionファイルのtime_idと連動している) |
| seconds_in_bucket  |  time_idの中で、0からスタートして何秒後か。たぶん予測するのは、10分のtotalなので、seconds_in_bucketは、最大600 secのはず |
| bid_price[1/2] | 株の買値の希望値の１番目と２番目 ※　(Normalized prices of the most/second most competitive buy level. だから、正確には、１番と２番目に正規化されたレベルの買値 |
| ask_price[1/2] | 株の売値の希望値の１番目と２番目　Normalized prices of the most/second most competitive buy level.  |
| bid_size[1/2] | 買うのを希望している側の１番目と２番目の株式数 |
| ask_size[1/2] | 売るのを希望している側の１番目と２番目の株式数 |  

※　parquetはロード時にカテゴリデータ型に強制することに注意が必要。

### trade_[train/test].parquet colomn infomaiton

実際に実行された取引に関するデータが含まれている。  
通常、市場では、実際の取引よりも受動的な売買意図の更新（bookの更新）が多いため、このファイルは注文書よりもまばらであると予想される場合がある。  

| name | Explanation |
| --- | --- |
| stock_id  | 同上 |
| time_id  | 同上 |
| seconds_in_bucket | 同上。トレードデータとブックデータは同じ時間枠から取得され、トレードデータは一般にまばらであるため、このフィールドは必ずしも0から始まるとは限らないことに注意 |
| price | 1秒間に発生する実行済みトランザクションの平均価格。価格は正規化されており、平均は各トランザクションで取引された株式数によって重み付けされている |
| size | 取引された株式の総数 |
| order_count | 発生している固有の取引注文の数 |

### train.csv

| name | Explanation |
| --- | --- |
| stock_id   | 同上 |
| time_id  | 同上 |
| target  | 10分間のtotalボラティリティ |

### test.csv

| name | Explanation |
| --- | --- |
| stock_id   | 同上 |
| time_id  | 同上 |
| row_id  | stock_idとtime_idを連結したもの |

### sample_submission.csv

| name | Explanation |
| --- | --- |
| row_id   | 同上 |
| target  | 同上 |


## 2021/09/06

取り敢えず日本語ノートブックを読み込む
| 内容 | URL |
| --- | --- |
| 日本語チュートリアル　データの説明などを丁寧にやってくれている | https://www.kaggle.com/chumajin/optiver-realized-eda-for-starter-version |
| 特徴量作成などの場面でどのような工夫をしたか記載してくれている  | https://www.kaggle.com/junjitakeshima/optiver-beginner-s-gradual-improvement-eng |
| 公式によるチュートリアル　WAPなどの用語がたくさん  | https://www.kaggle.com/jiashenliu/introduction-to-financial-concepts-and-data |

使える特徴量　公式チュートリアルより  
・bid/ask spread　最高売値と最高買値の比率から求める  
BidAskSpread=BestOffer/BestBid−1

・Weighted averaged price　WAP(加重平均価格)  
同じ価格帯にビッドとアスクの両方のオファーがある場合、オファーの数が多い方が株式評価額が低くなる。  
ほとんどの場合、連続した取引時間の間、オーダーブックにはビッドオーダー(買値)がオファー（アスク）オーダー(売値)よりも高いというシナリオは存在しない。  

・Log returns 価格の違う株価の変動を比較するために上昇率などで比較する　金融の世界では対数を取ることが推奨されている

・Realized volatility　ボラティリティの推定のために必要なlog returnの計算にWAPを株価として利用する

・ボラティリティは自己相関する傾向にあるらしい


## 2021/09/07
テーブルデータのNNにはTabNetが効く？https://www.kaggle.com/c/optiver-realized-volatility-prediction/discussion/266354  
KholdよりGroupKholdなどのバリデーションの切り方をした方がよい  
特徴量エンジニアリングについて  https://www.slideshare.net/mlm_kansai/kaggle-138546659

### 2021/09/08
GroupKholdで試してみたところCV、LBスコアが多少悪化  　
https://www.kaggle.com/ano12pmo/2xlgbm-fnn-ensemble-groupkhold?scriptVersionId=74258324    
パラメータをチューニングするなど検討したい

### 2021/09/09
LGBMのハイパラの仕方のノートブックが共有　https://www.kaggle.com/shivansh002/optuna-parameter-optimization-lightgbm  
optunaの勉強もまたしておきたい

### 2021/09/10
SIGNATEのファンダメンタル分析のチュートリアル資料を改めて読む  
https://japanexchangegroup.github.io/J-Quants-Tutorial/

#### 時系列データの定義と定常性の概念について

・時系列分析に使う時系列データは、その時点で一度しか観測できないという問題点がある  
・このため将来の予測を行おうとする場合は、時系列データ自身になんらかの構造を仮定して、その構造を用いて予測を行う必要がある  
・時系列分析では、観測された時系列データはある確率変数からの一つの実現値と仮定し、この確率変数の構造（確率分布）のことを時系列モデルと呼ぶ  
・この時系列モデルの概念の根幹にあるものが定常性
- 定常性；時系列データの基本統計量（期待値や分散など）や同時分布が時間の推移によって変わらないことを表す概念のこと(どれだけ確率分布が不変であるかによって弱定常性、強定常性に分割できる)  
- 弱定常性：時系列データの確率分布の平均・分散が時点によらず一定であり、自己共分散も同じ時点差（差分）において常に一定である  
- 強定常性：時系列データにおけるすべての依存構造が、時点に関係なく時間差のみに依存している  

・弱定常性が自己相関についてのみ時間差kに対して依存構造をもつのに対し、強定常性では平均や分散といった全ての確率構造が時間差kに依存するという性質を持っている  
・金融データや株価データにおいては、弱定常性が必要なことが圧倒的に多く、この手の研究分野における定常性というのは弱定常性を指すことが一般的

金融データでは特徴量をあまり増やしすぎない方がよい(オッカムの剃刀)  
説明変数を増やしすぎると未来のデータで同じ現象がはっせいする確率が減少するため。→モデルの複雑さとデータの適合度のバランスが大事  
特に今回のコンペではPBが３件しかないためそこに過剰適合したモデルを構築しないように注意(適切なCVの設計)

### 2021/09/13
TabNetのとても参考になるノートブックを利用(モデルの作成からFeatureImporttanceの評価まで丁寧にやってくれている)  
https://www.kaggle.com/datafan07/optiver-volatility-predictions-using-tabnet

### コンペ終了  
・やったこと  
foldの切り方の工夫 Group kfold StratifiedGroupKFold  
https://www.kaggle.com/nrcjea001/lgbm-baseline-no-leaks-stratifiedgroupkfold

optunaでチューニング

## 銅メダル獲得！！

