# スケールアウト
アプリケーションインスタンスのスケールアウト（インスタンス数の増加）を行います。

## 概要 / 説明
Pivotal Cloud Foundry では、簡単な手順でアプリケーションインスタンスの数を変更する事ができます。
スケールアウトの手順と動作を確認します。

## 前提 / 環境
- [バックエンドサービスの利用 - Redis](https://github.com/shinyay/pcf-workshop-service-redis/blob/master/README.md)
  - [事前作業](https://github.com/shinyay/pcf-workshop-prerequisite/blob/master/README.md)

## 手順 / 解説
### アプリケーションの修正
スケールアウトしてインスタンス数が増えた時に、どのインスタンスなのか判別できるようにソースコードを修正します。

- 表示するメッセージにインスタンスに関する環境変数を表示させる
  - `CF_INSTANCE_INDEX`
  - `CF_INSTANCE_IP`

<details><summary>編集済みソースコード</summary>

```
@GetMapping("/")
String hello() {
    return greeter.hello() + " (" + System.getenv("CF_INSTANCE_INDEX") + ")" + " on " + System.getenv("CF_INSTANCE_IP");
}
```
</details>

## まとめ / 振り返り

### 今回のソース
- []()
