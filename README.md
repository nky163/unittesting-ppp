
# 第１章 なぜ単体テストを行うのか
## なぜ単体テストを行うのか
- 第一の目標は、ソフトウェアプロジェクトを**持続可能**なものにする、ということ
	- 一般的に、テストがない場合、プロジェクトが大きくなるにつれ、変更に費やす時間は指数関数的に増えていく
		- 一つのバグを修正するのにより多くのバグを見出すことになったりする
	- テストを用意することでコードの変更によるリグレッションを検出するセーフティネットがプロジェクトに備わることになる
	- 新しい機能が追加したり、新たな要求を満たすためのリファクリングに費やすコストを減らせる
- テストの欠点は、テストを用意するのに最初はそれなりの労力を必要とするところ
	- 長い目で見れば、プロジェクトの成長を支援することになるので、後でその労力が報われることになる
- プロジェクトの成長を持続できるようにするには、質の良いテストケースだけを集める必要がある
	- 質の悪いテストケースが多くなると、目標を達成できない
	- 単体テストの価値とその維持にかかるコストの両方を考慮しなければならない
		- コストよりも価値のほうが高いテストスイートを残すことが重要
	- 良い単体テストと悪い単体テストの違いを学ぶことは開発者にとって必要不可欠(第4章で)

## 網羅立とテストスイートの質の関係
- 網羅率では、テストスイートの**質**を評価することはできない
	- 網羅率100％ならば質の良いテスト、とは断言できない
- 理由
	- 網羅率からは実際にテスト対象のコードが検証されたのかを保証できないため
		- コードが実行されるだけでなく、実際に検証されなければテストの目的を果たさない
	- 網羅率を計算する際、使用するライブラリ内のコードは計測の対象から外れるため
- 網羅率の結果に縛られること
	- 開発者を網羅率の数値で縛ると、開発を妨げる危険なものとなることがある
		- たとえ70％とかであっても
	- テストの内容について考えることの妨げになる
	- 網羅率を目標にするのではなく、テストが十分に行われていないことを示す１つの指標として見る必要がある

## 何がテストスイートの質を良くするのか
- 優れたテストスイートの特徴
	- テストすることが開発のサイクルに組み込まれている
		- 理想はコードに変更を加えるたびにテストが実施される状態
	- コードベースの特に重要な部分のみがテスト対象となっている
		- テストによってもたらされる価値は、テスト自体ではなく検証されるコードにある
		- 重要な部分とは、ビジネスロジック、つまり**ドメインモデル**の部分
		- なので、ドメインモデルをコードベースの本質てきでない部分から隔離しておかなければならない(第２部で)
	- 最小限の保守コストで最大限の価値を生み出すようになっている
		- これを実現するには、以下の２つのことを行えなくてはならない
			- 価値のあるテストケースを認識できること
			- 価値のあるテストケースを作成できること
				- そのためには、テスト対象のコードベースが良い設計であることが必要なので、設計のスキルも必要


# 第２章 単体テストとは何か
## 単体テストの定義
- 単体テストの３つの性質
	- 少量のコードを検証する
	- 実行時間が短い
	- 隔離された状態で実行する
## 古典学派およびロンドン学派が考える単体テスト
- **隔離**に関する見解の違いがある
	- ロンドン学派
		- テスト対象となるクラスが他のクラスに依存しているのであれば、その依存をテストダブルに置き換えるべき、という考え
		- テストが失敗したときに、対象オブジェクトで問題が起こったことを明確にできる
		- 複雑なオブジェクトグラフを分離できる
		- １つのテストケースで１つのクラスを検証できる
		- 基本的に全ての依存をテストダブルに置き換える
	- 古典学派
		- コードではなく、**テストケース**を隔離するという考え
		- 実行されるテストケースが他のテストケースに影響を与えることがないようにする（テストが並行に走っても良いようにする）
		- データベースやファイルシステムなどの共有依存だけをテストダブルに置き換える
			- 共有依存への呼び出しは実行時間が長くなるため、統合テストに含める必要がある
		- モックなどのテストダブルがロンドン学派よりも少なくなる

	|    |  隔離対象  |  単体の意味  |  テストダブルの置き換え対象  |
	| ---- | ---- | ---- | ---- |
	|  ロンドン学派  |  単体  |  １つのクラス  |  不変依存を除くすべての依存  |
	|  古典学派  |  テストケース  |  １つのクラス、もしくは、同じ目的を達成するためのクラスのグループ  |  共有依存  |

