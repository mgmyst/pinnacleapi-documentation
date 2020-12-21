<img _ngcontent-c2="" src="https://avatars2.githubusercontent.com/u/31601407?s=70&amp;u=f3c6e1cfc8a26665e4a4df6d8da4a7ee527aeceb&amp;v=4" style="background-color: transparent;"> 

# FAQ

### ホームチームは常に _team1_ ですか?

いいえ。

ベットを置く際には `team` を指定しなければなりません。`/odds` や `/fixtures`では、チームは home/away と呼ばれます。
home/away と team1/team2の対応付けは、 `/leagues` のレスポンスで `homeTeamType` プロパティで指定します。

ベット方法の詳細は[GettingStarted-ja.md](GettingStarted-ja.md)を参照してください
  
### 関連するイベントを見つけるには?

関連するイベントを探すために "親"イベントを辿るには[Get Fixtures](https://pinnacleapi.github.io/linesapi#operation/Fixtures_V1_Get) の `parentId` を利用することができます。 

関連イベントを探すにあたり次のことがヒントになるでしょう:

- ゲーム前とゲーム後(ライブ)では異なるイベントが開催されており、`liveStatus` で区別が可能です
- 親イベントは `parentId` を持ちません。またゲーム前イベントは必ず `liveStatus`=0 か `liveStatus`=2 となります
- 場合によっては、同一イベント内で複数のライブイベントを開催することもあります 
- ライブイベント (`liveStatus`=1) は、作成時から常に `parentid` を持ちます 
- 試合前のイベントにはほとんどの場合 `parentid` が設定されています。しかし我々は最善の努力をしていますが、様々なデータソースのため、 `parentid` 「必ず」設定されていることは保証できません 
- 試合前のイベントの場合、`parentId` はイベントの作成時にすぐには設定されないかもしれませんが、イベントの価格を設定する前に設定されます 
- 既知の問題として、北米リーグやESportsの一部の試合前イベントでは、 `parentid` が設定されていない場合があります
  
 例:

 ``` json
 {
    "sportId": 29,
    "last": 148671597,
    "league": [
        {
            "id": 2331,
            "name": "Norway - 1st Division",
            "events": [
                {
                    "id": 837721686,
                    "starts": "2018-04-10T17:00:00Z",
                    "home": "Viking Fk",
                    "away": "Mjondalen",
                    "rotNum": "6751",
                    "liveStatus": 1,
                    "status": "O",
                    "parlayRestriction": 2,
                    "parentId": 834342247,
                    "altTeaser": false
                },
                {
                    "id": 834342247,
                    "starts": "2018-04-10T17:00:00Z",
                    "home": "Viking Fk",
                    "away": "Mjondalen",
                    "rotNum": "6751",
                    "liveStatus": 2,
                    "status": "I",
                    "parlayRestriction": 2,
                    "altTeaser": false
                }
            ]
        },
        {
            "id": 6816,
            "name": "Norway - 1st Division Corners",
            "events": [
                {
                    "id": 837721684,
                    "starts": "2018-04-10T17:00:00Z",
                    "home": "Viking Fk (Corners)",
                    "away": "Mjondalen (Corners)",
                    "rotNum": "6751",
                    "liveStatus": 1,
                    "status": "O",
                    "parlayRestriction": 1,
                    "parentId": 834342247,
                    "altTeaser": false
                },
                {
                    "id": 837721615,
                    "starts": "2018-04-10T17:00:00Z",
                    "home": "Viking Fk (Corners)",
                    "away": "Mjondalen (Corners)",
                    "rotNum": "6751",
                    "liveStatus": 2,
                    "status": "I",
                    "parlayRestriction": 1,
                    "parentId": 834342247,
                    "altTeaser": false
                }
            ]
        }
    ]
} 
```

解説:
試合前の親イベントID:834342247があり、それは関連するライブイベントID:837721686を持っています。また、別のリーグで2つのコーナーイベントが存在しています。ひとつはライブイベント(837721684)、もうひとつは試合前のためのもの(837721615)です。

`parentid` の導入により、ローテーションナンバーの必要性がなくなります。 
次のバージョンの `/fixtures` では、 `rotNum` プロパティは廃止されることに注意してください。

### propsやfuturesマーケットを見つけるには?

propsやfuturesマーケットはスペシャルオファーとして提供されます。`/fixtures/special` や `odds/special` を利用して情報を入手してください。

"スペシャル"は関連するイベントの詳細を指定するための `event` オブジェクトを持ちます。
これは、 `/fixtures` エンドポイントからイベントにリンクするために使うことができます。

```json
  {
                    "id": 1065232780,
                    ...
                    "event": {
                        "id": 1051780418,
                        "periodNumber": 1,
                        "home": "Malta",
                        "away": "Norway"
                    },
                    ...
                },
```

###　ベッティングのためのマーケットが開いているのはいつですか? 

ストレートマーケット( `moneyline` , `spreads`, `totals`,  `teamtotal` )については、[Get Odds](https://pinnacleapi.github.io/linesapi#tag/Odds)(_注:API_)で得られる情報が次を満たすときにベッティングが可能です:
1. Period　の `status` が 1
2. 価格がついている
3. `cutoff` が未来の日時

例: `moneyline` マーケットが提供されないとき、オブジェクト全体が欠落してしまいます。これは他のマーケットタイプでも同じです。
```json
                 {
                            "lineId": 527557614,
                            "number": 1,
                            "cutoff": "2018-06-30T21:00:00Z",
                            "maxSpread": 250,
                            "maxTotal": 250,
                            "status": 1,
                            "spreads": [
                                {
                                    "hdp": -0.25,
                                    "home": 140,
                                    "away": -172
                                }
                            ],
                            "totals": [
                                {
                                    "points": 0.75,
                                    "over": -128,
                                    "under": 107
                                }
                            ]
                        }

```

ライブイベントの場合は、期間`status`と同様にオッズも頻繁に変化するので注意してください。
このような頻繁な変更のため、試合前のイベントよりも、[Get Line](https://pinnacleapi.github.io/linesapi#operation/Line_Straight_V1_Get)(_注:API_)のレスポンスに `NOT_EXISTS` というステータスが表示されることが多くなる可能性があります。

スペシャルマーケットは次のようなベッティングの場合に開催されます:

1. スペシャルイベントの `status` が `"O"` または `"I"` である。参考:[Get Special Fixtures](https://pinnacleapi.github.io/linesapi#operation/Fixtures_Special_V1_Get)
2. マーケットが priced である。参考:[Get Special Odds](https://pinnacleapi.github.io/linesapi#operation/Odds_Special_V1_Get)
3. スペシャルイベントの `cutoff` が未来の日時である。参考:[Get Special Fixtures](https://pinnacleapi.github.io/linesapi#operation/Fixtures_Special_V1_Get).
 
### ライブイベントにベットするには?

ライブディレイ（遅延）のあるイベントに置かれたベットは、通常のベットとは異なる扱いを受けます。ベットは `ACCEPTED` されて初めて `betId` が割り当てられます。
そのようなベットの `status` を調べる唯一の方法は、`/bets?uniqueRequestIds` を問い合わせることです。

`/bets?uniqueRequestIds=86a90ab9-fca1-4703-a11c-ce329a85584e`

ベットが `PENDING_ACCEPTANCE` ステータスである限り、レスポンスは次のようなものになります。

```json
{
    "straightBets": [
        {
            "uniqueRequestId": "86a90ab9-fca1-4703-a11c-ce329a85584e",
            "betStatus": "PENDING_ACCEPTANCE"
        }
    ]
}

```

備考: `PENDING_ACCEPTANCE` ステータスは追って `NOT_ACCEPTED` か `ACCEPTED` になります

もし `NOT_ACCEPTED` ステータスとなった場合、次のようなレスポンスとなります。

```json

{
    "straightBets": [
        {
            "uniqueRequestId": "86a90ab9-fca1-4703-a11c-ce329a85584e",
            "betStatus": "NOT_ACCEPTED"
        }
    ]
}


```

`ACCEPTED` ステータスの場合、レスポンスはベッドの詳細を含む次のようなものを返します。

```json

{
    "straightBets": [
        {
            "betId": 800110193,
            "uniqueRequestId": "86a90ab9-fca1-4703-a11c-ce329a85584e",
            "wagerNumber": 1,
            "placedAt": "2017-12-20T21:57:22Z",
            "betStatus": "ACCEPTED",
            "betType": "MONEYLINE",
            "win": 2519.25,
            "risk": 11991.63,
            "oddsFormat": "AMERICAN",
            "updateSequence": 175277412,
            "sportId": 29,
            "leagueId": 5595,
            "eventId": 799939900,
            "price": -476,
            "teamName": "Universitario",
            "team1": "Universitario",
            "team2": "Club Petrolero",
            "periodNumber": 0,
            "isLive": "TRUE"
        }
    ]
}
 
```

6秒前後のライブディレイを適用していますので、 `/bets` の最初の利用はベッティングから約6秒後となるはずです。

ベットしてから30分後には、提供されたuniqueRequestIdに対するレスポンスを返さなくなります。これは、最適なパフォーマンスを維持するためにキャッシュをクリーンアップするためです。

また、 `RUNNING` ベットリストは、 `PENDING_ACCEPTANCE` や `NOT_ACCEPTED` のライブディレイベットを返しませんのでご注意ください。

### アフィリエイトでAPIにアクセスするにはどうすればいいですか?

アフィリエイトとしてAPIにアクセスするためには、過去3ヶ月間の新規ファンディングサインアップを5件紹介する必要があります。過去3ヶ月間の5件の新規ファンディングサインアップを紹介できなかった場合、APIへのアクセスが取り消される場合があります

### オッズはどれくらいの頻度でリフレッシュできますか?

フェアユースポリシーをご確認ください。

### www.pinnacle.com へのリンクが正しくトラッキングされていることを確認するにはどうすればいいですか?

www.pinnacle.com へのリンクには、必ずトラッキングリンクを付けてください。
あなたのトラッキングリンクは、http://affiliates.pinnacle.com にアクセスしたあなたのアフィリエイトアカウントから利用可能です。 

正しいトラッキングリンクは、次のような形式になります:   http://affiliates.pinnacle.com/processing/clickthrgh.asp?btag=a_numbersb_numbers

### レスポンスをキャッシュしていますか? またそれはどの程度の期間ですか?

`Get Odds` と `Get Fixtures` のスナップショットは60秒間キャッシュされます。  
`Get Odds` と `Get Fixtures` のデルタコールはキャッシュされません。

### www.pinnacle.com の関連オッズページに直接リンクすることはできますか?

可能です。ただし customerservice@pinnacle.com までご連絡ください。

### データがあるスポーツやリーグだけを取得するにはどうすればいいですか?

`Get Sports` 及び `Get Leagues` APIは `hasOfferings` プロパティを持っており、これを有効にすることで利用可能なベットを持つスポーツやリーグのみを取得できます。

### USD以外の通貨での最大ベット額はどのように計算されていますか?

我々はoanda.comを利用し、24時間ごとに為替レートを更新しています。
米ドル以外の通貨については、通貨換算の結果、指定された小数点以下の最大の整数に四捨五入されます（_訳注:小数点切捨_）のでご注意ください。
例えば、最大ベット額が£345.23の場合は、£345に切り捨てられます。

### APIではどのタイムゾーンを使用していますか?

全てにおいてグリニッジ標準時（GMT）です。

### イベントが削除されたか決済されたかを知るにはどうすればいいですか?

イベントの期間が確定したのか、イベントが削除されたのかを調べるには、`Get Settled Fixtures`(_注:API_) を利用してください。

### オッズの変化を取得するには?

1) スナップショット: /odds を呼び出します (since パラメータなし) - これはキャッシュされたオッズのスナップショットを返します
2) デルタ: /odds を呼び出します (スナップショットのレスポンスから `since` パラメータを指定) - スナップショット以降のすべての変更を取得します

デルタレスポンスは現在提供されているすべてのマーケットの変更後のピリオドのみを持ちます。
例えば、イベントに対してピリオド0とピリオド1のオッズを提供していて、ピリオド1のマーケットのひとつに変更があった場合、デルタコールでは変更されたマーケットだけでなく、現在提供しているすべてのマーケットでピリオド1のみが提供され、ピリオド0はデルタレスポンスには含まれません。

例:

1) スナップショットコールはピリオド `number`=1 とピリオド `number`=0 を返し、どちらも `moneyline` と `spreads` のオッズを持つ
2) その後、デルタコールは `moneyline` と `totals` でピリオド1だけを返します。
    
 => これは、`number`=0 の期間には何も変更がなかったことを意味し、`number`=1 の期間には `スプレッド` が提供されなくなり、`合計` が提供されるようになり、`moneyline` が新しい価格を持つようになったことを意味します。
 
### TLS (Transport Layer Security) のどのバージョンがサポートされていますか?
 
セキュリティ要件に準拠するために、APIはTLS 1.2（できれば）とTLS 1.1のみをサポートしています。
 
### なぜEsportsでアクセスが拒否されてしまうのですか？
 
Esportsへのアクセスはブロックされており、特別な許可が必要です。アクセスを得るためには、b2b@pinnacle.com に連絡してください。 
 
### なぜ `/line` の操作を呼び出すと `NOT_EXISTS` が表示されるのですか?
 
次の理由が考えられます:

1) 送信している値が誤っている  `periodNumber`, `eventid` , `leagueid` , `sportId` など

実際の試合は複数のイベントで提供されることも珍しくありません。
また、同じ `periodNumber` であっても、`eventid` X では `MONEYLINE` を提供し、`eventid` Y では `TOTAL` を提供することもあります。

2) `cutoff` に過去の日付を指定している

