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

Please note that for live events, odds change quite frequently as well as the period `status`. 
Due to these frequent changes, it’s possible that you will be getting status `NOT_EXISTS` in the [Get Line](https://pinnacleapi.github.io/linesapi#operation/Line_Straight_V1_Get) response more often than for the pre-game events.

A special market is open for betting if:

1. Special event `status` is `"O"` or `"I"`. See [Get Special Fixtures](https://pinnacleapi.github.io/linesapi#operation/Fixtures_Special_V1_Get).
2. Market is priced. See [Get Special Odds](https://pinnacleapi.github.io/linesapi#operation/Odds_Special_V1_Get) .
3. Special event `cutoff` is in the future. See [Get Special Fixtures](https://pinnacleapi.github.io/linesapi#operation/Fixtures_Special_V1_Get).
 
### How to place a bet on live events?

Bets placed on events with live delay are treated differently than other bets. They get  `betId`  assigned only once they are `ACCEPTED`.


The only way to find out the  `status`  of such a bet is by querying  `/bets?uniqueRequestIds`:

`/bets?uniqueRequestIds=86a90ab9-fca1-4703-a11c-ce329a85584e`

As long as the bet is in `PENDING_ACCEPTANCE`, the response would be:

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



NOTE: Status can change from `PENDING_ACCEPTANCE` to `NOT_ACCEPTED` or `ACCEPTED`


If the bet was `NOT_ACCEPTED`, the response would be:

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

If the bet was `ACCEPTED`, the response includes the full bet details:

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

We apply live delay of around 6 seconds, so the first call to  `/bets`  should be 6 seconds after placing a bet. 

30 minutes after placing a bet, we will stop returning a response for provided uniqueRequestId. This is due to cache cleanup to maintain optimal performance.

Please also note that the `RUNNING` bet list does not return any live delay bets that are `PENDING_ACCEPTANCE` or `NOT_ACCEPTED`.




### How do I get access to the API as an affiliate?

In order to access the API as an affiliate, you are required to send 5 new funded signups from the previous 3 months. If you are unable to refer 5 new funded signups from the previous 3-month period, access to the API may be rescinded.

### How often can I refresh your odds?

Please check our fair use policy

### How do I make sure my links to www.pinnacle.com are tracking correctly?

Please ensure that any links back to  www.pinnacle.com  are tagged with your tracking link. 
Your tracking links are available from your affiliate account accessed at  http://affiliates.pinnacle.com

A correct tracking link will have the format:   http://affiliates.pinnacle.com/processing/clickthrgh.asp?btag=a_numbersb_numbers

### Do you cache responses? For how long?

`Get Odds`  and  `Get Fixtures`  snapshot calls are cached for 60 seconds.  
`Get Odds`  and  `Get Fixtures`  delta calls are not cached.

### Can I link directly to the relevant odds pages on www.pinnacle.com (deep linking)?

Yes. To link directly to our odds pages, please contact customerservice@pinnacle.com

### How can I return only those sports and leagues which have data?

`Get Sports`  and  `Get Leagues`  operations have the  `hasOfferings`  property that indicates availability of the markets.

### How do you calculate maximum bet amounts for currencies other than USD?

We follow oanda.com, and update our exchange rates every 24 hours. Please note that for non-USD currencies, we round them to the largest integer less-than or equal-to the specified decimal number resulting from currency conversion. For example, a maximum bet amount of £345.23 would be rounded down to £345.

### What time zone is used for the API?

All times are GMT.

### How can I know if an event is deleted or settled?

Please use  `Get Settled Fixtures`  to find out if the event's period was settled or if the event was deleted.


### How to get odds changes?

1) Call the snapshot /odds (without the since parameter) - this would return cached odds snapshot
2) Call the delta /odds (with the `since` parameter,  from the snapshot response) - to get all the changes since the snapshot.

Delta response has only changed periods, with all the markets that are currently offered. For example, if we offer period 0 and period 1 odds on an event and there was a change in one of the markets in period 1, the delta call will have only period 1 with all the markets that we currently offer, not just the changed markets, and the period 0 will not be in the delta response.

Example:

1) Snapshot call returns period `number`=1 and period `number`=0 , and both of them have `moneyline` and `spreads` odds.
2) Subsequent, Delta call returns just period 1 with the `moneyline` and `totals`
    
 => This means that the period `number`=0 did not have any changes, and on the period `number`=1 , the `spreads` is not offered anymore while the `totals` are offered now and the `moneyline` may have new prices
 
 ### What TLS (Transport Layer Security) versions are supported?
 
 To be compliant with the security requirements API supports only TLS 1.2 (preferably ) and TLS 1.1.
 
 
 ### Why am I getting denied access on Esports?
 Access to Esports is blocked and requires special authorization. To get the access please contact b2b@pinnacle.com and explain your business case. 
 
 ### Why am I getting `NOT_EXISTS` when calling `/line` operation?
 
 These are possible reasons:

1) Not sending correct  `periodNumber`, `eventid` , `leagueid` or `sportId`

It is not a rare situation that the actual match is offered with more than one event.
Different periods of the same actual match can be offered with the different events - period 0 can be  offered in `eventid` X and period 1 can be offered on `eventid` Y. It may also happen that for the same  `periodNumber` we offer `MONEYLINE` on `eventid`  X but `TOTAL` on `eventid`  Y.


2) Period `cutoff` date time is in the past.

3) Selection has no prices at the moment.

4) Not sending correct `handicap`, `team` or `side`

5) Period `status`=2 , offline

### How to detect deleted events?
Sometimes an event can be deleted from the system, in such a case, since `/fixtures` would not return deleted events,  the event will be returned in `/fixtures/settled` with the period `number`=0 and `status`=5 
 
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
### How to handle unexpected error when placing a bet?
If you get any unexpected error upon calling a place bet operation,  that does NOT mean that your bet was not placed.
You must check if the bet was placed by calling the [`bets?uniqueRequestIds={comma separated uniqueRequestIds}`](https://pinnacleapi.github.io/betsapi#operation/Bets_GetBetsByTypeV3).
If you have a retry logic, make sure you reuse the same uniqueRequestId in the place bet request. 
For more details on how uniqueRequestId works, please check [Deduplication](https://github.com/pinnacleapi/pinnacleapi-documentation#deduplication).

### How to handle RESUBMIT_REQUEST error when placing a bet?
This error can occur when trading logic is updating internal parameters. It's not an error on your side , neither is it an API error. Please continue to resubmit your wager until you receive a different response.




###  How to calculate max risk from the max volume limits in `/odds`?

`/odds` operation returns max volume limits.

To calculate the max risk from the max volume , for a price in decimal odds format, you can use this formula:

If  price  > 2  then:  
```
maxRisk = maxVolume  
```
, otherwise when price  < 2: 
```
 maxRisk = maxVolume/(price - 1)

```



##### Example:

When `/odds` return this moneyline offering
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
Max volume is 250.
Home team max risk is 305. 
Away team max risk is 250.