- ロンドン学派と古典学派の比較
	- 筆者は古典学派の考え方を推している
	- ロンドン学派の以下がメリットとして言われているが、古典学派の観点から見た時にはあまりメリットとは言えない
		- 細かい粒度で検証ができる(１クラス１テストケース)、、、
			- 単体テストでは１単位のコードを検証するのではなく、**１単位の振る舞いを検証するのが質が良いテスト**といえる
			- ここで意味する単体は複数のクラスにまたがることもありうる
			- 単体テストにおいて、各テストケースがすべきことは
				- **そのテストに関わる人たちにテスト対象のコードが何を解決しようとしているかを伝えること**
				- そのためには、非開発者でも理解できるようにすることが必要
				- 詳しくは第５章で
		- 依存関係が複雑になってもテストができる、、、
			- 本来考えるべきことは、膨大で複雑な依存関係を持つクラスを検証するための方法ではなく、
				- そのような複雑な依存関係を構築しなくても住むようにするための方法
			- ほとんどの場合、複雑な依存関係が必要になってしまう誤った設計になっている
		- テストが失敗したとき、どの機能に問題があったか正確に見つけることができる、、、
			- 古典学派の単体テストでもあっても、頻繁にテストを実施していれば、最後に変更した箇所にバグがあることを特定できる
- 古典学派とロンドン学派のテスト駆動開発
	- ロンドン学派
		- 外側から内側へのTDD
		- まずシステム全体がどのように機能するかを考えた広い視野でテストケースを考える
		- 内側をモックを使って、外側のクラスから実装していく
		- **テストが実装の詳細に深く結びつく**ので壊れやすいテストになる
	- 古典学派
		- 内側から外側へのTDD
		- 古典学派では共有依存以外はモックを使わないので、核となるドメインモデルからテストと実装を行う
		- その後に上の層のテストと実装を追加していく
## 古典学派及びロンドン学派における統合(integration)テスト
- **(本書では古典学派の定義を採用している)**
- ロンドン学派
	- 協力者オブジェクトを使って行うテストを統合テストとみなす
	- 古典学派のスタイルで書かれた単体テストはロンドン学派の視点だと統合テストに分類される
- 古典学派
	- 古典学派が考える統合テストは、古典学派が考える単体テストの性質を損なっているもの
	- 古典学派が考えるテストの性質は
		- １単位の振る舞いを検証すること
		- 実行時間が短いこと
		- 他のテストケースから隔離された状態で実行されること
- （統合テストについては第３部で詳しく、、）
- E2Eテスト(総合テスト)
	- 統合テストの一種と考えられるが
	- 統合テストとの違いは、E2Eの方がプロセス外依存を多く含むようになること
		- 明確な基準はないが、一般的に
			- 統合テストは、開発者が完全に制御できない外部サービス(決済サービスとか)はテストダブルを用意する
				- データベースやファイルシステムのような、開発者が管理しているサブシステムは結合してテストを行う
			- E2Eテストは外部サービス(決済サービスとか)も含め全てのプロセス外依存を扱う（エンドユーザの視点でシステムを検証するから）
		- CRUD操作のAPIだけを持つ基本的なシステムの場合、プロセス外依存はデータベースしか登場せず、ユーザーインターフェースも存在しなければ、統合テストとE2Eの違いはなくなる
			- あえて違いをあげるとすると、E2Eテストはアプリケーションのプロセスと別プロセスをホストして、よりエンドユーザの視点に近い条件でテストするところ
	- E2Eテストは保守にコストがかかるので、全ての単体テストと統合テストが成功するようになった後に実施する(ビルドサーバでのみ実施するとか)
```c#
public void Purchase_succeeds_when_enough_inventory() {
	// 準備
	var store = new Store();
	store.AddInventory(Product.Shampoo, 10);
	var customer = new Customer();
	
	// 実行
	bool success = customer.Purchase(store, Product.Shampoo, 5);
	
	// 確認
	Assert.True(success);
	Assert.Equal(5, store.GetInventory(Product.Shampoo));
}
```

```c#
public void Purchase_succeeds_when_enough_inventory() {
	// 準備
	var storeMock = new Mock<IStore>();;
	storeMock
		.Setup(x => x.HasEnoughInventory(Product.Shampoo, 5))
		.Returns(true);
	var customer = new Customer();
	
	// 実行
	bool success = customer.Purchase(storeMock.Object, Product.Shampoo, 5);
	
	// 確認
	Assert.True(success);
	storeMock.Verify(
		x => x.RemoveInventory(Product.Shampoo, 5),	 // このメソッドが１回呼ばれる
		Times.Once);
}
```


# 第３章 単体テストの構造
## 単体テストの構造
- AAAパターン
	- Arrange(準備)
	- Act(実行)
	- Assert(確認)
- テスト駆動開発の場合、Assertから書き始めることで、その機能の振る舞いの見通しが立てやすい
- 単体テストにおいて回避すべきこと
	- 同じフェーズを複数用意すること
		- 準備 -> 実行 -> 確認 -> 別の実行 -> 別の確認
		- これは２つの振る舞いをテストしようとしているので、２つのテストケースに分けるべき
		- テストの目的が分かりにくくなる
	- if文の仕様
		- 単体テストであれ統合テストであれ分岐のない流れにする必要がある
		- テストケースを分割すべき
