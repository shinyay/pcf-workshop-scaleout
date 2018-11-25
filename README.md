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

### アプリケーションのビルド & デプロイ
#### アプリケーションのビルド
以下のコマンドでアプリケーションをビルドします。
```
$ ./gradlew build -x test
```

#### アプリケーションのデプロイ
アプリケーションのデプロイ (`cf push`) 時にインスタンス数の指定が行えます。
オプションに `-i <インスタンス数>` を加えて `cf push` コマンドを実行します。

```
$ cf push hello-pcf-redis -i 1 -p build/libs/hello-pcf-redis-0.0.1-SNAPSHOT.jar --random-route --no-start
```

<details><summary>実行結果</summary>

```
Pushing app hello-pcf-redis to org syanagihara-org / space development as syanagihara@pivotal.io...
Getting app info...
Creating app with these attributes...
+ 名前:           hello-pcf-redis
  path:           /Users/shinyay/works/workshop/pcf-workshop-scaleout/build/libs/hello-pcf-redis-0.0.1-SNAPSHOT.jar
+ インスタンス:   1
  routes:
+   hello-pcf-redis-quiet-gerenuk.cfapps.io

Creating app hello-pcf-redis...
Mapping routes...
Comparing local files to remote cache...
Packaging files to upload...
Uploading files...
 281.18 KiB / 281.18 KiB [===============================================================================================================================================] 100.00% 2s

Waiting for API to complete processing files...

名前:                   hello-pcf-redis
要求された状態:         stopped
インスタンス:           0/1
使用:                   1G x 1 instances
routes:                 hello-pcf-redis-quiet-gerenuk.cfapps.io
最終アップロード日時:   Sun 25 Nov 14:47:22 JST 2018
スタック:               cflinuxfs2
ビルドパック:
start command:

このアプリの実行インスタンスはありません。

```
</details>

#### サービスインスタンスのバインド
アプリケーションとサービスインスタンスのバインドをします。

```
$ cf bind-service hello-pcf-redis hello-redis
```

### メモリ容量のスケール
デフォルトでは、アプリケーションインスタンスに 1GB のメモリ容量が割り当てられています。
このメモリ容量の変更を行います。

メモリ容量の変更は、以下のコマンドを使用します。

```
$ cf scale -m <メモリ容量> <アプリケーション名>
```

- メモリ容量を 768M に設定

<details><summary>実行結果</summary>