3) その時点でセレクションに価格がついていない

4) 正しくない値の送信 `handicap`, `team` , `side` など

5) ピリオドの`status`が「2」または「offline」

### 削除されたイベントを検出するには？

システムからイベントが削除されることもあります。
そのような場合、`/fixtures`では削除されたイベントは返されないので、`/fixtures/settled`で `number`=0, `status`=5 のピリオドでイベントを探してください。。 
 
```json
  {
                    "id": 933912855,
                    "periods": [
                        {
                            "number": 0,
                            "status": 5,
                            "settlementId": 3637379,
                            "settledAt": "2018-12-24T02:12:06.707Z",
                            "team1Score": 0,
                            "team2Score": 0
                        }
                    ]
                },
 
```

### ベッティング時の予期せぬエラーに対応するには?

ベッティング操作時の予期せぬエラーの発生は、必ずしもベッティングの失敗を意味しません。
[`bets?uniqueRequestIds={comma separated uniqueRequestIds}`](https://pinnacleapi.github.io/betsapi#operation/Bets_GetBetsByTypeV3)のようにして必ずベッティング状況を確認してください。
リトライするようなロジックを組み込むならば、同じユニークリクエストIDを使うようにしてください。 
ユニークリクエストIDの動作の詳細については[こちら](https://github.com/pinnacleapi/pinnacleapi-documentation#deduplication)を確認してください。

### ベッティングの際にRESUBMIT_REQUESTエラーが発生した場合の対処法は？

このエラーは、取引ロジックが内部パラメータを更新しているときに発生する可能性があります。これはあなたの側のエラーではありませんし、APIエラーでもありません。別のレスポンスを受信するまで、賭け金を再送信し続けてください。

###  `/odds`の最大ボリュームリミットから最大リスクを計算するには？

`/odds` は最大ボリュームリミットを返します。
最大ボリュームから最大リスクを計算するには、10進オッズ形式の価格については、次の式を使用することができます。

もし  price  > 2  ならば:  
```
maxRisk = maxVolume  
```
, または price  < 2 ならば: 
```
 maxRisk = maxVolume/(price - 1)
```

##### 例:

`/odds`が次のようなmoneylineを返してきた場合、

```json 
{
                            "lineId": 242220498,
                            "number": 1,
                            "cutoff": "2019-08-30T21:00:00Z",
                            "maxMoneyline": 250,
                            "status": 1,
                            "moneyline": {
                                "home": 1.819,
                                "away": 2.03
                            }
                        }

```

最大ボリュームは250。
ホームチームの最大リスクは305。
アウェイチームの最大リスクは250。