- 各フェーズ(AAA)の長さ
	- Act(実行)が２行以上になる場合は設計がおかしいかも
		- カプセル化が破綻していないか確認する必要がある
- テストの後始末
	- 単体テストでは、ほとんどの場合、後始末の処理を必要としない
		- 単体テストでは、プロセス外依存とのやり取りは行わないので、破棄しなくてはならないものは残らない
	- 後始末が必要になるのは統合テスト
## 単体テストのフレームワークについて
- **各テストケースは、プロダクションコードが解決しようとしている物語について語るべき**
	- 単体テストでテストべきはプロダクションコードがすることを列挙するのではなく
	- アプリケーションの振る舞いについてより高いレベルで描写すること
	- **理想的には、非開発者にも伝わるようにテストコードを書く**
- テストフィクスチャの準備は慎重に
	- テストケース間で共有しないようにする
		- テストケース間のつながりが強くなるので
		- 個々のテストケースで、準備のフェーズが見えないので、何を検証しているのか分かりにくくなる
	- テストクラスにプライベートなファクトリメソッドを用意してテストケースで呼び出すようにすると良い感じに
	
## 単体テストでの名前のつけ方
- 何を検証するかを明確に説明するために、テストメソッドの名前のつけ方は超重要
- 非常に有名で、おそらく最も役に立たない命名規則↓
	- {テスト対象メソッド名}\_{事前準備}\_{想定する結果}
		- eg) public void Sum_TwoNumbers_ReturnsSum()
	- 振る舞いではなく、実装の詳細に注目しているからダメ
	- 非開発者が見ても分かるような名前にすべき
- テストメソッドに名前を付けるときの指針
	- 厳格な命名規則に縛られないようにする
		- テスト対象の振る舞いに応じて簡潔な言い回しで命名する
	- **非開発者にどのような検証をするのか伝わるように**
	- (英語の場合)アンダースコアを使って単語を区切る
		- 名前が長い時に読みやすくなる
- テスト対象のメソッド名をテストメソッド名に含めるべきでない理由
	- 単体テストはコードをテストしているわけではなく、**単一の振る舞いをテストしているから**
	- テスト対象のメソッド名はテスト対象の振る舞いの入口に過ぎない
	- ただしユーティリティ系のコードをテストする場合は、例外的にＯＫ
		- ユーティリティ系のコードにはビジネスロジックは含まれていないので


## パラメータ化テストへのリファクタリング
- パラメータ化テスト
	- １つの振る舞いを検証するために、複数のテストケースが必要な場合、１つのテストメソッドでテストを簡潔に書くことができる
## Assertフェーズの読みやすさの改善
- Assertionライブラリを使おう


# 第４章 良い単体テストを構成する４本の柱
## 良い単体テストを構成する４本の柱
- リグレッションに対する保護
- リファクタリングへの耐性
- 迅速なフィードバック(テスト準備と実行時間)
- 保守のしやすさ
### 退行(リグレッション)に対する保護
- リグレッションとは、新しい機能を追加した後に、既存の機能が意図したように動かなくなること
- 扱っている機能が増えれば増えるほど、リグレッションのリスクが大きくなる
- リグレッションに対する保護がテストにどのくらい備わっているかを把握する観点
	- テスト時に実行されるプロダクションコードの量
		- 実行されるコードの量が増えれば、リグレッションが見つかる可能性は高くなる
	- そのコードが扱っているドメインの重要性
		- ビジネスロジックを含むコードは確実にテストするようにする
## リファクタリングへの耐性
- リファクタリングとは、観測可能な振る舞いを変えることなく、そのコードを変更すること
- テストコードがテスト対象の機能が正しく振舞っていることを正しく検証できていない場合、リファクタリングでテストが失敗するようになる(**偽陽性**)
	- 偽陽性の発生が多くなると、テストすることの意味を損なってしまう
		- テストの失敗を無視するようになる（たとえそれが本当にバグであっても）
- 何が偽陽性を引き起こすか？
	- テストコードがプロダクションコードとより密接に結びついてしまうと、偽陽性が起きやすくなる
	- テストコードを内部的なコードから切り離す必要がある
		- 検証する対象を**観測可能な振る舞い**とし、その結果を得るための**細かい手順である実装の詳細には目を向けない**ようにする必要がある
- 実装の詳細に目を向けずにテストを書くとは？
	- 以下のMessageRenderクラスのテストを例に考える
