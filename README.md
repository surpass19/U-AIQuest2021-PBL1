# U-AIQuest2021-PBL1

## 学んだこと
* いれこ構造の時系列データの扱い
* groupby

## 日記

### 9/17
* 去年の12月のデータをそのまま今年12月のデータでいいんじゃないか？
平均は2.7351290684624017個

欠損値は 0 submit 1 : 4.22
欠損値は 1 submit 2 :4.1464926
欠損値は 1.5 submit 3 : 4.1414926
欠損値は 2 submit 4 :4.1616833 

結構スコアが出た, 1年前との売れる数は, 結構似てるかも
全体的に結構売れてそう
0が多くなると不味そう

```
全部0は4.5971716
全部1で提出してみたら4.3502686
```
全部2とかでも良さそう

* (CV) 月毎で予測するか, 日にちごとで予測するか？
テストデータは月毎での予想だが, trainのデータは, 日毎であるので日毎での予測 → 集計でも行けそう

* 売り上げ個数がマイナスのものはどうするか？
0に切り上げる？
削除で良さそう

* 値段が時系列で変わるっぽい

* (特徴量) 特売フラグ？
```
もしも特売フラグを作るならばsale_historyにある同一商品IDのうち、最大販売価格を取得してそれに対して○%以下の価格の時に特売フラグとか特徴量を作りますが…
```

* いれこ構造難しい
日付, ----------------------------------------------------------------------日付, 日付, 日付, 日付, 日付, 日付, 日付, 日付, 日付, 日付, 日付, 
 |
店舗ID,---------------------店舗ID, 店舗ID, 店舗ID, 店舗ID, 店舗ID, 店舗ID, 店舗ID, |
 |
商品ID, 商品ID, 商品ID,  商品ID,

* 似たようなkaggle
```
kaggleのPredict Future Salesが参考になると思います。  
```
https://www.kaggle.com/c/competitive-data-science-predict-future-sales


* モデル作成案
①時系列そのまま → 1日, 商品ID, 予想 
②商品ID か 店舗ID, またはどっちもごとの1ヶ月予想 → それらを特徴量にして無理か


### 9/18
* AIいらない？
```
年、月、商品ID、店舗IDで集約して売り上げ個数を合計→ 商品ID、店舗IDで集約し、売り上げの平均を求める。
という手法でスコア3.7くらいでした。
もはやAIじゃないですが報告します。
```
年、月、商品ID、店舗IDで集約して売り上げ個数を合計→ 商品ID、店舗IDで集約し、売り上げの平均 fillna(0) → 3.70

去年の12月のデータをそのまま今年12月のデータ.fillna(年、月、商品ID、店舗IDで集約して売り上げ個数を合計→ 商品ID、店舗IDで集約し、売り上げの平均) fillna(0) → 3.78

(疑問)「 去年の12月のデータ」と, 「年、月、商品ID、店舗IDで集約して売り上げ個数を合計→ 商品ID、店舗IDで集約し、売り上げの平均」ってどれぐらい違う？
→ 「 去年の12月のデータ」(スコア 3.78)と, 「年、月、商品ID、店舗IDで集約して売り上げ個数を合計→ 商品ID、店舗IDで集約し、売り上げの平均」(スコア 3.70)では, 「 去年の12月のデータ」の方が大きい → 去年より売れてない！(submit_withoutAI.ipynb)

確かに, 去年の1~10月の売上個数と今年の去年の1~10月の売上個数を比べた時, 明らかに下がっている (data_EDA.ipynb)
「去年の12月のデータ」に下がっている率掛け算(0.7730087581405793倍)して, 下駄をはかすか？

→ 去年の12月と同じ * 0.77, 去年売れてないやつはfillna(2年分の平均).fillna(0) → (スコア 3.68)若干上がった, 「去年売れてないやつはfillna(2年分の平均)」が微妙...他に仮説考える必要あり.


* AI使うとしたら？
validationを考えにくい, 2年分しかないため, 12月をvalidationにしたかったら去年のものしかない


### 9/19
* AIを使うのも考えるために, 似てると言われているkaggleのnotebookを見るかな
→ 1_2feature-engineering, 2_2-xgboostに写経
traindataを月次データにできるようになった, lag特徴量を入れられるようになった.

### 9/21
PBL01_需要予測・在庫最適化　サンプルコードを見て
Feature_generation.ipynb作成して, データを作り直してみる

* 特徴量の作り方について
トレーニングデータは, 1日のデータであるため, 祝日や曜日, 日にちなどが追加できる(Feature_generation-1day_feature.ipynb), 
しかし, テストデータは1ヶ月のデータしかないので, その情報が反映することができない.どうするか
→ まずは, 1ヶ月のデータで追加できる特徴量のみを追加して, 1日単位のものは, 放置する.(Feature_generation.ipynb)
→ 次に考えられるのは, 1日の特徴量を追加して, 後でgroupbyするということ ex)祝日の数, 水曜日の数など(Feature_generation-1day_feature.ipynb)


* 1ヶ月前のlag特徴量について
1ヶ月前のlag特徴量はどうやら使えそう. (1_2feature-engineering.ipynb). 
しかし, テストデータ(2019/12)には1ヶ月前のデータがない → もし, 1ヶ月前のデータが重要そうであれば, 2018年11月のデータに * 0.77をしたデータを1ヶ月前のデータとしても良いかもしれない.

* 対数変換とりあえずはしない

