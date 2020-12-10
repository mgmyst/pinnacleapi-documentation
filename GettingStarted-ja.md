<img _ngcontent-c2="" src="https://avatars2.githubusercontent.com/u/31601407?s=70&amp;u=f3c6e1cfc8a26665e4a4df6d8da4a7ee527aeceb&amp;v=4" style="background-color: transparent;"> 

# はじめかた

##### ステップ 1 - サインアップ

はじめるにあたり、まずはアカウントを作成する必要があります。
またPinnacle APIを利用するためには、そのアカウントに入金がされていなくてはならないことを忘れないでください。

##### ステップ 2 - 提供されているスポーツやリーグの一覧を取得する

次に、Get Sports(_注:Pinnacle APIで提供されているAPIのひとつ。以下、API登場時は単に『注:API』とする_)を利用してスポーツのリストを取得しましょう。
もし特定のリーグに興味があるならば、Get Leagues(_注:API_)を利用することによって、すべてのスポーツリーグを取得することができます。

##### ステップ3 - ベットする

ベットの方法については、後述するストレートベットとパーレイベットのセクションを参照してください。

##### ステップ 4 - ベットの状態を確認する

現在のベットの状態を確認するには、Get Bets(_注:API_)を利用します。
複数の利用方法がありますが、betIdを使用することを推奨します。

### ストレートベットの仕方 

##### ステップ 1 - Get Fixtures(_注:API_)を利用する

これは現在提供されているイベントのリストを返します。更新情報を取得するには、deltaリクエスト(sinceパラメータ付き)を使用します。

##### ステップ2 - Get Odds(_注:API_)を利用する

これは現在提供されているオッズのリストを返します。更新情報を取得するには、deltaリクエスト(sinceパラメータ付き)を使用します。

##### ステップ3 - Get Line(_注:API_)(optional)を利用する

正確なリミットが必要な場合や、特定のラインに興味がある場合は、Get Lineを利用してください。Get Odds応答のリミットは一般的なリミットであることに注意してください。

##### ステップ 5 - ベットする

ベットするには、Place Bet(_注:API_)を利用する必要があります。

次の表はPlace BetとGet Lineリクエストに対するGet Odds操作のレスポンスをマッピングする方法を示しています。

<table width="100%">
<tbody>
<tr><th width="30%"><strong>Parameter</strong></th><th width="70%"><strong>Get Odds response parameter</strong></th></tr>
<tr>
<td>sportId</td>
<td>sportId</td>
</tr>
<tr>
<td>leagueId</td>
<td>League Type -&gt; id</td>
</tr>
<tr>
<td>eventId</td>
<td>Event Type -&gt; id</td>
</tr>
<tr>
<td>periodNumber</td>
<td>Period Type -&gt; number</td>
</tr>
<tr>
<td>team</td>
<td>
<p>Depends on selected odds from:<br><br>· Spread Type<br>· Moneyline Type<br>· Team Total Points<br><br>check the value in the corresponding Get Leagues Response -&gt; League Type -&gt; homeTeamType and set the appropriate value.</p>
<p><strong>Example 1:</strong><br><br>Given:<br>&nbsp;&nbsp; &nbsp;homeTeamType="Team1"<br>When:<br>&nbsp;&nbsp; &nbsp;Selected odds is Spread Type -&gt; away<br>Then:<br>&nbsp;&nbsp; &nbsp;team=Team2<br><br><strong>Example 2:</strong></p>
<p>When:<br>&nbsp;&nbsp; &nbsp;Selected odds is Moneyline Type -&gt; draw<br>Then:<br>&nbsp;&nbsp; &nbsp;team=Draw<br><br><strong>Example 3:</strong><br><br>Given:<br>&nbsp;&nbsp; &nbsp;homeTeamType="Team2"<br>When:<br>&nbsp;&nbsp; &nbsp;Selected odds is Team Total Points Type -&gt; away<br>Then:<br>&nbsp;&nbsp; &nbsp;team=Team1</p>
</td>
</tr>
<tr>
<td>handicap</td>
<td>Spread Type -&gt; hdp<br>Total Points Type -&gt; points<br>Team Total Points Type -&gt; Total Points Type -&gt; points</td>
</tr>
<tr>
<td>lineId</td>
<td>Period Type -&gt; lineId</td>
</tr>
<tr>
<td>altLineId</td>
<td>Spread Type -&gt;altLineId<br>Total Points Type -&gt; altLineId <br><br></td>
</tr>
</tbody>
</table>

