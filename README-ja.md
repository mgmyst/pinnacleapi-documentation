<img _ngcontent-c2="" src="https://avatars2.githubusercontent.com/u/31601407?s=70&amp;u=f3c6e1cfc8a26665e4a4df6d8da4a7ee527aeceb&amp;v=4" style="background-color: transparent;"> 

# はじめに

このドキュメントは [pinnacleapi / pinnacleapi-documentation](https://github.com/pinnacleapi/pinnacleapi-documentation) からフォークされた非公式の邦訳ドキュメントです。
オリジナルのドキュメントは[こちら](https://github.com/pinnacleapi/pinnacleapi-documentation)を参照してください。

# 概要

Pinnacle APIは、あらゆるベットタイプでスポーツにベットするためのRESTfulサービスです。

```
api.pinnacle.com
```

#### 認証

APIはHTTPによるBasic認証を使用します。なおAPIにアクセスする際は、必ずHTTPSを使用してください。

HTTPリクエストヘッダには次のようにする必要があります:
```
Authorization: Basic <Base64 value of UTF-8 encoded “username:password”> 
```

例:
```
Authorization: Basic U03MyOT23YbzMDc6d3c3O1DQ1 
```

Pinnacle APIにアクセスするためには、入金済みのアカウントを持っている必要があることに注意してください。

#### データフォーマット

Pinnacle APIはJSONのみをサポートしています。
HTTPヘッダの `Accept` に次のようにセットしてください。

```
    Accept: application/json
```

POSTリクエストの場合は必ずボディコンテンツとしてJSONを持っていなくてはなりません。
またHTTPヘッダの `Content-Type` が次のようにセットされている必要があります。

```
    Content-Type: application/json
```

#### HTTP圧縮

Pinnacle APIはHTTP圧縮をサポートしています。優れたパフォーマンスを得るためにも、原則としてHTTP圧縮を用いることを強く推奨します。
HTTPヘッダに `User-Agent` を設定することを忘れないようにしてください。

#### 日時フォーマット

全ての日時はISO8601準拠のGMTタイムゾーンによって管理されます。

#### 重複排除

クライアントがリクエストを発行した場合に、リクエストがタイムアウトしたり、ベットが受け入れられなかったことを示すエラーステータスコードを返したりする可能性があります。
これにより、同じリクエストが複数回送信されベットの重複が発生する可能性が発生します。

「重複排除」は、リクエストを再試行する際にこのような重複ベットを作らないようにするためのテクニックです。重複排除は自動的に行われます。
ベットが受け入れられた場合、`uniqueRequestId` が30分間システムに保存されます。
その時間内に再度同じ `uniqueRequestId` でベットをしようとすると、適切なエラーが表示されます。

すべてのプレースベットリクエストは重複排除をサポートしています。

# APIリファレンス

**[Pinnacle API Open API Specification](https://github.com/pinnacleapi/OpenAPI-Specification)** はGitHubでホストされています。

APIリファレンスドキュメント:

**[Lines API](https://pinnacleapi.github.io/linesapi)**

**[Bets API](https://pinnacleapi.github.io/betsapi)**

**[Customer API](https://pinnacleapi.github.io/customerapi)**

# その他

[Getting Started](GettingStarted-ja.md)

[API Change Log](original/APIChangelog.md) 

[FAQ](FAQ-ja.md)

[Fair Use Policy](FairUsePolicy-ja.md)

# APIステータス

[pinnacle status page](https://status.pinnacle.com/)をサブスクライブすることでAPIステータスの通知を受け取ることができます。
noreply@mg.pinnacle.com から通知が届きますので、迷惑メールボックスを確認してください。

APIのコンテンツ配信事業者としてCloudflareを利用していますので、その状況は[こちら](https://www.cloudflarestatus.com/)で確認できます。

# 参考

#### R

CRANにある[pinnacle.API](https://cran.r-project.org/web/packages/pinnacle.API/index.html)パッケージを利用してください(install.packages(“pinnacle.API”))。
ソースコードは[こちら](https://github.com/marcoblume/pinnacle.API)を参照してください。

# 免責事項

Pinnacleは、いかなる目的でのAPIの使用についても責任を負いません。
APIは「現状のまま」および「利用可能な状態で」提供され、明示または黙示を問わず、商品性および特定目的への適合性、または非侵害性に関する保証を含むいかなる種類の保証も行いません。