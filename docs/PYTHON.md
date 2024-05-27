## 1 対多のサンプル

```python
from typing import TYPE_CHECKING
from sqlalchemy.orm import Mapped, declarative_mixin, declared_attr, relationship


if TYPE_CHECKING:
    from app.models.orders import Order

@declarative_mixin
class UserMixin:
    __tablename__ = "users"

    id = Column(
        BigInteger,
        primary_key=True,
    )
    email = Column(
        String(255),
        nullable=False,
        unique=True,
        comment="メールアドレス",
    )
    password = Column(
        String(255),
        nullable=False,
        comment="パスワード",
    )

    @declared_attr
    def orders(cls) -> Mapped["Order"]:
        return relationship("Order", backref="user")

@declarative_mixin
class OrderMixin:
    __tablename__ = "orders"

    id = Column(
        BigInteger,
        primary_key=True,
    )
    name = Column(
        String(255),
        nullable=False,
        comment="名前",
    )
    user_id = Column(
        BigInteger,
        ForeignKey("users.id"),
        nullable=False,
    )
```

## メモ

- app/models のみモジュール化して使用するようにするように統一する？
  - リレーションの関係から、モジュールにした方がいい気がする
- jwt のアルゴリズムを HS256->RS256(cognito と同じ)にする
- 復習を兼ねて terraform のコードを追加する
- ~~シーダーの導入、ファクトリーの導入~~
- ~~DB リセットコマンドの作成~~
- エラーハンドラー追加
- 必要なミドルウェアを設定する
- クラス化する、クラス化しないものを精査する
  - ファイル名がモジュール名を解釈すれば、わざわざ class 化しなくても良い気がする
  - いろいろなコードを見てどちらが python らしいか確認する
- モノレポの弊害
  - 各プロジェクトで import 文に名前空間のエラーが出る python の方はライブラリの参照元に飛べない。node の方(php も)は飛べるので何か方法はあるはず 解決方法としてコンテナにアタッチして vscode を開いて対応している workspace というのを使用してそれぞれのフォルダ内に./vscode/settings.json を置けばうまくいくことは確認できたが複数開かれるので微妙かもしれない いったんは workspace は使用せずに対応しようと思う workspace を使用するには下記のファイルのコメントアウトを外して、メニューの「ファイル > ファイルでワークスペースを開く」で project.code-workspace を選択する
    apps/admin-api/.vscode
    apps/user-api/.vscode
    project.code-workspace

## フォルダ構成の説明

factory: database/factories
migration: database/migrations
外部と連携するファイル: app/services
便利な関数(プロジェクト全体で使用が可能なもの): app/helpers
モデル: app/models
enum: app/enums
ログ: logs
テストケース: tests
ドキュメントをまとめるフォルダ: doc