### 9/21
* 商品価格に0の商品があるらしい

* 追加したい特徴量
①日付のsin, cos
②トレンド(kaggle)
③price (agg price)
```
gp_month2 = sales.groupby([‘Month_Block’,  ‘Product_ID’, ‘Store_ID’]).agg({‘Price’: np.mean}).reset_index()
で店舗ごとの月平均の価格を作り、
all_train2 = pd.merge(all_train, gp_month2, on=[‘Month_Block’, ‘Product_ID’, ‘Store_ID’], how=‘left’)
```
```
Priceを追加してみました。
でマージしました。
売り上げがない月もあるので、前月の価格をコピー（for文で頑張ってコピー。もっと上手く書けるのかもしれない）
これで3.9を切るところまで行きました。
売り上げがない店舗は価格が0円になっているのが、良いのかどうか。他の店の価格をコピーして入れた方が良いかもしれないが、精度は落ちそう？
```
④ 割引率, (lag / 元)
⑤ 祝日の数 (agg holiday)
⑥ 月別商品別売上を標準化
⑦ クラスタリング
```
月別商品別売上を標準化したのち、tslearnのTimeSeriesKMeansで時系列クラスタリングしました。
例えば、n=4でやると、
Cluster1：売上減少　：3589item
Cluster2：弱い周期性 ：2331item
Cluster3：後半に売上増加：618item
Cluster4：強い周期性：2502item
のように別れました。最適クラスター数の検討まではできていませんが、
ざっくりと商品別に傾向があることが分かります。
```
⑧ 商品ID, 商品カテゴリのcount_encoding
⑨ 商品カテゴリはone-hot-encodingの方が良さそう
⑩ 2018年11月 * 0.77

* CVの作り方案
⑩ 時系列のCV + モデルのアンサンブル


### 9/22
* 追加済み特徴量
①日付のsin, cos
⑤ 祝日の数 (agg holiday)
⑩ 2018年11月 * 0.77
②トレンド(kaggle)
③price (agg price)(kaggle)
④ 割引率, (lag / 元)(kaggle)


* 追加したい特徴量
⑥ 月別商品別売上を標準化
⑦ クラスタリング
⑧ 商品ID, 商品カテゴリのcount_encoding

* モデルをlightgbmで作成してみた
train_LGBM.ipynb(ハイパラチューニングもした)
精度は4.3ぐらい, 全然上がらない

* 時系列の交差検証をやってみたい
→ TimeSeriesSplit
https://blog.amedama.jp/entry/time-series-cv
train_TimeSeriesSplit.ipynb
精度は4.2ぐらい, 全然上がらない

### 9/23
* 追加済み特徴量
発売日
商品価格0_flag
lag_trend

* 今後やりたいこと
月別商品別売上を標準化
クラスタリング
(商品ID, 商品カテゴリのcount_encoding)
2019年11月を予測　→ 特徴量にする
kggle notebookみる

### 9/26
* 追加済み特徴量
クラスタリング(商品別売上個数, 店舗別売上個数)

* 今後やりたいこと
月別商品別売上を標準化
2019年11月を予測　→ 特徴量にする
kggle notebookみる(kaggle_Predict_Future_Salesフォルダ)


### 10/7
* 追加済み特徴量
月別商品別売上を標準化
2019年11月を予測　→ 特徴量にする

結局, 
→ 去年の12月と同じ * 0.77, 去年売れてないやつはlightBGMモデルで予測.fillna(0)が一番精度が良かった.

lightBGMモデルのみでは4.0を切るか切らないかぐらい
順位 https://signate.jp/competitions/492/leaderboard
(画像あり)


* 今後やりたいこと
target_encoding

## notebook概要

* originaldata_kakunin
特に何も変えずデータ見てみる
* deta_addfeature
とりあえず, 思いついた特徴量を入れて, 保存
* data_EDA
↑のデータでグラフとか見てみる
* submit_withoutAI
AIを使わずに, 仮説ベースで予測する
---------------------------------
* PBL01_sample_code
PBL01_需要予測・在庫最適化　サンプルコード

* 1_2feature-engineering, 2_2-xgboost
似ているkaggleのnotebook lag関数やgroupby

* Feature_generation
特徴量を作成する
↓ 
* train_LGBM.ipynb
* train_TimeSeriesSplit.ipynb

* ☆Feature_generation-pred11_to_12
* ☆Feature_generation-pred11_to_12-2 (最終submit)
11月の予測と12月の予測を行うための, 特徴量を作成する
↓ 
* train_TimeSeriesSplit_month11.ipynb
* ☆train_TimeSeriesSplit_month12.ipynb
予測

* Feature_generation-pred_Future2month
2ヶ月先の売上個数の予測(2019年10月 → 2019年12月)を行うための, 特徴量を作成する
↓ 
* train_TimeSeriesSplit_Future2month.ipynb
予測


## スコア

### 
submit 1 : 4.22
submit 2 : 4.1464926
submit 3 : 4.1414926
submit 4 : 4.1616833
submit 7 : 3.67
submit 26: 3.92

## slack知見まとめ

“““
商品価格が別テーブルじゃなくて時系列データに入っているのが不思議だなと思ったんですが、割引が入るんですかね…
多分中古と返品の問題があるような気がしますね。同じ月・店舗・商品で絞っても値段変わるようなものが多々ありました

kaggleのPredict Future Salesが参考になると思います。  
https://www.kaggle.com/c/competitive-data-science-predict-future-sales


“““