```c#
public class Message {
	public string Header {get; set;}
	public string Body {get; set;}
	public string Footer {get; set;}
}

public interface IRenderer {
	string Render(Message message);
}

public class MessageRender : IRenderer {
	
	public IReadOnlyList<IRenderer> SubRenderers {get;}	// 補助的描画クラスのリスト
	
	public MessageRender() {
		SubRenderers = new List<IRenderer> {
			new HeaderRender(),
			new BodyRender(),
			new FooterRender()
		};
	}
	
	public string Render(Message message) {
		return SubRenderers
			.Select(x => x.Render(message))	// 補助的描画クラスのRenderメソッドを呼び出す
			.Aggregate("", (str1, str2) => str1 + str2);	// つなげる
	}
}
```

- MessageRendererクラスが使っているアルゴリズムを分析してテストを考えると以下のようなテストになる
```c#
[Fact] // [Fact]はJUnitの「@Test」に相当するもの
public void MessageRendr_users_correct_sub_renders() {
	var sut = new MessageRender();
	
	IReadOnlyList<IRenderer> renderers = sut.SubRenderers;
	
	Assert.Equal(3, renderers.Count);
	Assert.IsAssignableFrom<HeaderRenderer>(renderers[0]);
	Assert.IsAssignableFrom<BodyRenderer>(renderers[0]);
	Assert.IsAssignableFrom<FooterRenderer>(renderers[0]);
}
```
- これは、MessageRendererクラスの観測可能な振る舞いを検証しているか、、？
	- このテストは、リファクタリングで補助的描画クラスの構成が変わったり、別の補助的描画クラスを使うようにリファクタリングしたときに、テストが失敗するようになるが、それはバグが混入したことを意味しているのか、、？
	- 本来であればMessageRenderresクラスのRenderメソッドの出力が変わらなければ、補助的描画クラスをどのように変更してもバグではないはず
	- このテストはケースは、内部的な実装の一部が想定通りの実装になっているかを検証しているだけで**実行結果を検証していない**
- このような壊れやすいテストは
	- リグレッションが発生したことを知らせる早期の警告が意味を成さなくなる
	- リファクタリングを行う能力や意思を開発者から奪ってしまう
- 実装の詳細ではなく、最終的な結果を確認する
```c#
[Fact]
public void Rendering_a_message() {
	var sut = new MessageRenderer();
	var message = new Message() {
		Header = "h",
		Body = "b",
		Footer = "f"
	};
	
	string html = sut.Render(message);
	
	Assert.Equal("<h1>h</h1><b>b</b><i>f</i>", html);	// 最終的な結果を検証する
}
```
- テスト対象のクラスを**ブラックボックスとして扱うことで、観測可能な振る舞いのみに目を向けることができる**
	- リファクタリングへの耐性がより強く備わる
- 質の良いテストは、最終的な結果が正しいか検証する
- 質の悪いテストは、手順が正しいのか検証する

## リグレッションに対する保護とリファクタリングへの体制との関係
- この二つは本質的に繋がっていて、両方ともテストスイートの正確性を向上することに貢献するもの
	- プロジェクト初期は、リグレッションに対する保護を備えることが重要
	- プロジェクトが成長するにつれて、リファクタリングへの耐性が重要になってくる
		- プロジェクトが成長してくると、コードが増えて複雑になってきてリファクタリングが必要になってくる
		- 偽陽性のことを問題として認識していない開発者が多い
- テストの正確性を最大限にすること
	- テストの正確性についての表
	- リグレッションに対する保護は、実際の振る舞いが間違いなのにテストが成功すること(偽陰性)の最小化
	- リファクタリングに対する耐性は、実際の振る舞いが正しいのにテストが失敗すること(偽陽性)の最小化

## 迅速なフィードバックと保守のしやすさ
- 迅速なフィードバック
	- テストを速やかに行えると、フィードバックを得てから改善するまでの時間が短くなる
	- バグを修正するコストを大幅に減らすことができる
- 保守のしやすさの把握の観点
	- テストケースを理解することがどのくらい難しいか
	- テストを行うことがどのくらい難しいか(依存関係の準備とか)

## 理想的なテストの探求
- テストケースが価値を持つためには、良い単体テストの４本柱を全て備えている必要がある
- ４本柱すべてを最大限に備えることは可能なのか？
	- ４本柱すべてを最大限に備えることは不可能
	- 保守のしやすさ以外の、リグレッションに対する保護、リファクタリングへの耐性、迅速なフィードバックの**３本柱はトレードオフの関係にある**
	- テストケースの価値を最大化するには、それぞれのバランスが重要
	- 優先順位としては
		1. リファクタリングへの耐性
			- ほとんどの場合、リファクタリングへの耐性が「ある」か「ない」かの２択になるため、「ある」を選ぶしかないため最優先になる
		1. リグレッションに対する保護・迅速なフィードバック(実行時間の短さ) はケースバイケース
			- テストの種類(単体、統合、E2E)によって、この２本柱のトレードオフを調整することになる