```
$ cf scale -m 768M hello-pcf-redis

このため、このアプリは再始動されます。 hello-pcf-redis をスケーリングしますか?> y

syanagihara@pivotal.io として組織 syanagihara-org / スペース development 内のアプリ hello-pcf-redis をスケーリングしています...
OK

syanagihara@pivotal.io として組織 syanagihara-org / スペース development 内のアプリ hello-pcf-redis を開始しています...
Downloading dotnet_core_buildpack_beta...
Downloading staticfile_buildpack...
Downloaded go_buildpack
Downloading dotnet_core_buildpack...
Downloading ruby_buildpack...
Downloading nodejs_buildpack...
Downloaded dotnet_core_buildpack
Downloading go_buildpack...
Downloaded staticfile_buildpack
Downloading python_buildpack...
Downloaded ruby_buildpack
Downloading php_buildpack...
Downloaded dotnet_core_buildpack_beta
Downloading binary_buildpack...
Downloaded nodejs_buildpack
Downloading java_buildpack...
Downloaded php_buildpack
Downloaded binary_buildpack
Downloaded python_buildpack
Downloaded java_buildpack
Cell d9c94b60-cd3f-4b74-8376-739f07d48b28 creating container for instance b2d708db-8f6a-414d-a6d0-df6886fb2a04
Cell d9c94b60-cd3f-4b74-8376-739f07d48b28 successfully created container for instance b2d708db-8f6a-414d-a6d0-df6886fb2a04
Downloading app package...
Downloaded app package (22.3M)
-----> Java Buildpack v4.16.1 (offline) | https://github.com/cloudfoundry/java-buildpack.git#41b8ff8
-----> Downloading Jvmkill Agent 1.16.0_RELEASE from https://java-buildpack.cloudfoundry.org/jvmkill/trusty/x86_64/jvmkill-1.16.0_RELEASE.so (found in cache)
-----> Downloading Open Jdk JRE 1.8.0_192 from https://java-buildpack.cloudfoundry.org/openjdk/trusty/x86_64/openjdk-1.8.0_192.tar.gz (found in cache)
       Expanding Open Jdk JRE to .java-buildpack/open_jdk_jre (1.2s)
       JVM DNS caching disabled in lieu of BOSH DNS caching
-----> Downloading Open JDK Like Memory Calculator 3.13.0_RELEASE from https://java-buildpack.cloudfoundry.org/memory-calculator/trusty/x86_64/memory-calculator-3.13.0_RELEASE.tar.gz (found in cache)
       Loaded Classes: 15489, Threads: 250
-----> Downloading Client Certificate Mapper 1.8.0_RELEASE from https://java-buildpack.cloudfoundry.org/client-certificate-mapper/client-certificate-mapper-1.8.0_RELEASE.jar (found in cache)
-----> Downloading Container Security Provider 1.16.0_RELEASE from https://java-buildpack.cloudfoundry.org/container-security-provider/container-security-provider-1.16.0_RELEASE.jar (found in cache)
-----> Downloading Spring Auto Reconfiguration 2.5.0_RELEASE from https://java-buildpack.cloudfoundry.org/auto-reconfiguration/auto-reconfiguration-2.5.0_RELEASE.jar (found in cache)
Exit status 0
Uploading droplet, build artifacts cache...
Uploading droplet...
Uploading build artifacts cache...
Uploaded build artifacts cache (132B)
Uploaded droplet (69M)
Uploading complete
Cell d9c94b60-cd3f-4b74-8376-739f07d48b28 stopping instance b2d708db-8f6a-414d-a6d0-df6886fb2a04
Cell d9c94b60-cd3f-4b74-8376-739f07d48b28 destroying container for instance b2d708db-8f6a-414d-a6d0-df6886fb2a04
Cell d9c94b60-cd3f-4b74-8376-739f07d48b28 successfully destroyed container for instance b2d708db-8f6a-414d-a6d0-df6886fb2a04

1 個の中の 0 個のインスタンスが実行中です, 1 個が開始中です
1 個の中の 0 個のインスタンスが実行中です, 1 個が開始中です
1 個の中の 0 個のインスタンスが実行中です, 1 個が開始中です
1 個の中の 0 個のインスタンスが実行中です, 1 個が開始中です
1 個の中の 1 個のインスタンスが実行中です

アプリが開始されました


OK

アプリ hello-pcf-redis はコマンド `JAVA_OPTS="-agentpath:$PWD/.java-buildpack/open_jdk_jre/bin/jvmkill-1.16.0_RELEASE=printHeapHistogram=1 -Djava.io.tmpdir=$TMPDIR -Djava.ext.dirs=$PWD/.java-buildpack/container_security_provider:$PWD/.java-buildpack/open_jdk_jre/lib/ext -Djava.security.properties=$PWD/.java-buildpack/java_security/java.security $JAVA_OPTS" && CALCULATED_MEMORY=$($PWD/.java-buildpack/open_jdk_jre/bin/java-buildpack-memory-calculator-3.13.0_RELEASE -totMemory=$MEMORY_LIMIT -loadedClasses=16268 -poolType=metaspace -stackThreads=250 -vmOptions="$JAVA_OPTS") && echo JVM Memory Configuration: $CALCULATED_MEMORY && JAVA_OPTS="$JAVA_OPTS $CALCULATED_MEMORY" && MALLOC_ARENA_MAX=2 SERVER_PORT=$PORT eval exec $PWD/.java-buildpack/open_jdk_jre/bin/java $JAVA_OPTS -cp $PWD/. org.springframework.boot.loader.JarLauncher` を使用して開始されました

syanagihara@pivotal.io として組織 syanagihara-org / スペース development 内のアプリ hello-pcf-redis の正常性と状況を表示しています...
OK

要求された状態: started
インスタンス: 1/1
使用: 768M x 1 インスタンス
URL: hello-pcf-redis-quiet-gerenuk.cfapps.io
最終アップロード日時: Sun Nov 25 05:47:22 UTC 2018
スタック: cflinuxfs2
ビルドパック: client-certificate-mapper=1.8.0_RELEASE container-security-provider=1.16.0_RELEASE java-buildpack=v4.16.1-offline-https://github.com/cloudfoundry/java-buildpack.git#41b8ff8 java-main java-opts java-security jvmkill-agent=1.16.0_RELEASE open-jd...

     状態   開始日時                 CPU     メモリー             ディスク           詳細
#0   実行   2018-11-25 02:49:21 PM   86.8%   768M の中の 165.7M   1G の中の 150.5M
```
</details>