**重要**:
ベットする際には、Get LineまたはGet Oddsレスポンスの `lineId` と `altLineId` の両方を使用してください。
価格が代替ラインの価格であったときに `altLineId` パラメータを省略していたならば、ベットはメインラインに置かれることになります。

### パーレーベットの仕方 

##### ステップ 1 - Get Fixtures(_注:API_)を利用する

これは現在提供されているイベントのリストを返します。更新情報を取得するには、deltaリクエストを使用します(sinceパラメータを使用)。

##### ステップ 2 – Get Odds(_注:API_)を利用する

これは現在提供されているオッズのリストを返します。更新情報を取得するには、deltaリクエストを使用します(sinceパラメータを使用)。

##### ステップ 3 – Get Parlay Lines(_注:API_)を利用する

ベットしたいイベントとベットタイプごとに、Get Parlay Lines用のLegオブジェクトを構築し、以下のようにしてリクエストを送信します。

```
POST /line/parlay
-> If response contains Invalid Legs – remove them and resubmit the request
-> If response has status = ‘VALID’ – place parlay bet request can be created
```


##### ステップ 4 – Place Parlay Bet(_注:API_)を利用する

Get Parlay LinesレスポンスからlineId値を使用してLegsリストを構築し、Get Parlay Linesレスポンスで返されたもののうちroundRobbinOptionsを指定します。

### ティーザーベットの仕方

##### ステップ 1 – Get Teaser Groups(_注:API_)を利用する

これは各ティーザーの詳細を含むグループ別のティーザーのリストを返します。例えば、最小/最大のLegs数、選択したティーザーのペイアウトの組み合わせ、各ティーザーのリーグなどです。

##### ステップ 2 – Get Teaser Odds(_注:API_)を利用する

これは与えられたティーザーに現在提供されている調整済みポイントのリストを返します。
 
##### ステップ 3 – オプション : Get Teaser Lines(_注:API_)を利用する

ティザーベットを行う前に、このエンドポイントを呼び出して提案されたベットの有効性を確認し、有効な最小/最大勝率/リスクのベットリミットを（実際にベットすることなく）計算することができます。

##### ステップ 4 – Place Teaser Bet(_注:API_)を利用する

以上のステップで得た情報を用いて、Place Teaser Betを利用し実際にベットを行っていきます。

### スペシャルベットの仕方

##### ステップ 1 – Get Special Fixtures(_注:API_)を利用する

This will return the list of specials that are currently offered. To get updates use delta requests (with since parameter)

これは現在提供されているスペシャルベットのリストを返します。更新情報を取得するには、deltaリクエストを使用します(sinceパラメータを使用)。

##### ステップ 2 – Get Special Odds(_注:API_)を利用する

これは現在提供されているスペシャルオッズのリストを返します。更新情報を取得するにはdeltaリクエストを使用します(sinceパラメータを使用します)。
 
##### ステップ 3 – オプション : Get Special Lines(_注:API_)を利用する

スペシャルベットをする前にこのAPIを利用し、提案されたベットの有効性を確認したうえで、有効な最小/最大勝率/リスクのベットリミットを（実際にベットすることなく）計算することができます。

##### ステップ 4 – Place Special Bet(_注:API_)を利用する

以上のステップで得た情報を用いて、Place Special Betを利用し実際にベットを行っていきます。