## ソフトウェア・単体テストにおける良く知られた概念と4本柱
- テストピラミッド
	- 下から順にテストケースの数が多く、上から順にエンドユーザの観点に近い
	1. E2Eテスト
	1. 統合(integration)テスト
	1. 単体テスト
	- どの層でも、リファクタリングへの耐性は必ず最大限備えるようにする -> 偽陽性を可能な限り排除する
	- 階層が異なれば、リグレッションに対する保護と迅速なフィードバックのバランスが異なる
	- E2Eテストは、実行時間が極端に長く、かつ保守コストが高くつく
		- E2Eテストが意味を持つのは、単体テスト、統合テストでは保護を得られない非常に重要な機能に対してテストを行いたいときのみ
		- なので、テストケースは自然と最も少なくなる
- ブラックボックステストとホワイトボックステスト
	- ブラックボックステスト
		- 仕様や要求をもとに作成される
		- テスト対象の処理をどのように(How)行うべきか、ではなく、何(What)をすべきか、の観点で検証される
	- ホワイトボックステスト
		- 仕様や要求からでなく、ソースコードから作成される
		- アプリケーションが内部的に行っていることを検証するために使われる
		- 詳細まで検証できるが、内部的なコードと深く結び付き、壊れやすいテストになる
			- リファクタリングへの耐性が欠落する
	- 単体テスト、統合テスト、E2Eテストのどれでも、まずはブラックボックステストを選択する必要がある
		- もし作成したテストからビジネス要求を導き出せないのであれば、そのテストケースは壊れやすい
	- テストが良いテストかを分析する際は、ホワイトボックステストの観点を活用できる(テストできていない箇所はないか等)
		- でもテストケースを作成するときは、ブラックボックスの観点で
	
# 第５章 モックの利用とテストの壊れやすさ
## モックとスタブの違い
- テストダウブルとは、プロダクトコードに含まれず、テストでしか使わない偽りの依存として表現されるものすべてを包括的に意味するもの
- テストダブルの種類
	- モック
		- テスト対象システムからその依存に向かって行われる**外部に向かう**コミュニケーション(出力)を模倣し、検証に使われる
		- モックが模倣するコミュニケーションは**テスト対象システムが依存の状態を変えるために行うその依存への呼び出し**
		- モックの種類
			- モック...フレームワークやモックライブラリで生成したモック
			- スパイ...自前で用意したモック
	- スタブ
		- 依存からテスト対象システムに向かって行われる**内部に向かう**コミュニケーション(入力)を模倣するのに使われる
		- スタブが模倣するコミュニケーションは**テスト対象システムが依存からデータを取得するために行うその依存への呼び出し**
		- スタブの種類
			- ダミー...ハードコードされた値を返す
			- スタブ...設定によって返却値を変更できる
			- フェイク...機能はスタブと同じだが、フェイクはまだ存在しない依存を置き換えることを目的とする
	- とはいえテストダブルの種類の違いは細かな実装の違いなのでそこまで重要ではない
	- 本書のサンプルコードにおいてはMoqというC#のモックライブラリを使ってMockクラスを作成するが、このMockクラスでモックもスタブも表現する
		- テストケースの中でのMockクラスの使い方次第で、テストダブルとしてのモックなのかスタブなのかが異なる
		- モックのサンプル
		```c#
		[Fact]
		public void Sending_a_greetings_email() {
			var mock = new Mock<IEmailGateway>();
			var sut = new Controller(mock.Object);	// 「sut」は「system user test」の略で、テスト対象を意味する
			
			sut.GreetUser("user@email.com,");
			
			mock.Verify(	// テスト対象システムからモックに対して行われた呼び出しを検証する
				x => x.SendGreetngsEmail("user@email.com"),
				Times.Once
			);
		}
		```
		- スタブのサンプル
		```c#
		[Fact]
		public void Creating_a_report() {
			var stub = new Mock<IDatabase>();
			stub.Setup(x => x.GetNumberOfUsers()).Returns(10);	// スタブが呼び出された時に返却する結果を設定する
			var sut = new Controller(stub.Object);
			
			Report report = sut.CreateReport();
			
			Assert.Equal(10, report.NumberOfUsers);
		}
		```

- スタブとのコミュニケーションは検証してはいけない！
	- テスト対象システムが行うスタブへの呼び出しは、テスト対象システムが生み出す最終的な結果の一部ではなく、結果を生み出す一過程に過ぎないから
	- スタブが行うことは、テスト対象システムが最終的な結果を生成するのに必要なデータを提供する、ということだけ
		- これは内部的な実装の詳細なので、検証することでテストが壊れやすくなる
	- モックのメソッド呼び出しの検証は、最終的な結果の一部(上のサンプルではメールの送信)なので検証する価値がある
	- 最終的な結果の一部とならないものを検証することを**過剰検証**と呼ぶ
		- スタブとのコミュニケーションの検証で発生しやすい