### インスタンス数のスケール
インスタンス数の変更は、以下のコマンドを使用します。

```
$ cf scale -i <インスタンス数> <アプリケーション名>
```

- インスタンス数を **2** に設定

```
$ cf scale -i 2 hello-pcf-redis
```

#### インスタンス数の確認
`cf apps` コマンドでインスタンス数の確認をします。

```
$ cf apps

syanagihara@pivotal.io として組織 syanagihara-org / スペース development 内のアプリを取得しています...
OK

名前              要求された状態   インスタンス   メモリー   ディスク   URL
hello-pcf-redis   started          2/2            768M       1G         hello-pcf-redis-quiet-gerenuk.cfapps.io
```

インスタンス数が **2/2** になっている事が確認できます。

#### ログの確認
インスタンス数の変更を行った時に出力したログを確認します。

```
$ cf logs hello-pcf-redis --recent
```


<details><summary>実行結果</summary>

1つ目のインスタンスを起動した時のログ

```
   2018-11-25T14:49:03.43+0900 [APP/PROC/WEB/0] OUT   .   ____          _            __ _ _
   2018-11-25T14:49:03.43+0900 [APP/PROC/WEB/0] OUT  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
   2018-11-25T14:49:03.43+0900 [APP/PROC/WEB/0] OUT ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
   2018-11-25T14:49:03.44+0900 [APP/PROC/WEB/0] OUT  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
   2018-11-25T14:49:03.44+0900 [APP/PROC/WEB/0] OUT   '  |____| .__|_| |_|_| |_\__, | / / / /
   2018-11-25T14:49:03.44+0900 [APP/PROC/WEB/0] OUT  =========|_|==============|___/=/_/_/_/
   2018-11-25T14:49:03.44+0900 [APP/PROC/WEB/0] OUT  :: Spring Boot ::        (v2.1.0.RELEASE)

```

2つ目のインスタンスを起動した時のログ

```
   2018-11-25T14:58:36.54+0900 [APP/PROC/WEB/1] OUT   .   ____          _            __ _ _
   2018-11-25T14:58:36.54+0900 [APP/PROC/WEB/1] OUT  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
   2018-11-25T14:58:36.54+0900 [APP/PROC/WEB/1] OUT ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
   2018-11-25T14:58:36.54+0900 [APP/PROC/WEB/1] OUT  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
   2018-11-25T14:58:36.54+0900 [APP/PROC/WEB/1] OUT   '  |____| .__|_| |_|_| |_\__, | / / / /
   2018-11-25T14:58:36.54+0900 [APP/PROC/WEB/1] OUT  =========|_|==============|___/=/_/_/_/
   2018-11-25T14:58:36.54+0900 [APP/PROC/WEB/1] OUT  :: Spring Boot ::        (v2.1.0.RELEASE)
```
</details>



## まとめ / 振り返り

### 今回のソース
- []()