- モック/スタブとコマンド・クエリ分離の原則
	- コマンド・クエリ分離の原則とは
		- 全てのメソッドはコマンドとクエリのどちらかになるべきであり、両方の性質を持つべきではない、という原則
		- 例外はあるがコードの可読性を保つために可能な限り従ったほうがよい
			- コマンド...副作用を起こして、戻り値を持たないメソッド
			- クエリ...いかなる副作用も起こさず、何らかの値を返すメソッド
	- コマンドはモックに紐づき、クエリはスタブに紐づく

## 観察可能な振る舞いと実装の詳細
- 良いテストを構成する４本柱のうち、テストの壊れやすさが影響するのは**リファクタリングへの耐性**
- ほとんどの場合、リファクタリングへの耐性は、**単体テストに備えることができるか否かの選択肢しかない**
	- 統合テスト、E2Eテストでは自然と観測可能な振る舞いを検証することになるので(依存を置き換えることが少ない)
	- 単体テストにおいて最も重要な柱となる
- 単体テストでは統合、E2Eテストの領域に入らないように注意しつつ、リファクタリングへの耐性を最大限備えるようにする必要がある
	- 統合、E2Eテストはリファクタリングへの耐性があるが、保守と実行コストが大きくなるので
- 単体テストにおいては、検証の際に**how**ではなく**what**に目を向けることで、観測可能な振る舞いを検証する
- 観測可能な振る舞いと公開されたAPIの違い
	- プロダクションコードの分類の観点
		- パブリックなAPIか、プライベートなAPIか
		- 観測可能な振る舞いか、実装の詳細なのか
	- 観測可能な振る舞いであるコードは、必ず以下のどちらかである
		- クライアントが目標を達成するために使う公開された**操作**
			- 計算したり、副作用を起こすメソッド
		- クライアントが目標を達成するために使う公開された**状態**
			- システムの現時点でのコンディション
		- これらに該当しない場合、そのコードは**実装の詳細**となる
		- クライアントは何なのか、クライアントの目標は何なのか、で判断が変わる
	- **パブリックなAPIが観測可能な振る舞いと一致**し、**実装の詳細はクライアントから隠れる**ようになっていることが理想的でそうなるように設計する
	- パブリックなAPIが実装の詳細を含んでいる例
	```c#
	public class User {	// ユーザ名は最大50文字までの制限を持ったUserクラス
		public string Name {get; set;}	// ユーザ名のププロパティ
		
		public string NormalizeName(string name) {	// 50文字以上の名前だった場合、それ以降を切り捨てる
			if (name.Length > 50) {
				return name.Substring(0, 50);
			}
			return name;
		}
	}
	
	public class UserController {
		public void RenameUser(int userId, string newName) {	// ユーザの名前変更のAPI
			User user = GetUserFromDatabase(userId);
			string normalizedName = user.NormalizeName(newName);
			user.Name = normalizedName;
			SaveUserToDatabase(user);
		}
	}
	```
	- UserクラスのNameプロパティとNormalizeNmaeメソッドはパブリックなAPIなので観測可能な振る舞いの一部になる必要がある
	- そのため以下のどちらかである必要がある
		- クライアントが目標を達成するために使う公開された**操作**
		- クライアントが目標を達成するために使う公開された**状態**
	- 今回の例だと、UserクラスのNameプロパティだけがこの条件を満たすことになる
		- クライアントであるUserControllerクラスはNameプロパティのsetアクセサを使って、目標であるユーザ名の変更を行うから
		- NormalizeNameメソッドは、UserControllerクラスが達成しようとしていることと直接的には繋がりがない
			- このメソッドはUserクラス自身の不変条件(名前は50文字以内)を達成するための操作なので
	- 実装の詳細の漏洩を修正したサンプル
	```c#
	public class User {
		public string Name {
			get => _name;
			set => _name = NormalizeName(value);
		}
		
		private string NormalizeName(string name) {	// 実装の詳細はプライベートなメソッドにする、、！
			if (name.Length > 50) {
				return name.Substring(0, 50);
			}
			return name;
		}
	}
	
	public class UserController {
		public void RenameUser(int userId, string newName) {
			User user = GetUserFromDatabase(userId);
			user.Name = newName;	// クライアントからは、実装の詳細は呼び出さない、、！
			SaveUserToDatabase(user);
		}
	}
	```
	- クライアントが１つの目標を達成するために、テスト対象の複数のメソッドを呼び出す必要がある場合、実装の詳細が漏れだしている可能性が高い
- きちんと設計されたAPIとカプセル化
	- カプセル化
		- カプセル化は、データの整合性を守るために、不変条件の侵害からコードを守るための手法
		- 不変条件とは、常に「True」でならなければならない条件（UserクラスのNameが50文字以内とか）
	- 上の例のUserクラスはカプセル化の不備で、実装の詳細が漏れだしていた
	- ちなみに、第４章のMessageRenderクラスの壊れやすいテストも、補助的描画クラスのリストを公開していることで、壊れやすいテストを書けてしまった
	- APIをきちんと設計すれば、単体テストは自然と質の良いものになる

## モックの利用とテストの壊れやすさの関係
- ヘキサゴナルアーキテクチャ
	- 一般的にアプリケーションは、ドメイン層とアプリケーションサービス層の２層に分かれる
		- ドメイン層...アプリケーションの核となりビジネスロジックを持つ
		- アプリケーションサービス層...ドメイン層のビジネスロジックをユースケースに結びつける
	- REST APIのアプリケーションの場合、このAPIへの全てのリクエストは最初にアプリケーションサービス層に到達する
		- アプリケーションサービス層は、リクエストに応じた処理を行わせるため、ドメインクラスとプロセス外依存との調整を行う
			1. データベースに対して検索を行い、取得したデータをつかってドメインクラスのインスタンスを生成する
			1. 作成したインスタンスに対して、目的の操作を呼び出す
			1. その結果をデータベースに保存する
	- ヘキサゴナルアーキテクチャの方針
		- ドメイン層とアプリケーションサービス層との関心の分離
			- ドメイン層はビジネスロジックだけを責務として担うべき
			- アプリケーション層は外部アプリケーションやデータベースとのコミュニケーションを行う責務を担う
		- アプリケーション内でのコミュニケーション
			- ドメイン層のクラスはドメイン層のクラスにしか依存してはならない
			- ドメイン層は他の層に依存しないようにする
		- 外部アプリケーションとのコミュニケーション
			- 外部アプリケーションはドメイン層に直接アクセスしてはならに
			- 必ずアプリケーションサービス層の共通IFを介してアクセスする
- ドメイン層とアプリケーションサービス層の観測可能な振る舞い
	- ドメイン層の観測可能な振る舞いは、アプリケーションサービス層が行いたいことを実現するために利用する操作や状態を集めたもの
	- 各層のAPIがきちんと設計されたものになっていると、テストも自己相似的な構造を持つようになる
		- アプリケーションサービスのテストケースでは、外部クライアントによって提示された目標をどのように達成するかを検証する
		- ドメイン層のクラスに対するテストケースでは、その目標の一部をどのように達成するかを検証する
		- つまり、ドメイン層クラスが達成しようとしていることは、外部クライアントが達成しようとしている目標の一部である
	- きちんと設計されたAPIの場合、観測可能な振る舞いだけがテスト対象となるため、テストもビジネス要求と繋がることになる（ユーティリティのテストは例外）
		- 前述のUserクラスとUserControllerクラスの例では、NormalizeNmaeメソッドは**クライアントの要求ではないので実装の詳細**であり、テストすべきではないし、テストできるように設計してはならない
			- テストすべきは、クライアントの目標を達成するためのメソッドであるsetアクセサである
- システム内コミュニケーションとシステム間コミュニケーション
	- システム内コミュニケーション...アプリケーション内のクラス間で行われる
	- システム間コミュニケーション...外部アプリケーションと行う(メール送信サービスとか、外部のWebAPIとか)
	- このうち、システム内コミュニケーションは実装の詳細であり、システム間コミュニケーションはそうではない
		- なぜなら、クライアントから見てドメイン層のクラス同士のコミュニケーションは観測可能な振る舞いの一部にはならないため
		- 依存をモックで差し替えると壊れやすいテストになる
	- システム間コミュニケーションは、**システムの観測可能な振る舞い全体を形成する**
		- テスト対象のアプリケーションがどのように外部とのコミュニケーションをとるのかというのは、**アプリケーションが常に遵守しなければならない契約**の一部である(IFの仕様が決まっている)
	- アプリケーションが外部アプリケーションとの契約を遵守しているかを検証するためにモックが効果を発揮する
		- 副作用で外部システムを呼び出している部分をモックして、その呼び出し方が正しいか(契約に則っているか)を検証する
	- テストの壊れやすさに影響を与えないモックの使用の例
	```c#
	// 商品を購入すると、領収書をメール送信(外部システム)する
	[Fact]
	public void Successful_pruchase() {	// 商品を購入したら領収書をメール送信(外部システム)
		var mock = new Mock<IEmailGateway>;
		var sut = new CustomerController(mock.Object);
		
		bool isSuccess = sut.Purchase(customerId: 1, productId: 2, quantity: 5);
		
		Assert.True(isSuccess);
		mock.Verify(	// アプリケーションが購入に関する領収書をメールで送ったことを確認する(観測可能な振る舞い)
			x => x.SendReceipt("customer@email.com", "Shampoo", "5"),
			Times.Once
		);
	}
	```
	
	- テストの壊れやすくしてしまうモックの利用の例
	```c#
	// 在庫が十分にある場合、購入が成功する
	[Fact]
	public void Purchase_succeds_when_enough_inventory() {
		var storeMock = new Mock(IStore);	// システム内コミュニケーションをモック
		storeMock
			.SetUp(x => x.HasEnoughInventory(Product.Shampoo, 5));
			.Returns(true);
		var customer = new Customer();
		
		bool success = customer.Purchase(storeMock, Product.Shampoo, 5);
		
		Assert.True(success);
		storeMock.Verify(
			x => x.RemoveInventory(Product.Shampoo, 5),	// 実装の詳細を検証している
			Times.Once
		);
	}
	```
	- customerとstoreはドメインクラス同士なのでstoreはモックにしてはいけない
	- この２つのクライアントはCustomerControllerクラスで、クライアントが行いたいことは顧客に商品を購入させること
		- 在庫を減らすことは目標を達成する仮定なので実装の詳細
- 
## 振り返り：単体テストの古典学派とロンドン学派の違い
- モックに置き換える必要のないプロセス外依存
	- 依存の種類
		- 共有依存
			- テストケース間で共有される依存(プロダクションコードで共有されるものではない)
			- 複数のテストケースを同時に実行できるようにするために、モックに置き換える必要がある
			- staticで可変な変数、データベース
		- プロセス外依存
			- テスト対象のプログラムを実行しているプロセスとは異なるプロセスでホストされている依存
			- モックに置き換えるかは、アプリケーションとプロセス外依存との関係次第
			- データベース、メールサービス
		- プライベートな依存
			- テストケース間で共有されない依存(共有依存以外の依存)
			- 実装の詳細なのでモックに置き換えない
			- ドメイン層のクラス同士とか
	- プロセス外依存のモック置き換えの要否
		- **プロセス外依存にアクセスするのに必ずアプリケーションを経由する必要がある場合**、そのコミュニケーションは実質的に**実装の詳細となる**
			- 詳細は第６章と第７章で、、
- モックを使った振る舞いの検証
	- モックは振る舞いを検証するものとして見られることがあるが、ほとんどの場合そのようなことはない
		- クラス間で行われるコミュニケーションは実装の詳細であり、観測可能な振る舞いではないから
	- テストにおいてモックが振る舞いに関与するのは、
		- アプリケーションの境界を超えて行われるコミュニケーションで
		- かつ、そのコミュニケーションによって生じる副作用を外部から見ることができる場合だけ


# 第６章 単体テストの３つの手法
## 単体テストの３つの手法
## 単体テストの３つの手法の比較
## 関数型アーキテクチャについて
## 関数型アーキテクチャおよび出力値ベーステストへの移行
## 関数型アーキテクチャの欠点

# 第７章 単体テストの価値を高めるリファクタリング
## リファクタリングが必要なコードの識別
## 単体テストに価値を持たせるためのリファクタリング
## プロダクションコードの種類に基づく効果的な単体テストの作成
## コントローラにおける条件付きロジックの扱い
## 結論

# 第８章 なぜ統合(integration)テストを行うのか
## 統合テストとは
## どのようなプロセス外依存をモックに置き換えるべきか
## どのように統合(integration)テストを行うのか
## インターフェースを使った依存の抽象化
## 統合テストのベストプラクティス
## ログ出力に対するテスト
## 結論

# 第９章 モックのベストプラクティス
## モックの価値を最大限に引き出す方法
## モックのベストプラクティス

# 第10章 データベースに対するテスト
## データベースをテストするのに必要な事前準備
## データベーストランザクションの管理
## テストデータのライフサイクル
## テストコードの再利用
## データベースを使ったテストに関するよくある疑問

# 第11章 単体テストのアンチパターン
## プライベートなメソッドに対する単体テスト
## プライベートな状態を公開
## テストへのドメイン知識の漏洩
## プロダクションコードへの汚染
## 具象クラスに対するテストダブル
## 単体テストに置ける現在時刻の扱い
## 結論

# メモ
- 説明できるというスキルは非常に重要
	- 自分の考えを他人に明確に伝えられることは価値のある能力であるから
- 単体テストと設計の関係
	- テストをしにくいプロダクションコードは、設計がよくないことが多い
		- コード同士が密結合になっていることで、コードを分離して個別でテストするのが難しい
	- ただし単体テストを作成しやすいからと言って、プロダクションコードに質がいい、と判断することはできない


- 以下の問いに答えられるように
	- テスト対象となるプロダクションコードとともに、テストスイートをリファクタリングするにはどうすればよいか？
	- 異なる手法の単体テストをどのように適用するのか？
	- どのように統合テストを使って、システムの全体の振る舞いを検証するのか
	- 単体テストにおけるアンチパターンをどのように識別し、それを回避するのか