# wolfSSLのビルド

wolfSSLは移植性を念頭に置いて書かれており、一般的にほとんどのシステムで容易にビルドできるはずです。wolfSSLのビルドで問題が生じた場合は、遠慮なくサポートフォーラム（<https://www.wolfssl.com/forums>）でサポートを求めるか、[support@wolfssl.com](mailto:support@wolfssl.com)まで直接ご連絡ください。

本章では、UnixおよびWindows上でwolfSSLをビルドする方法を説明し、非標準環境でwolfSSLをビルドする際のガイダンスを提供します。「はじめに」ガイドは[第3章](chapter03.md#getting-started)に、SSLチュートリアルは[第11章](chapter11.md#ssl-tutorial)にあります。

autoconf / automakeシステムを使用してwolfSSLをビルドする場合、wolfSSLは単一のMakefileを使用してライブラリのすべての部分とサンプルをビルドします。これはMakefileを再帰的に使用するよりも簡単で高速です。

## wolfSSLソースコードの取得

wolfSSLの最新バージョンは、wolfSSLウェブサイトからZIPファイルとしてダウンロードできます。

<https://wolfssl.jp/download/>

ZIPファイルをダウンロードした後、`unzip`コマンドを使用してファイルを解凍します。ネイティブの行末文字を使用するには、unzipの実行時に`-a`修飾子を有効化してください。unzipのmanページでは、`-a`修飾子の機能は次のように説明されています。

> [...] The -a option causes files identified by zip as text files (those with
> the ‘t’ label in zipinfo listings, rather than ‘b’) to be automatically
> extracted as such, converting line endings, end-of-file characters
> and the character set itself as necessary. [...]

**注記**: wolfSSL 2.0.0rc3のリリース以降、wolfSSLのディレクトリ構造と標準インストール場所が変更されました。これらの変更は、オープンソースプロジェクトがwolfSSLを統合しやすくするために行われました。ヘッダーおよび構造の変更に関する詳細は、[ライブラリヘッダー](chapter09.md#library-headers)および[Structure Usage](#structure-usage)をご覧ください。

## Unix系システムでのビルド

Linux、\*BSD、OS X、Solaris、その他の\*nix系システムでwolfSSLをビルドする場合は、autoconfシステムを使用します。wolfSSLをビルドするには、wolfSSLのルートディレクトリから`./configure`と`make`の2つのコマンドを実行するだけです。

`./configure`スクリプトはビルド環境をセットアップします。`./configure`には任意の数のビルドオプションを追加できます。利用可能なビルドオプションの一覧については、[ビルドオプション](#build-options)を参照するか、次のコマンドを実行して`./configure`スクリプトに渡すことができるオプションの一覧を確認してください。

```sh
./configure --help
```

`./configure`が正常に実行された後、wolfSSLをビルドするには次を実行します。

```sh
make
```

wolfSSLをインストールするには次を実行します。

```sh
make install
```

インストールにはスーパーユーザー権限が必要な場合があります。その場合はコマンドの前にsudoを付けてください。

```sh
sudo make install
```

ビルドをテストするには、wolfSSLのルートディレクトリからtestsuiteプログラムを実行します。

```sh
./testsuite/testsuite.test
```

または、autoconfを使用してtestsuiteに加えて標準のwolfSSL APIおよび暗号テストを実行することもできます。

```sh
make test
```

testsuiteプログラムの期待される出力の詳細については、[テストスイートの節](chapter03.md#testsuite)をご覧ください。wolfSSLライブラリのみをビルドし、追加の項目（サンプル、testsuite、ベンチマークアプリなど）をビルドしない場合は、wolfSSLのルートディレクトリから次のコマンドを実行できます。

```sh
make src/libwolfssl.la
```

## Windowsでのビルド

以下の手順に加えて、Visual StudioでwolfSSLをビルドするための手順とヒントを[こちら](https://wolfssl.com/wolfSSL/Docs-wolfssl-visual-studio.html)でご覧いただけます。

### VS 2008

インストールのルートディレクトリには、Visual Studio 2008用のソリューションが含まれています。Visual Studio 2010以降で使用する場合、既存のプロジェクトファイルはインポート処理の際に変換できるはずです。

**注記**:
新しいバージョンのVSにインポートする場合、「Do you want to overwrite the project and its imported property sheets?（プロジェクトとインポートされたプロパティシートを上書きしますか？）」と尋ねられます。「No」を選択することで以下の作業を回避できます。「Yes」を選択した場合は、`SAFESEH`の指定により`EDITANDCONTINUE`が無視されるという警告が表示されます。testsuite、sslSniffer、server、echoserver、echoclient、clientをそれぞれ個別に右クリックし、Properties-\>Configuration Properties-\>Linker-\>Advanced（Advancedウィンドウで一番下までスクロール）を変更する必要があります。「Image Has Safe Exception Handlers」を探し、右端のドロップダウン矢印をクリックします。前述の各項目についてこれをNo（`/SAFESEH:NO`）に変更してください。もう1つの選択肢は`EDITANDCONTINUE`を無効化することですが、これはデバッグ目的で有用であることがわかっているため、製品ソフトウェアには推奨しません。

### VS 2010

更新されたwolfSSLソリューションをビルドするには、Service Pack 1をダウンロードする必要があります。VSがリンカーエラーを報告した場合は、プロジェクトをクリーンしてリビルドしてください。リンカーエラーは解消されるはずです。

### VS 2013 (64 bit solution)

更新されたwolfSSLソリューションをビルドするには、Service Pack 4をダウンロードする必要があります。VSがリンカーエラーを報告した場合は、プロジェクトをクリーンしてリビルドしてください。リンカーエラーは解消されるはずです。

各ビルドをテストするには、Visual Studioのメニューから「Build All」を選択し、testsuiteプログラムを実行します。Visual Studioプロジェクトでビルドオプションを編集するには、対象のプロジェクト（wolfssl、echoclient、echoserverなど）を選択し、「Properties」パネルを開きます。

**注記**: wolfSSL v3.8.0リリース以降、ビルドのプリプロセッサマクロは`IDE/WIN/user_settings.h`にある一元化されたファイルに移動されました。このファイルはプロジェクト内でも見つけられます。ECCやChaCha20/Poly1305などの機能を追加するには、ここに`HAVE_ECC`や`HAVE_CHACHA` / `HAVE_POLY1305`などの`#defines`を追加してください。

### Cygwin

Windows開発マシン上でWindows用のwolfSSLをビルドする場合は、付属のVisual StudioプロジェクトファイルでwolfSSLをビルドすることを推奨します。ただし、Cygwinが必要な場合のために、当社チームがビルドに成功した手順の概要を以下に示します。

1. <https://www.cygwin.com/install.html>にアクセスし、`setup-x86_64.exe`をダウンロードします
2. `setup-x86_64.exe`を実行し、任意の方法でインストールします。「Select Packages」の段階に到達するまでインストールメニューをクリックして進めます。
3. 「+」アイコンをクリックして「All」を展開します
4. 次に「Archive」セクションに移動し、「unzip」のドロップダウンを選択して「Skip」を6.0-15（または他のバージョン）に変更します。
5. 「Devel」の下で「autoconf」のドロップダウンをクリックし、「Skip」を「10-1」（または他のバージョン）に変更します
6. 「Devel」の下で「automake」のドロップダウンをクリックし、「Skip」を「10-1」（または他のバージョン）に変更します
7. 「Devel」の下で「gcc-core」のドロップダウンをクリックし、「Skip」を7.4.0-1に変更します（注記: wolfSSLはGCC 9および10をテストしておらず、これらはかなり新しいため、開発向けにもう少し調整される時間が経つまで使用を推奨しません）。
8. 「Devel」の下で「git」のドロップダウンをクリックし、「Skip」を2.29.0-1（または他のバージョン）に変更します
9. 「Devel」の下で「libtool」のドロップダウンをクリックし、「Skip」を「2.4.6-5」（または他のバージョン）に変更します
10. 「Devel」の下で「make」のドロップダウンをクリックし、「Skip」を4.2.1-1（または他のバージョン）に変更します
11. 「Next」をクリックし、残りのインストールを進めます。

追加パッケージの一覧には以下が含まれます。

* unzip
* autoconf
* automake
* gcc-core
* git
* libtool
* make

#### インストール後

Cygwinターミナルを開き、wolfSSLをクローンします。

```sh
git clone https://github.com/wolfssl/wolfssl.git
cd wolfssl
./autogen.sh
./configure
make
make check
```

## 各種ベンダーIDEでのビルド

wolfSSLウェブサイトから完全なwolfSSLソースコードパッケージをダウンロードして展開し、以下の設定を適用します。

### 1. wolfSSLライブラリビルドプロジェクト

- **コンパイル対象のソースファイルを登録**  
  `./src`配下のすべての`*.c`ファイルと`./wolfcrypt/src`配下のすべての`*.c`ファイルを登録します。  
  次のファイルは除外します。

./src/ssl_.c, ./src/x509_.c,
src/conf.c, src/bio.c
wolfcrypt/src/misc.c, ./wolfcrypt/src/evp.c

- **設定オプションを定義**  
設定の定義を`user_settings.h`という名前のヘッダーファイルに格納します。

参照:
- `wolfssl/examples/configs/README.md`
- `wolfssl/examples/configs/user_settings_template.h`

- **定義済みマクロ名を登録**  
`WOLFSSL_USER_SETTINGS`が定義されている場合、ビルド時に上記の`user_settings.h`ファイルがインクルードされます。

- **インクルードパスを登録**  
wolfSSLソースファイルのルートへのパスと、上記の`user_settings.h`へのパスを登録します。

---

### 2. アプリケーション実行ファイルビルドプロジェクト

- 手順1で生成したwolfSSLライブラリをリンク対象として登録します。
- 手順1で定義したwolfSSLソースファイルのルートパスと設定オプションのパス（`user_settings.h`）をインクルードパスとして登録します。
- 定義済みマクロ名として`"WOLFSSL_USER_SETTINGS"`を指定します。
- アプリケーションのプログラム先頭付近で "wolfssl/wolfcrypt/settings.h" をインクルードします。




## 非標準環境でのビルド

公式にはサポートされていませんが、非標準環境、特に組み込みシステムやクロスコンパイルシステムでwolfSSLをビルドしたいユーザーを支援するよう努めています。以下は、その手始めとなるいくつかの注意点です。

1. ソースファイルとヘッダーファイルは、wolfSSLダウンロードパッケージと同じディレクトリ構造のままにしておく必要があります。
2. 一部のビルドシステムでは、wolfSSLヘッダーファイルの場所を明示的に知る必要があるため、それを指定する必要がある場合があります。ヘッダーファイルは`<wolfssl_root>/wolfssl`ディレクトリにあります。通常、`<wolfssl_root>`ディレクトリをインクルードパスに追加することで、ヘッダーの問題を解決できます。
3. wolfSSLは、configure処理がビッグエンディアンを検出しない限り、デフォルトでリトルエンディアンシステムを想定します。非標準環境でビルドするユーザーはconfigure処理を使用しないため、ビッグエンディアンシステムを使用する場合は`BIG_ENDIAN_ORDER`を定義する必要があります。
4. wolfSSLは64ビット型が利用可能であると速度面で恩恵を受けます。configure処理はlongまたはlong longが64ビットであるかを判定し、そうであれば定義をセットアップします。したがって、お使いのシステムで`sizeof(long)`が8バイトであれば、`SIZEOF_LONG 8`を定義してください。そうではなく`sizeof(long long)`が8バイトであれば、`SIZEOF_LONG_LONG 8`を定義してください。
5. ライブラリのビルドを試み、問題が発生した場合はお知らせください。サポートが必要な場合は、[facts@wolfssl.com](mailto:facts@wolfssl.com)までご連絡ください。
6. ビルドを変更できる定義の一部を、以下のサブセクションに列挙します。多くのオプションのより詳しい説明については、[ビルドオプション](#build-options)をご覧ください。

### Yocto Linuxへのビルド

wolfSSLには、Yocto LinuxおよびOpenEmbedded上でwolfSSLをビルドするためのレシピも含まれています。これらのレシピは、GitHubリポジトリのmeta-wolfSSLレイヤー内で保守されています: <https://github.com/wolfSSL/meta-wolfssl>。Yocto Linux上でwolfSSLをビルドするには、Gitとbitbakeが必要です。以下の手順は、（レシピが存在する）いくつかのwolfSSL製品をYocto Linux上でビルドする方法を示しています。

1. **wolfSSL metaのクローン**

    これは、次のURLに対するgit-cloneコマンドで行えます。
    <https://github.com/wolfSSL/meta-wolfssl>

2. **ビルドのbblayers.confに「meta-wolfSSL」レイヤーを挿入**

    `BBLAYERS`セクション内に、meta-wolfsslをクローンした場所へのパスを
    追加します。例:

    ```sh
    BBLAYERS ?= "... \
    /path/to/meta-wolfssl/ \
    ..."
    ```

3. **wolfSSL製品レシピのビルド**

    bitbakeを使用して、次の3つのwolfSSL製品レシピのいずれかをビルドできます:
    *wolfssl*、*wolfssh*、*wolfmqtt*。これらのレシピのいずれかをbitbakeコマンドに
    渡すだけです（例: `bitbake wolfssl`）。これにより、ユーザー自身が
    問題なくコンパイルが成功することを確認できます。

4. **local.confの編集**

    最後の手順は、ビルドのlocal.confファイルを編集することです。これにより、ビルドされる
    イメージに目的のライブラリを含めることができます。`IMAGE_INSTALL_append`の行を
    編集して、目的のレシピの名前を含めます。以下にその例を示します。

    ```sh
    IMAGE_INSTALL_append = "wolfssl wolfssh wolfmqtt"
    ```

イメージがビルドされると、wolfSSL（またはレシピによる関連製品）のデフォルトの配置場所は`/usr/lib/`ディレクトリになります。

さらに、[ビルドオプション](#build-options)に列挙されているenable/disableオプションを使用することで、Yoctoへのビルド時にwolfSSLをカスタマイズできます。これには、`.bbappend`ファイルを作成し、wolfSSLアプリケーション/レシピレイヤー内に配置する必要があります。このファイルの内容には、`EXTRA_OECONF`変数に連結する内容を指定する行を含める必要があります。TLS 1.3の有効化オプションによってTLS 1.3サポートを有効化する例を以下に示します。

```sh
EXTRA_OECONF += "--enable-tls13"
```

Yoctoへのビルドに関するさらなるドキュメントは、meta-wolfsslのREADMEにあります: <https://github.com/wolfSSL/meta-wolfssl/blob/master/README.md>

### Atollic TrueSTUDIOでのビルド

3.15.5以降のバージョンのwolfSSLには、ARM M4-Cortexデバイス上でwolfSSLをビルドするために使用されるTrueSTUDIOプロジェクトファイルが含まれています。TrueSTUDIOプロジェクトファイルはSTM32デバイス上でのビルド処理を簡素化するもので、無料でダウンロードでき、ST Microelectronicsの一部門であるAtollicによって作成されています。TrueSTUDIOでwolfSSL静的ライブラリプロジェクトファイルをビルドするには、TrueSTUDIOを開いた後、ユーザーは次の手順を実行する必要があります。

1. プロジェクトをワークスペースにインポートする（File > Import）
2. プロジェクトをビルドする（Project > Build project）

ビルドの際には、`user_settings.h`内にある設定がビルド時に取り込まれます。`user_settings.h`ファイルのデフォルトの内容は最小限であり、多くの機能は含まれていません。ユーザーはこのファイルを変更し、本章の残りの部分に列挙されているオプションを使用して機能を追加または削除できます。

### IARでのビルド

`<wolfssl_root>/IDE/IAR-EWARM`ディレクトリには次のファイルが含まれています。

1. ワークスペース: `wolfssl.eww`
   このワークスペースには、wolfSSL-Libライブラリと、wolfCrypt-test、wolfCrypt-benchmark
   実行ファイルのプロジェクトが含まれます。
2. wolfSSL-Libプロジェクト: `lib/wolfSSL-lib.ewp`
   wolfCryptおよびwolfSSL関数のフルセットのライブラリを生成します。
3. テストスイートプロジェクト: `test/wolfCrypt-test.ewp`
   テストスイート実行ファイルtest.outを生成します
4. ベンチマークプロジェクト: `benchmark/wolfCrypt-benchmark.ewp`
   ベンチマーク実行ファイルbenchmark.outを生成します

これらのプロジェクトは汎用のARM Cortex-M MPU向けにセットアップされています。特定のターゲットMPU向けのプロジェクトを生成するには、次の手順を実行します。

1. デフォルト設定: プロジェクトのデフォルトターゲットはCortex-M3シミュレータに設定されています。user_settings.hにはプロジェクトのデフォルトオプションが含まれています。ビルドしてシミュレータに
ダウンロードできます。「view」->「Terminal I/O」でTerminal I/Oウィンドウを開き、実行を開始してください。

2. プロジェクトオプションの設定: 各プロジェクトについて、適切な「Target」オプションを選択します。

3. 実行ファイルプロジェクトの場合: お使いのMPU用の「SystemInit」と「startup」を追加し、デバッグ用の「Driver」を選択します。

4. ベンチマークプロジェクトの場合: current_time関数のオプションを選択するか、WOLFSSL_USER_CURRTIMEオプションを使用して独自の「current_time」ベンチマークタイマーを作成します。

5. ビルドとダウンロード: メニューバーの「Project->Make」および「Download and Debug」に進み、EWARMでのビルドとダウンロードを行います。

### OS XおよびiOSでのビルド

#### XCODE

`<wolfssl_root>/IDE/XCODE`ディレクトリには次のファイルが含まれています。

1. `wolfssl.xcworkspace` -- ライブラリとテストスイートクライアントを含むワークスペース
2. `wolfssl_testsuite.xcodeproj` -- テストスイートを実行するプロジェクト
3. `wolfssl.xcodeproj` -- wolfSSLおよび/またはwolfCryptのOS/xおよびiOSライブラリをビルドするプロジェクト
4. `wolfssl-FIPS.xcodeproj` -- 利用可能な場合にwolfSSLおよびwolfCrypt-FIPSをビルドするプロジェクト
5. `user_settings.h` -- プロジェクト間で共有されるカスタムライブラリ設定

ライブラリは、ターゲットに応じて`libwolfssl_osx.a`または`libwolfssl_ios.a`として出力されます。また、wolfSSL/wolfCrypt（およびCyaSSL/CtaoCrypt
互換）のヘッダーが、
`Build/Products/Debug`または`Build/Products/Release`内の`include`ディレクトリにコピーされます。

ライブラリとテストスイートが正しくリンクされるためには、ビルドの場所をワークスペースからの相対位置に設定する必要があります。

1. File -> Workspace Settings（または Xcode -> Preferences -> Locations -> Locations）
2. Derived Data -> Advanced
3. Custom -> Relative to Workspace
4. Products -> Build/Products

これらのXcodeプロジェクトは`WOLFSSL_USER_SETTINGS`プリプロセッサを定義しており、複数のプロジェクトにまたがってマクロを設定するための`user_settings.h`ファイルを有効化しています。

必要に応じて、Xcodeのプリプロセッサは次の手順で変更できます。

1. 「Project Navigator」でプロジェクトをクリックします。
2. 「Build Settings」タブをクリックします。
3. 「Apple LLVM 6.0 - Preprocessing」セクションまでスクロールダウンします。
4. 「Preprocessor Macros」の展開表示を開き、「+」と
「-」のボタンで変更します。DebugとReleaseの両方に対して行うことを忘れないでください。

このプロジェクトは、デフォルト設定でwolfSSLとwolfCryptをビルドするはずです。

### GCC ARMでのビルド

`<wolfssl_root>/IDE/GCC-ARM`ディレクトリには、Cortex Mシリーズ向けのwolfSSLサンプルプロジェクトがありますが、他のアーキテクチャにも適用できます。

1. `gcc-arm-none-eabi`がインストールされていることを確認します。
2. `Makefile.common`を修正します:
   * 正しいツールチェーンパス`TOOLCHAIN`を使用します。
   * 正しいアーキテクチャ'ARCHFLAGS'を使用します。[GCC ARM Options](https://gcc.gnu.org/onlinedocs/gcc-4.7.3/gcc/ARM-Options.html)の`-mcpu=name`を参照してください。
   * linker.ld内のメモリマップがお使いのflash/ramと一致することを確認するか、Makefile.common内の`SRC_LD = -T./linker.ld`をコメントアウトします。
3. `make`を使用して、静的ライブラリ（libwolfssl.a）、wolfCryptテスト/ベンチマーク、wolfSSL TLSクライアントの各ターゲットを`.elf`および`.hex`として`/Build`にビルドします。

#### 汎用makefileによるクロスコンパイルでのビルド

Cortex-A53搭載のRaspberry Pi向けの`Makefile.common`の変更例:

1. Makefile.commonで`ARCHFLAGS`を`-mcpu=cortex-a53 -mthumb`に変更します。
2. カスタムメモリマップは適用されないため、`SRC_LD`をコメントアウトします。
3. デフォルトの`gcc`が使用されるように`TOOLCHAIN`をクリアします。`TOOLCHAIN =`と設定します
4. nosysおよびnano向けの`LDFLAGS += --specs=nano.specs`と`LDFLAGS += --specs=nosys.specs`をコメントアウトします。

#### configureによるクロスコンパイルでのビルド

メインプロジェクトディレクトリのconfigureスクリプトは、gcc-arm-none-eabiツールを
使用してクロスコンパイルビルドを実行できます。ツールが実行パスに
インストールされていると仮定すると、次のようになります。

```sh
./configure \
  --host=arm-none-eabi \
  CC=arm-none-eabi-gcc \
  AR=arm-none-eabi-ar \
  STRIP=arm-none-eabi-strip \
  RANLIB=arm-none-eabi-ranlib \
  --prefix=/path/to/build/wolfssl-arm \
  CFLAGS="-march=armv8-a --specs=nosys.specs \
      -DHAVE_PK_CALLBACKS -DWOLFSSL_USER_IO -DWOLFSSL_NO_SOCK -DNO_WRITEV" \
  --disable-filesystem --enable-crypttests \
  --disable-shared
make
make install
```

32ビットアーキテクチャ向けにビルドする場合は、CFLAGSのリストに`-DTIME_T_NOT_64BIT`を
追加してください。

### Keil MDK-ARMでのビルド

Keil MDK-ARM上でwolfSSLをビルドするための詳細な手順とヒントは[こちら](https://www.wolfssl.com/docs/keil-mdk-arm/)でご覧いただけます。

**注記**: MDK-ARMがデフォルトのインストール場所にインストールされていない場合、プロジェクトファイル内のすべての参照パス定義を
インストール場所に変更する必要があります。

## Cプリプロセッサマクロとして定義される機能

### 機能の削除

以下の定義は、wolfSSLから機能を削除するために使用できます。ライブラリ全体のフットプリントサイズを削減しようとする場合に役立ちます。`NO_<feature-name>`の定義を行うことに加えて、対応するソースファイルもビルドから削除できます（ただしヘッダーファイルは削除できません）。

#### NO_WOLFSSL_CLIENT

クライアント固有の呼び出しを削除します。サーバー専用ビルド向けです。サイズ削減のためにいくつかの呼び出しを削除したい場合にのみ使用してください。

#### NO_WOLFSSL_SERVER

同様に、サーバー側固有の呼び出しを削除します。

#### NO_DES3

DES3暗号化の使用を削除します。一部の古いサーバーがまだDES3を使用しており、SSL 3.0で必須であるため、DES3はデフォルトで組み込まれています。`NO_DH`と`NO_AES`は上記2つと同様で、広く使用されています。

#### WOLFSSL_DES_ECB

DES-ECB（Electronic Codebook）モードを有効化します。デフォルトではオフです。一部のレガシープロトコルで使用されます。

#### WC_ASYNC_ENABLE_3DES

wolfSSL非同期暗号フレームワークを介した非同期3DES操作を有効化します。

#### FREESCALE_LTC_DES

Freescale/NXP LTCハードウェアによるDES/3DESアクセラレーションを有効化します。

#### NO_DSA

DSAを削除します。DSAは一般的な用途から段階的に廃止されつつあるためです。

#### NO_ERROR_STRINGS

エラー文字列を無効化します。エラー文字列は、wolfSSLでは`src/internal.c`に、wolfCryptでは`wolfcrypt/src/asn.c`にあります。

#### NO_HMAC

HMACをビルドから削除します。

**注記**: SSL/TLSはHMACに依存していますが、wolfCryptのみを使用する場合、すなわちビルドオプション`WOLFCRYPT_ONLY`を使用する場合には、HMACを無効化できます。

#### HAVE_HKDF

RFC 5869に基づくHKDF（HMAC-based Extract-and-Expand Key Derivation Function）を有効化します。TLS 1.3の鍵導出、および共有秘密からの汎用的な鍵導出に使用されます。

#### WOLFSSL_HMAC_COPY_HASH

各HMAC操作のたびに再初期化する代わりに、ハッシュ状態をコピーします。同じ鍵を複数のHMAC計算に使用する場合のパフォーマンスが向上します。

#### STM32_HMAC

STM32 HASHペリフェラルを使用したSTM32ハードウェアHMACアクセラレーションを有効化します。

#### WC_ASYNC_ENABLE_HMAC

wolfSSL非同期暗号フレームワークを介した非同期HMAC操作を有効化します。

#### WOLFSSL_DEVCRYPTO_HMAC

Linuxの`/dev/crypto`インターフェースを介したHMACアクセラレーションを有効化します。

#### WOLFSSL_KCAPI_HMAC

Linuxカーネル暗号API（AF_ALG）を介したHMAC操作を有効化します。

#### NO_MD4

MD4をビルドから削除します。MD4は破られており、使用すべきではありません。

#### NO_MD5

MD5をビルドから削除します。

#### HAVE_MD5_CUST_API

カスタムMD5 APIを有効化します。ユーザー提供のMD5実装を使用できます。

#### STM32_NOMD5

STM32ハードウェアMD5アクセラレーションを無効化します。STM32暗号が有効化されているものの、MD5にはソフトウェア実装を使用すべき場合に使用します。

#### WC_ASYNC_ENABLE_MD5

wolfSSL非同期暗号フレームワークを介した非同期MD5操作を有効化します。

#### NO_SHA

SHA-1をビルドから削除します。

#### NO_SHA256

SHA-256をビルドから削除します。

#### NO_PSK

事前共有鍵拡張の使用をオフにします。デフォルトで組み込まれています。

#### NO_PWDBASED

PBKDF1、PBKDF2、およびPKCS #12のPBKDFなど、パスワードベースの鍵導出関数を無効化します。

#### HAVE_PKCS7

署名データ、エンベロープデータ、暗号化データ、圧縮データの各コンテンツタイプに対するPKCS#7（Cryptographic Message Syntax）サポートを有効化します。

#### NO_PKCS7_STREAM

PKCS#7ストリーミングモードを無効化します。ストリーミングモードは、PKCS#7データを逐次的に処理できるようにするものです。

#### NO_PKCS7_ENCRYPTED_DATA

PKCS#7のEncryptedDataコンテンツタイプを無効化します。SignedDataまたはEnvelopedDataのみが必要な場合にコードサイズを削減します。

#### NO_PKCS7_COMPRESSED_DATA

PKCS#7のCompressedDataコンテンツタイプを無効化します。CompressedDataサポートが有効化されている場合（すなわち、このマクロが定義されていない場合）、zlib（[`HAVE_LIBZ`](#have_libz)）が必要です。

#### WC_PKCS7_STREAM_DEBUG

PKCS#7ストリーミング操作のデバッグ出力を有効化します。

#### WOLFSSL_PKCS7_MAX_DECOMPRESSION

PKCS#7の解凍出力の最大サイズを設定します。悪意を持って作成された圧縮データによるメモリ枯渇を防止します。

#### HAVE_PKCS7_RSA_RAW_SIGN_CALLBACK

PKCS#7におけるraw RSA署名用のカスタムコールバックを有効化します。HSMまたは外部署名との統合を可能にします。

#### HAVE_PKCS7_ECC_RAW_SIGN_CALLBACK

PKCS#7におけるraw ECC署名用のカスタムコールバックを有効化します。HSMまたは外部署名との統合を可能にします。

#### HAVE_X963_KDF

ANSI X9.63鍵導出関数を有効化します。PKCS#7 EnvelopedDataにおけるECCベースの鍵共有に使用されます。

#### NO_RC4

ARC4ストリーム暗号の使用をビルドから削除します。ARC4は今も普及しており広く使用されているため、デフォルトで組み込まれています。

#### NO_SESSION_CACHE

セッションキャッシュが不要な場合に定義できます。これにより、メモリ使用量が約3 kB削減されるはずです。

#### NO_TLS

TLSをオフにします。TLSをオフにすることは推奨しません。

#### SMALL_SESSION_CACHE

wolfSSLが使用するSSLセッションキャッシュのサイズを制限するために定義できます。これにより、デフォルトのセッションキャッシュが33セッションから6セッションに削減され、約2.5 kBを節約できます。

#### NO_RSA

RSAアルゴリズムのサポートを削除します。

#### WC_NO_RSA_OAEP

OAEPパディングのコードを削除します。

#### WOLFSSL_RSA_VERIFY_INLINE

インラインRSA検証を有効化し、出力をコピーする代わりに入力バッファ内へのポインタを返します。RSA検証操作のメモリ使用量を削減します。

#### WC_RSA_DIRECT

直接RSA暗号化/復号API（`wc_RsaDirect`）を有効化します。パディングなしの生のRSA操作を提供し、カスタムプロトコルに役立ちます。

#### WC_RSA_NO_PADDING

パディングなしRSAモードを有効化します。パディングスキームを一切適用せずにRSA操作を行えます。使用には注意が必要です。通常はカスタム実装専用です。

#### WOLFSSL_RSA_KEY_CHECK

`wc_CheckRsaKey()`によるRSA鍵ペアの整合性チェックを有効化します。公開鍵と秘密鍵の構成要素が数学的に整合していることを検証します。

#### WOLFSSL_RSA_CHECK_D_ON_DECRYPT

各復号操作の前にRSA秘密指数`d`を検証します。パフォーマンスと引き換えに、フォールトインジェクション攻撃に対する追加のセキュリティを提供します。

#### WOLFSSL_RSA_DECRYPT_TO_0_LEN

RSA復号操作が長さゼロの結果（空の平文）を返すことを許可します。デフォルトでは、長さゼロの復号結果はエラーとして扱われます。

#### NO_RSA_BOUNDS_CHECK

RSA入力データの境界チェックを無効化します。デフォルトでは、wolfSSLは入力値がRSAモジュラスより小さいことを検証します。

#### SHOW_GEN

RSA鍵生成中の進捗インジケータ（ドット）を有効化します。時間のかかる鍵生成操作中のユーザーフィードバックに役立ちます。

#### WOLFSSL_PSS_LONG_SALT

RSA-PSS署名がハッシュ出力長より長いソルト長を使用することを許可します。一部の実装では、鍵サイズからオーバーヘッドを引いた値に等しいソルト長を使用します。

#### WOLFSSL_PSS_SALT_LEN_DISCOVER

RSA-PSS署名検証時のPSSソルト長の自動検出を有効化します。異なるソルト長を試して一致するものを見つけます。

#### WC_RSA_NONBLOCK_TIME

時間ベースのノンブロッキングRSA操作を有効化します。RSA操作が設定可能な時間の経過後に処理を明け渡すことを可能にします。[`WC_RSA_NONBLOCK`](#wc_rsa_nonblock)が必要です。

#### WOLFSSL_MP_INVMOD_CONSTANT_TIME

定数時間のモジュラ逆元計算を使用します。RSA秘密鍵操作中のタイミングサイドチャネル攻撃から保護します。

#### WC_RSA_NO_FERMAT_CHECK

RSA鍵生成中のフェルマー因数分解近接チェックを無効化します。デフォルトでは、wolfSSLは`p`と`q`が近すぎないことを検証します。近すぎると、鍵がフェルマーの因数分解法に対して脆弱になります。

#### FP_MAX_BITS

高速数学（`USE_FAST_MATH`）使用時の最大鍵サイズをビット単位で設定します。値は鍵サイズの2倍に設定する必要があります。例えば、RSA 3072の場合は6144に設定します。デフォルトは4096です（RSA 2048までをサポート）。

#### WOLFSSL_HAVE_SP_RSA

RSA操作に対するSingle Precision（SP）数学最適化を有効化します。SP数学は、一般的な鍵サイズ（2048、3072、4096）に対して大幅なパフォーマンス向上を提供します。

#### WOLFSSL_SP_ASM

アセンブリ最適化されたSP数学ルーチンを有効化します。サポートされるプラットフォーム（x86_64、ARM、RISC-V）上で、RSA、ECC、DH操作に対して最大限のパフォーマンスを提供します。

#### WC_ASYNC_ENABLE_RSA

wolfSSL非同期暗号フレームワークを介した非同期RSA操作を有効化します。RSA操作をハードウェアアクセラレータにオフロードできます。

#### WOLFSSL_KCAPI_RSA

Linuxカーネル暗号API（AF_ALG）を介したRSA操作を有効化します。RSAをカーネルの暗号サブシステムにオフロードします。

#### WOLFSSL_AFALG_XILINX_RSA

Xilinx暗号ハードウェアを使用したXilinxプラットフォーム上でのAF_ALGを介したRSAアクセラレーションを有効化します。

#### WOLFSSL_SE050_NO_RSA

NXP SE050セキュアエレメントを介したRSAを無効化します。SE050のその他の操作は引き続き利用できます。

#### WOLFSSL_XILINX_CRYPT

Xilinx FPGA/SoCプラットフォーム上で、RSAおよびその他のアルゴリズムに対するXilinxハードウェア暗号アクセラレーションを有効化します。

#### NO_AES_CBC

AES-CBCアルゴリズムのサポートをオフにします。

#### NO_AES_DECRYPT

コードサイズを削減するために設定できます。復号を無効化して暗号化のみをサポートするように設定します。

#### WOLFCRYPT_ONLY

TLSを無効化し、wolfCryptのみを有効化します。

#### NO_CAMELLIA_CBC

Camellia CBCサポートを無効化しますが、TLS暗号スイートにのみ適用されます。

#### NO_AES

AESアルゴリズムのサポートを無効化します。

#### NO_AES_128

コンパイル時のAES鍵サイズ選択に使用されます。

#### NO_AES_192

コンパイル時のAES鍵サイズ選択に使用されます。

#### NO_AES_256

コンパイル時のAES鍵サイズ選択に使用されます。

#### NO_AESGCM_AEAD

AES GCMを使用するTLS暗号スイートを無効化するために使用されます。AES GCM暗号スイートが1つも有効化されていない場合に内部的に使用されますが、暗号スイートを制限するために使用することもできます。

#### NO_ASN_TIME

ASNの時刻チェックを無効化します。注記: すべての証明書の開始/終了日付チェックがスキップされるため、使用には注意が必要です。


#### NO_BIO

BIO（Basic I/O）抽象化レイヤーを無効化します。`BIO_new()`、`BIO_read()`、`BIO_write()`などのBIO機能が不要な場合にコードサイズを削減します。

#### WOLFSSL_CERT_PIV

政府向けスマートカードアプリケーション用のPIV（Personal Identity Verification）証明書サポートを有効化します。

#### WOLFSSL_CERT_GEN_CACHE

証明書生成時のDERエンコーディングをキャッシュし、再エンコードせずに再利用できるようにします。

#### WOLFSSL_CERT_SIGN_CB

証明書署名用のコールバック関数を有効化し、外部での署名操作（HSMなど）を可能にします。

#### WOLFSSL_CERT_NAME_ALL

イニシャル、名（given name）、DN修飾子を含むすべての証明書名構成要素を格納します。より多くのメモリを使用しますが、名前への完全なアクセスを提供します。

#### WOLFSSL_MULTI_ATTRIB

証明書における複数値の相対識別名（RDN）属性を有効化します。

#### WOLFSSL_DER_TO_PEM

DERからPEM形式への変換関数を有効化します。

#### WOLFSSL_PUB_PEM_TO_DER

公開鍵のPEMからDER形式への変換を有効化します。

#### WOLFSSL_KEY_TO_DER

RSAおよびECC秘密鍵の鍵からDERへのエンコード関数を有効化します。

#### ASN_BER_TO_DER

BER（Basic Encoding Rules）からDER（Distinguished Encoding Rules）への変換を有効化します。BERエンコードされた証明書やCMS/PKCS#7データの解析に必要です。

#### WOLFSSL_DUP_CERTPOL

証明書ポリシー拡張における証明書ポリシーOIDの重複を許可します。デフォルトでは、重複は検証エラーになります。

#### NO_VERIFY_OID

証明書解析中のOID検証を無効化します。

#### NO_SKID

Subject Key Identifier拡張の処理を無効化します。

#### NO_STRICT_ECDSA_LEN

厳格なECDSA署名長チェックを緩和します。相互運用性のために、ECDSA署名の非最小DERエンコーディングを許可します。

#### ALLOW_SELFSIGNED_INVALID_CERTSIGN

Key Usage拡張にkeyCertSignビットが設定されていない自己署名証明書を許可します。

#### ALLOW_V1_EXTENSIONS

X.509 v1証明書が拡張を含むことを許可します。RFC 5280によれば、拡張はv3証明書でのみ有効です。


#### USE_WOLF_VALIDDATE

プラットフォームの実装の代わりに、wolfSSL独自の日付検証実装を使用します。


#### USE_WOLF_TM

システム提供の定義の代わりに、wolfSSL独自の`struct tm`定義を使用します。標準の`struct tm`を持たないプラットフォームで必要です。

#### WC_ASN_RUNTIME_DATE_CHECK_CONTROL

証明書の日付チェックの実行時制御を有効化します。APIを介して実行時に日付検証を有効化または無効化できるようになります。

#### WOLFSSL_AFTER_DATE_CLOCK_SKEW

証明書のnot-after日付チェックに対するクロックスキュー許容値（秒単位）を設定します。有効期限をわずかに過ぎた証明書を有効として扱えるようにします。

#### WOLFSSL_BEFORE_DATE_CLOCK_SKEW

証明書のnot-before日付チェックに対するクロックスキュー許容値（秒単位）を設定します。有効期間の開始よりわずかに前の証明書を受け入れられるようにします。

#### NO_WOLFSSL_SKIP_TRAILING_PAD

ASN.1解析における末尾パディングバイトのスキップを無効化します。


#### NO_WOLFSSL_STUB

未実装のOpenSSL互換関数のスタブ実装を無効化します。スタブがない場合、未実装の関数を呼び出すと、実行時エラーではなくリンカーエラーになります。

#### WOLFSSL_ALT_NAMES

証明書の生成および解析におけるSubject Alternative Name（SAN）サポートを有効化します。

#### WOLFSSL_ALT_NAMES_NO_REV

Subject Alternative Nameを順序を反転せずに格納します。デフォルトでは、SANは逆順で格納されます。


#### WOLFSSL_ALTERNATIVE_DOWNGRADE

標準的なアプローチの代わりに、代替のプロトコルダウングレード検出メカニズムを使用します。

#### WOLFSSL_IP_ALT_NAME

Subject Alternative NameにおけるIPアドレスエントリを有効化します。


#### WOLFSSL_JNI

Java JNI（wolfSSL JNI/JSSE）互換性に必要なAPIおよび動作を有効化します。

#### WOLFSSL_RID_ALT_NAME

Subject Alternative NameにおけるRegistered IDエントリを有効化します。

#### WOLFSSL_EKU_OID

標準セット以外の追加OIDに対するExtended Key Usage OIDサポートを有効化します。

#### WOLFSSL_ACERT

X.509属性証明書サポート（RFC 5755）を有効化します。

#### IGNORE_KEY_EXTENSIONS

証明書検証時にkey usageおよびextended key usage拡張を無視します。

#### WOLFSSL_ALLOW_CRIT_AIA

Authority Information Access（AIA）拡張がcriticalとしてマークされることを許可します。デフォルトでは、criticalなAIAは検証エラーになります。

#### WOLFSSL_ALLOW_CRIT_AKID

Authority Key Identifier拡張がcriticalとしてマークされることを許可します。

#### WOLFSSL_ALLOW_CRIT_SKID

Subject Key Identifier拡張がcriticalとしてマークされることを許可します。


#### WOLFSSL_ALLOW_MAX_FRAGMENT_ADJUST

初回のTLSネゴシエーション完了後に、最大フラグメントサイズの実行時調整を許可します。


#### WOLFSSL_ALLOW_NO_SUITES

暗号スイートが利用できない場合でもSSL/CTXオブジェクトの作成を許可します。通常は、ビルド構成に一致するスイートがない場合にエラーが発生します。


#### WOLFSSL_ALLOW_NO_CN_IN_SAN

Subject Alternative Name（SAN）拡張を持つが、サブジェクトにCommon Name（CN）を持たない証明書を許可します。

#### WC_ASN_UNKNOWN_EXT_CB

未知の証明書拡張を処理するためのコールバックを有効化します。コールバックはカスタム処理のためにOIDと拡張データを受け取ります。

#### WOLFSSL_ASN_ALL

すべてのオプションのASN.1機能を一括で有効化します。

#### WOLFSSL_ASN_CA_ISSUER

Authority Information Access拡張のCA Issuerフィールドの解析を有効化します。

#### WOLFSSL_ASN_PRINT

DERエンコードされたデータのデバッグと検査のためのASN.1構造出力関数を有効化します。

#### WOLFSSL_ASN_INT_LEAD_0_ANY

ASN.1 INTEGERエンコーディングにおいて任意の先頭ゼロバイトを許可します。デフォルトでは、最小エンコーディングのみが受け入れられます。

#### WOLFSSL_ASN_PARSE_KEYUSAGE

証明書処理中のKey Usage拡張の解析を有効化します。

#### WOLFSSL_ASN_TIME_STRING

ASN.1時刻値の人間が読める文字列形式への変換を有効化します。

#### ASN_TEMPLATE_SKIP_ISCA_CHECK

ASN.1テンプレートベースの証明書解析におけるisCAチェックをスキップします。

#### HAVE_OID_ENCODING

ドット区切り10進表記からASN.1 OIDを生成するためのOID（Object Identifier）エンコーディングサポートを有効化します。

#### WOLFSSL_OLD_OID_SUM

古いOID合計計算方式を使用します。特定のOID合計値に依存するアプリケーションとの後方互換性のためのものです。


#### WOLFSSL_OPENVPN

wolfSSLにおけるOpenVPN互換動作を有効化します。OpenVPNのTLSプロバイダーとしてwolfSSLを使用する場合に必要です。

#### HAVE_OCSP_RESPONDER

OCSPレスポンダー機能を有効化します。wolfSSLがOCSPレスポンスに署名して送信するOCSPレスポンダーとして動作できるようになります。

#### WOLFSSL_OCSP_PARSE_STATUS

詳細なステータス情報のためのOCSPレスポンスステータスフィールドの解析を有効化します。

#### HAVE_PKCS8

暗号化および非暗号化秘密鍵のインポートとエクスポートのためのPKCS#8秘密鍵フォーマットサポートを有効化します。

#### HAVE_PKCS12

秘密鍵、証明書、およびCAチェーンを単一の暗号化ファイルにまとめるためのPKCS#12（PFX）フォーマットサポートを有効化します。

#### WOLFSSL_DILITHIUM_NO_ASN1

Dilithium鍵および署名のASN.1エンコード/デコードを無効化します。代わりに生（raw）フォーマットを使用します。


#### WOLFSSL_DISABLE_EARLY_SANITY_CHECKS

受信TLSメッセージの早期サニティチェックを無効化します。非準拠のTLS実装との相互運用性のために必要になる場合があります。

#### WOLFSSL_DILITHIUM_FIPS204_DRAFT

FIPS 204ドラフト版のDilithiumパラメータを有効化します。

#### HAVE_SPHINCS

SPHINCS+耐量子署名スキームのサポートを有効化します。

#### WC_ENABLE_ASYM_KEY_IMPORT

Ed25519、Ed448、Curve25519、およびCurve448用の汎用非対称鍵インポート関数を有効化します。

#### WC_ENABLE_ASYM_KEY_EXPORT

Ed25519、Ed448、Curve25519、およびCurve448用の汎用非対称鍵エクスポート関数を有効化します。

#### WOLFSSL_X509_NAME_AVAILABLE

OpenSSL互換の証明書名アクセスのためのX509_NAME APIを有効化します。

#### WOLFSSL_HAVE_ISSUER_NAMES

効率的なアクセスのために、発行者名の構成要素へのポインタ、その長さ、およびエンコーディングを格納します。

#### WOLFSSL_ASN_KEY_SIZE_ENUM

ASN.1処理におけるAES鍵サイズの表現に、生の整数の代わりにenumを使用します。

#### HAVE_SMIME

電子メールの署名と暗号化のためのS/MIME（Secure/Multipurpose Internet Mail Extensions）サポートを有効化します。

#### WC_RC2

RC2暗号サポートを有効化します。主にレガシーPKCS#12ファイルとの互換性のために必要です。

#### WOLFSSL_MD2

MD2ハッシュアルゴリズムサポートを有効化します。レガシー証明書との互換性のためにのみ必要です。新規アプリケーションには推奨されません。

#### NO_CHECK_PRIVATE_KEY

このマクロは、デフォルトで有効になっている追加の秘密鍵チェックを無効化します。このチェックは、秘密鍵が公開鍵とペアであることを検証するものです。RSA、ECDSA、ED25519、ED448、Falcon、Dilithium、Sphincsでサポートされています。

#### NO_CIPHER_SUITE_ALIASES

暗号スイート名のエイリアスを無効化します。主要な暗号スイート名のみが認識され、代替名は認識されなくなります。

#### NO_CHAPOL_AEAD

ChaCha20-Poly1305 AEAD暗号スイートを無効化します。アルゴリズム自体はコンパイルされていても、ChaCha20-Poly1305サポートが不要な場合に使用します。

#### NO_DH

Diffie-Hellman（DH）サポートを無効化します。

#### NO_ED25519_CLIENT_AUTH

ED25519のTLSクライアント認証サポートを無効化します。ED25519はメッセージのキャッシュを必要とするため、ED25519を使用しない場合にTLS時のメモリ使用量を削減するために使用されます。

#### NO_ED448_CLIENT_AUTH

ED448のクライアント認証を無効化します。

#### NO_FORCE_SCR_SAME_SUITE

デフォルトでは、セキュアリネゴシエーションには同一の暗号スイートの使用が必要です。このマクロはその要件を無効化します。

#### NO_MULTIBYTE_PRINT

組込みデバイスで問題となる可能性のある特殊文字をコンパイル対象から除外するために使用されます。

#### NO_OLD_SSL_NAMES

wolfSSLとOpenSSLを併用するための古いOpenSSL互換マクロの一部を無効化します。

#### NO_OLD_WC_NAMES

不要な名前空間を削除します。

#### NO_OLD_POLY1305

主に相互運用のために使用される、古いChaCha20/Poly1305 TLS 1.2暗号スイートのサポートを無効化します。

#### NO_HANDSHAKE_DONE_CB

`wolfSSL_SetHsDoneCb`で設定されるハンドシェイクコールバックのサポートを無効化します。このオプションはコードサイズの削減に有用です。

#### NO_STDIO_FILESYSTEM

stdio.hのインクルードを無効化します。移植性のために使用されます。

#### NO_TLS_DH

TLS DHを除外します。エフェメラルな有限体Diffie-Hellman鍵合意に基づく暗号スイートをネゴシエートしないようにします。

#### NO_WOLFSSL_CM_VERIFY

Certificate Manager検証コールバックを無効化します。検証コールバックは、エラーをインターセプトして上書きすることを可能にするものです。このオプションはコードサイズの削減に有用です。

#### NO_WOLFSSL_DIR

ディレクトリサポートを無効化します。

#### NO_WOLFSSL_RENESAS_TSIP_TLS_SESSION

TSIP TLS連携の共通鍵暗号化方式のみを無効化するためのものです。注記: これはRenesas RX TSIP固有の定義です。

#### NO_WOLFSSL_SHA256

TLS 1.3のみに適用されます。SHA2-256をwolfCryptで有効化して使用可能にしつつ、TLS 1.3からは除外できるようにします。

#### WOLFSSL_BLIND_PRIVATE_KEY

秘密鍵をブラインドするためのマスクとして使用されます。このブラインディングはRowhammer攻撃から保護するために使用されます。

#### WOLFSSL_DTLS13_NO_HRR_ON_RESUME

定義された場合、DTLSサーバーはクライアントの再開（レジューム）が成功した際にクッキー交換を行いません。再開は高速化され（RTTが1回減少）、帯域幅の消費も少なくなります（ClientHelloが1回、HelloVerifyRequest/HelloRetryRequestが1回減少）。一方で、有効なSessionID/チケット/PSKが収集された場合、偽造されたclientHelloメッセージがサーバーのリソースを消費することになります。DTLS 1.3では、このオプションを使用することでサーバーがEarly Data/0-RTT Dataを処理することも可能になります。このオプションがない場合、サーバーはクッキー付きの検証済みClientHelloを受信するまでステートフル処理に入らないため、Early Dataは破棄されます。クッキー交換なしでDTLS 1.3の再開を許可するには、次のようにします。- `WOLFSSL_DTLS13_NO_HRR_ON_RESUME`を定義してwolfSSLをコンパイルする - WOLFSSLオブジェクトに対してwolfSSL_dtls13_no_hrr_on_resume(ssl, 1)を呼び出し、再開時のクッキー交換を無効化する - 通常の接続と同様に処理を続ける。


#### WOLFSSL_DTLS13_SEND_MOREACK_DEFAULT

損失の多いネットワークでの信頼性向上のため、DTLS 1.3でより多くのACKメッセージをデフォルトで送信するようにします。

#### WOLFSSL_NO_CLIENT_AUTH

Ed25519およびEd448の使用に必要なキャッシュコードを無効化します。

#### WOLFSSL_NO_CURRDIR

wolfssl/test.h内のテストパスで./をサポートしないプラットフォームのための移植性マクロです。テストツールのみに適用されます。

#### WOLFSSL_NO_DEF_TICKET_ENC_CB

デフォルトのチケット暗号化コールバックを無効化します（サーバーのみ）。セッションチケットを使用するには、アプリケーションが独自のコールバックを設定する必要があります。


#### WOLFSSL_NO_DTLS_SIZE_CHECK

DTLSレコードサイズの検証チェックを無効化します。非標準のレコードサイズを送信する実装との相互運用性のために必要になる場合があります。


#### WOLFSSL_NO_ETM_ALERT

Encrypt-Then-MAC拡張のネゴシエーションが失敗した際のアラートメッセージを抑制します。暗黙的にMAC-then-encryptにフォールバックします。

#### WOLFSSL_NO_SOCK

組込みのソケットサポートを無効化するための移植性マクロです。ソケットなしでTLSを使用する場合、通常は`WOLFSSL_USER_IO`を定義し、送受信にコールバックを使用します。


#### WOLFSSL_NO_STRICT_CIPHER_SUITE

厳格な暗号スイート検証要件を緩和します。ネゴシエートされたプロトコルバージョンと完全には一致しない可能性のある暗号スイートを許可します。


#### WOLFSSL_NO_TICKET_EXPIRE

セッションチケットの有効期限チェックを無効化します。セッションチケットは経過時間に関わらず受け入れられるようになります。

#### WOLFSSL_NO_TLS12

TLS 1.2を除外するために定義します。

#### WOLFSSL_PEM_TO_DER

PEMからDERへの変換を無効化するための、鍵および証明書生成機能のサポートです。

#### WOLFSSL_NO_SIGALG

署名アルゴリズム拡張を無効化します

#### NO_RESUME_SUITE_CHECK

TLS接続の再開時における暗号スイートのチェックを無効化します

#### NO_ASN

ASN形式の証明書処理のサポートを削除します。

#### NO_OLD_TLS

SSLv3、TLSv1.0、およびTLSv1.1のサポートを削除します

#### WOLFSSL_AEAD_ONLY

非AEADアルゴリズムのサポートを削除します。AEADは「authenticated encryption with associated data（関連データ付き認証暗号）」の略で、これらのアルゴリズム（AES-GCMなど）はデータの暗号化と復号を行うだけでなく、そのデータの機密性と真正性も保証します。

#### WOLFSSL_SP_NO_2048

RSA/DH 2048ビットのSingle-Precision（SP）最適化を削除します。

#### WOLFSSL_SP_NO_3072

RSA/DH 3072ビットのSingle-Precision（SP）最適化を削除します。

#### WOLFSSL_SP_NO_256

SECP256R1のECC Single-Precision（SP）最適化を削除します。`WOLFSSL_SP_MATH`にのみ適用されます。


### 機能有効化マクロ（デフォルトで有効）

#### HAVE_TLS_EXTENSIONS

TLS拡張のサポートを有効化します。TLS拡張はほとんどのTLSビルドで必要です。`./configure`ではデフォルトで有効ですが、`WOLFSSL_USER_SETTINGS`でビルドする場合は手動で定義する必要があります。

#### HAVE_SUPPORTED_CURVES

TLSで使用されるTLS supported curves拡張およびkey share拡張を有効化します。ECC、Curve25519、およびCurve448を使用する場合に必要です。`./configure`ではデフォルトで有効ですが、`WOLFSSL_USER_SETTINGS`でビルドする場合は手動で定義する必要があります。

#### HAVE_EXTENDED_MASTER

TLS v1.2以前で使用されるセッション鍵の計算のための、拡張マスターシークレットPRFを有効化します。このPRF方式はデフォルトで有効であり、より安全であると考えられています。`./configure`を使用する場合はデフォルトで有効ですが、`WOLFSSL_USER_SETTINGS`でビルドする場合は手動で定義する必要があります。

#### HAVE_ENCRYPT_THEN_MAC

ブロック暗号において、暗号化後にMACを実行するencrypt-then-macサポートを有効化します。これはデフォルトであり、セキュリティを向上させます。`./configure`を使用する場合はデフォルトで有効ですが、`WOLFSSL_USER_SETTINGS`でビルドする場合は手動で定義する必要があります。

#### HAVE_ONE_TIME_AUTH

TLS v1.2でChacha20/Poly1305を使用する場合に、Poly認証をセットアップするために必要です。`./configure`を使用する場合、ChaCha20/Poly1305が有効であればデフォルトで有効ですが、`WOLFSSL_USER_SETTINGS`でビルドする場合は手動で定義する必要があります。

#### WOLFSSL_ASN_TEMPLATE
テンプレートベースのASN.1処理を使用する新しいバージョンのASN解析コードを有効化します。この解析は標準のASN.1規則に準拠し、テンプレート構造を用いてエンコードとデコードを規定することで、パーサーコードをテンプレート間で汎化できるようにしています。`./configure`を使用する場合はデフォルトで有効ですが、`WOLFSSL_USER_SETTINGS`でビルドする場合は手動で定義する必要があります。

#### WOLFSSL_DEBUG_ASN_TEMPLATE
ASN.1テンプレート使用時のデバッグ出力を有効化します。`WOLFSSL_ASN_TEMPLATE`と併用する場合にのみ意味を持ちます。

#### WOLFSSL_DEBUG_CERTS

解析、検証、チェーン構築を含む証明書処理操作のデバッグログを有効化します。

#### WOLFSSL_ASN_TEMPLATE_TYPE_CHECK
テストのために、コンパイラの型に関する問題をより適切に検査するASN関数を使用します。`WOLFSSL_ASN_TEMPLATE`と併用する場合にのみ意味を持ちます

### デフォルトで無効な機能の有効化

#### WOLFSSL_CERT_GEN

wolfSSLの証明書生成機能を有効にします。詳細は[鍵と証明書](chapter07.md#keys-and-certificates)を参照してください。

#### WOLFSSL_DER_LOAD

関数[`wolfSSL_CTX_der_load_verify_locations()`](group__CertsKeys.md#function-wolfssl_ctx_der_load_verify_locations)を使用して、DER形式のCA証明書をwolfSSLコンテキスト（`WOLFSSL_CTX`）に読み込めるようにします。

#### WOLFSSL_DTLS

DTLS（データグラムTLS）の使用を有効にします。DTLSは広くサポートされておらず、あまり使用されていません。

#### WOLFSSL_DTLS_CID

DTLS Connection IDサポート（RFC 9146）を有効化します。トランスポートアドレスではなくCIDで接続を識別することにより、DTLS接続がIPアドレスの変更後も存続できるようになります。


#### WOLFSSL_DTLS_DROP_STATS

DTLS接続品質の監視とデバッグのための、DTLSパケットドロップ統計の追跡を有効化します。


#### WOLFSSL_DTLS_DISALLOW_FUTURE

未来のエポック番号を持つDTLSレコードを拒否します。DTLS通信中のより厳格なエポック検証を提供します。

#### WOLFSSL_ALLOW_TLSV10

TLS 1.0接続を許可します。TLS 1.0はセキュリティ上の理由からデフォルトで無効化されています。レガシー互換性が必要な場合にのみ有効化してください。


#### WOLFSSL_ALLOW_TLS_SHA1

セキュリティポリシーによってSHA-1が制限されている場合でも、TLSにおけるSHA-1ベースの暗号スイートと署名を許可します。

#### WOLFSSL_EITHER_SIDE

同一の`WOLFSSL_CTX`をクライアント接続とサーバー接続の両方に使用できるようにします。デフォルトでは、コンテキストは作成時にクライアントまたはサーバーのいずれか一方用に構成されます。


#### WOLFSSL_EGD_NBLOCK

EGDを使用するシステムでの乱数生成のための、非ブロッキングEGD（Entropy Gathering Daemon）サポートを有効化します。

#### HAVE_SNI

Server Name Indication（SNI）TLS拡張サポート（RFC 6066）を有効化します。クライアントが接続先のホスト名を示せるようになり、TLS上での仮想ホスティングが可能になります。

#### WOLFSSL_ALWAYS_KEEP_SNI

ハンドシェイク完了後もSSLセッション内にSNI値を保持します。デフォルトでは、メモリ節約のためにハンドシェイク後にSNIデータは解放されます。


#### WOLFSSL_ALWAYS_VERIFY_CB

検証が成功した場合でも、証明書検証コールバックを常に呼び出します。デフォルトでは、コールバックは失敗時にのみ呼び出されます。

#### HAVE_TRUNCATED_HMAC

Truncated HMAC TLS拡張（RFC 6066）を有効化します。帯域幅削減のため、フルサイズの代わりに80ビットのHMACタグを使用できるようになります。


#### HAVE_TIME_T_TYPE

プラットフォームが`time_t`型定義を提供していることを示します。ほとんどのプラットフォームでは自動的に設定されます。

#### HAVE_SECURE_RENEGOTIATION

セキュアリネゴシエーションサポート（[RFC 5746](https://tex2e.github.io/rfc-translater/html/rfc5746)）を有効化します。アクティブなセッション中にTLS接続が暗号スイートと鍵を再ネゴシエートできるようになります。

#### HAVE_SERVER_RENEGOTIATION_INFO

サーバー側のリネゴシエーション情報拡張を有効化します。サーバーhelloメッセージでセキュアリネゴシエーションのサポートを示します。

#### HAVE_SESSION_TICKET

TLSセッションチケットサポート（RFC 5077）を有効化します。サーバーがセッションチケットを発行できるようになり、サーバー側のセッション状態なしに高速な再開が可能になります。TLS 1.3の再開に必要です。

#### HAVE_TRUSTED_CA

Trusted CA Indication TLS拡張（RFC 6066）を有効化します。クライアントが信頼するCA証明書を示せるようになり、サーバーが適切な証明書チェーンを選択するのに役立ちます。

#### HAVE_RPK

Raw Public Keyサポート（RFC 7250）を有効化します。TLSにおいてX.509証明書の代わりに生の公開鍵を使用できるようになり、制約の厳しい環境でのハンドシェイクのオーバーヘッドを削減します。

#### HAVE_ECH

Encrypted Client Hello（ECH）サポートを有効化します。ClientHelloを暗号化し、SNIなどの機微なフィールドを受動的な観測者から保護します。

#### WOLFSSL_NO_CA_NAMES

CertificateRequestメッセージでのCA名の送信を無効化します。サーバーが多数の信頼済みCAを持つ場合に、ハンドシェイクメッセージのサイズを削減します。

#### WOLFSSL_NO_SERVER_GROUPS_EXT

サーバーの最優先グループがクライアントのリストに含まれない場合に、サーバーがサポートするグループをTLS拡張で送信しないようにします。

#### HAVE_FFDHE

RFC 7919の標準化されたグループを使用した、Finite Field Diffie-Hellman Ephemeral（FFDHE）鍵交換を有効化します。

#### HAVE_SECRET_CALLBACK

TLSシークレットコールバックを有効化し、アプリケーションがハンドシェイク中にTLS鍵素材を受け取れるようにします。鍵のロギング、デバッグ、外部ツールとの統合に使用されます。

#### HAVE_PK_CALLBACKS

公開鍵操作コールバックを有効化し、アプリケーションがデフォルトのRSA、ECC、およびDH操作をカスタム実装（HSMやセキュアエレメントとの統合など）で上書きできるようにします。

#### WOLFSSL_SNIFFER

TLSパケットスニッフィングサポートを有効化します。秘密鍵を用いて、wolfSSLスニッファーライブラリによるTLSトラフィックの復号と検査が可能になります。

#### HAVE_WEBSERVER

追加のHTTPヘルパー関数など、wolfSSLにおけるWebサーバー向け機能を有効化します。

#### HAVE_WOLF_EVENT

非同期操作のためのwolfイベント駆動処理サポートを有効化します。保留中の非同期暗号操作を管理するためのイベントキューを提供します。

#### HAVE_WRITE_DUP

書き込み複製サポートを有効化し、別々のスレッドが同一のSSLオブジェクトに対してSSLの読み取りと書き込み操作を同時に実行できるようにします。

#### NO_CERTS

wolfSSLにおけるすべての証明書処理を無効化します。証明書処理が不要なPSK専用構成で使用することで、コードサイズを大幅に削減します。


#### NO_CLIENT_CACHE

クライアント側のセッションキャッシュを無効化します。セッション再開にはサーバーのセッションキャッシュのみが使用されます。

#### WOLFSSL_HAVE_PRF

TLS疑似乱数関数（PRF）へのアクセスを有効化します。アプリケーションがTLS PRFを使用して追加の鍵素材を導出できるようになります。

#### WOLFSSL_REQUIRE_TCA

クライアントがTrusted CA拡張を送信することを必須とします。この拡張がない場合、ハンドシェイクは失敗します。

#### WOLFSSL_DH_EXTRA

追加のDH鍵情報をSSLオブジェクトに格納します。ハンドシェイク後にDHパラメータおよび鍵へのアクセスを提供します。

#### WOLFSSL_CURVE25519_BLINDING

TLS鍵交換中のCurve25519操作に対するブラインディングを有効化します。タイミングサイドチャネル攻撃から保護します。

#### WOLFSSL_KEY_GEN

wolfSSLのRSA鍵生成機能を有効にします。詳細は[鍵と証明書](chapter07.md#keys-and-certificates)を参照してください。

#### WOLF_PRIVATE_KEY_ID

PKCS11とともに使用し、鍵IDおよびラベルAPIのサポートを有効化します。FIPS v5以前は、Crypto Callbacksとの併用での`WOLF_PRIVATE_KEY_ID`をサポートしていません。

#### WOLFSSL_WOLFSENTRY_HOOKS

このスイッチは、`wolfSSL_CTX_set_AcceptFilter()`および`wolfSSL_CTX_set_ConnectFilter()`を使用した、汎用的なネットワーク`accept`および`connect`フィルターフックのサポートをTLS層に追加します。また、サンプルのクライアントおよびサーバーアプリケーションにおけるwolfSentry統合も有効化します。

#### WOLFSSL_CERT_EXT

証明書拡張、鍵および証明書生成機能です。

#### WOLFSSL_CERT_REQ

証明書リクエスト、鍵、および証明書生成機能です。

#### WOLFSSL_SSLKEYLOGFILE

Wiresharkで使用される鍵ロギングを有効化します。マスターシークレットとクライアント乱数がファイルに書き込まれるため、コンパイラ警告が発生します。テストには有用ですが、本番環境では推奨されません。

#### WOLFSSL_SSLKEYLOGFILE_OUTPUT

このマクロは鍵ロギング用のファイル名を定義します。`WOLFSSL_SSLKEYLOGFILE`とともに使用されます。

#### WOLFSSL_HAVE_WOLFSCEP

wolfSCEPが利用可能かどうかを確認するためにautoconfが使用する機能を有効化します。

#### WOLFSSL_HAVE_MIN

このマクロは、MIN/MAXがプラットフォームによってすでに定義されているかどうかを示すための、ライブラリの移植性のためのものです。重複定義を防ぎます。

#### WOLFSSL_HAVE_TLS_UNIQUE

TLSハンドシェイク後の「Finished」メッセージを、「tls-unique」チャネルバインディングとして使用するために保持します。libestポートで追加されたもので、アプリケーションが'tls-unique'チャネルバインディングタイプ（https://tex2e.github.io/rfc-translater/html/rfc5929.html#3--The-tls-unique-Channel-Binding-Type）を取得できるようにします。これはESTプロトコルにおいて、'proof-of-possession'（https://tex2e.github.io/rfc-translater/html/rfc7030.html#3-4--Proof-of-Possession および https://tex2e.github.io/rfc-translater/html/rfc7030.html#3-5--Linking-Identity-and-POP-Information）を通じてエンロールメントをTLSセッションにバインドするために使用されます。

#### WOLFSSL_ENCRYPTED_KEYS

暗号化鍵のPKCS8サポートのために有効化します。このマクロはPKCS8のパスワードベース鍵暗号化を有効化します。PKCS8のRFCドキュメントへのリンクはこちらです（https://tex2e.github.io/rfc-translater/html/rfc5208）。

#### WOLFSSL_CUSTOM_OID

サブジェクトおよびリクエスト拡張のカスタムOIDサポートを有効化する証明書機能です。カスタムOIDを持つ証明書の解析にも適用されます。

#### WOLFSSL_RIPEMD

RIPEMD-160サポートを有効化します。

#### WOLFSSL_SHA384

SHA-384サポートを有効化します。

#### WOLFSSL_SHA512

SHA-512サポートを有効化します。

#### WOLFSSL_AES_DIRECT

AES ECBモードの直接サポートを有効化します。ECBモード単体では安全とは見なされません。この機能はPKCS7に必要です。警告: ほぼすべてのユースケースにおいて、ECBモードは安全性が低いと見なされます。可能な限りECB APIを直接使用することは避けてください。

#### DEBUG_WOLFSSL

デバッグ機能をビルドに組み込みます。wolfSSLのデバッグに関する詳細は、[デバッグ](chapter08.md#debugging)を参照してください。

#### HAVE_AESCCM

AES-CCMサポートを有効化します。

#### HAVE_AESGCM

AES-GCMサポートを有効化します。

#### WOLFSSL_AES_XTS

AES-XTSサポートを有効化します。

#### WOLFSSL_AES_128

AES-128鍵サイズのサポートを有効化します。デフォルトで有効です。より大きな鍵サイズのみが必要な場合、無効化することでAES-128を削除しコードサイズを削減できます。

#### WOLFSSL_AES_192

AES-192鍵サイズのサポートを有効化します。デフォルトで有効です。無効化することでAES-192を削除しコードサイズを削減できます。

#### WOLFSSL_AES_256

AES-256鍵サイズのサポートを有効化します。デフォルトで有効です。不要な場合は無効化することでAES-256を削除できます。

#### AES_MAX_KEY_SIZE

AESの最大鍵サイズをビット単位で設定します。デフォルトは256です。128または192に設定することで、コードとメモリの使用量を削減できます。

#### HAVE_AES_ECB

AES-ECB（Electronic Codebook）モードを有効化します。デフォルトでは無効です。ECBモードは各ブロックを独立して暗号化するため、ほとんどのアプリケーションでは一般に推奨されませんが、一部のプロトコルで必要とされます。

#### HAVE_AES_DECRYPT

AES復号サポートを有効化します。デフォルトで有効です。AES暗号化のみが必要なプラットフォーム（GCM暗号化のみなど）では、無効化することでコードサイズを節約できます。

#### WOLFSSL_AES_COUNTER

AES-CTR（Counter）モードを有効化します。インクリメントするカウンターを使用して、ブロック暗号をストリーム暗号に変換します。

#### WOLFSSL_AES_CFB

AES-CFB（Cipher Feedback）モードを有効化します。[`WOLFSSL_NO_AES_CFB_1_8`](#wolfssl_no_aes_cfb_1_8)で制限されない限り、CFB-1、CFB-8、CFB-64、およびCFB-128のサブモードが含まれます。

#### WOLFSSL_NO_AES_CFB_1_8

[`WOLFSSL_AES_CFB`](#wolfssl_aes_cfb)が有効な場合に、AES-CFB-1およびAES-CFB-8サブモードを無効化します。CFB-64/128のみが必要な場合にコードサイズを削減します。

#### WOLFSSL_AES_OFB

AES-OFB（Output Feedback）モードを有効化します。OFBモードはAESをストリーム暗号に変換し、パディングを必要としません。

#### WOLFSSL_AES_CTS

AES-CTS（Ciphertext Stealing）モードを有効化します。CTSにより、ブロックサイズの倍数でないデータをパディングなしで暗号化できます。

#### WOLFSSL_AES_SIV

AES-SIV（Synthetic Initialization Vector）モード（RFC 5297）を有効化します。ノンスの誤用に耐性のあるAEADモードであり、決定論的な認証付き暗号化を提供します。

#### WOLFSSL_AES_EAX

AES-EAX AEADモードを有効化します。EAXはCTRモードとOMAC（CMAC）から構築される2パスのAEADスキームであり、関連データ付き認証暗号を提供します。

#### HAVE_AES_KEYWRAP

AES Key Wrapサポート（RFC 3394）を有効化します。安全な転送のために暗号鍵をラップ（暗号化）するのに使用されます。

#### WOLFSSL_AES_CBC_LENGTH_CHECKS

AES-CBC操作における入力データ長の厳格な検証を有効化します。有効化すると、入力長がAESブロックサイズの倍数でない場合、CBC暗号化/復号はエラーを返します。

#### HAVE_AESGCM_DECRYPT

AES-GCM復号サポートを有効化します。[`HAVE_AESGCM`](#have_aesgcm)が有効な場合、デフォルトで有効です。GCM暗号化のみが必要な制約の厳しいデバイスでは無効化できます。

#### WOLFSSL_AESGCM_STREAM

ストリーミングAES-GCM APIを有効化します。AES-GCMデータを一括ではなく逐次的に処理できるようになり、大きなデータやメモリに制約のある環境で有用です。


#### WC_AES_GCM_DEC_AUTH_EARLY

復号を実行する前にGCMタグを認証します。認証タグが一致しない場合にフェイルファスト動作を提供し、無効な暗号文の不要な復号を回避します。

#### GCM_TABLE

AES-GCMのガロア体乗算に事前計算済みの4ビットルックアップテーブルを使用します。[`GCM_SMALL`](#gcm_small)より高速ですが、より多くのメモリを使用します。[`GCM_TABLE_4BIT`](#gcm_table_4bit)および[`GCM_WORD32`](#gcm_word32)も参照してください。

#### GCM_TABLE_4BIT

4ビットGCMルックアップテーブルモードの明示的なオプションです。[`GCM_TABLE`](#gcm_table)と同様に機能します。

#### GCM_WORD32

AES-GCMのガロア体乗算に32ビットワード実装を使用します。[`GCM_SMALL`](#gcm_small)および[`GCM_TABLE`](#gcm_table)の代替であり、64ビットサポートのないプラットフォームで有効に機能します。

#### GCM_GMULT_LEN

1回のGMULT呼び出しでAADまたは暗号文の複数ブロックを処理するためのGCM GMULT長最適化を有効化します。

#### WOLFSSL_AESXTS_STREAM

ストリーミングAES-XTS APIを有効化します。AES-XTSデータを単一の操作ではなく、複数の更新呼び出しにわたってインクリメンタルに処理できるようになります。

#### WC_AESXTS_STREAM_NO_REQUEST_ACCOUNTING

ストリーミングAES-XTS APIにおけるリクエストアカウンティングを無効化します。不要な場合にデータユニット境界を追跡するオーバーヘッドを削除します。

#### WC_AES_XTS_SUPPORT_SIMULTANEOUS_ENC_AND_DEC_KEYS

AES-XTSコンテキストが暗号化鍵と復号鍵を同時に保持できるようにします。デフォルトでは、XTSコンテキストはメモリ節約のため一度に一方向のみをサポートします。

#### HAVE_CAMELLIA

Camelliaサポートを有効化します。

#### HAVE_CHACHA

ChaCha20サポートを有効化します。

#### HAVE_POLY1305

Poly1305サポートを有効化します。

#### POLY130564

64ビットプラットフォームでのパフォーマンス向上のため、64ビットPoly1305実装を使用します。64ビットサポートのあるプラットフォームでは自動的に選択されます。

#### USE_INTEL_POLY1305_SPEEDUP

Intel/AMDプロセッサで最大のスループットを得るため、Intel AVX/AVX2最適化Poly1305実装を有効化します。

#### HAVE_CRL

証明書失効リスト（CRL）サポートを有効化します。

#### HAVE_CRL_IO

CRL URLに対するブロッキングのインラインHTTPリクエストを有効化します。CRLを`WOLFSSL_CTX`にロードし、そこから作成されるすべてのWOLFSSLオブジェクトに適用します。

#### HAVE_ECC

楕円曲線暗号（ECC）サポートを有効化します。

#### HAVE_LIBZ

接続上でのデータ圧縮を可能にする拡張機能です。通常は使用すべきではありません。後述のconfigureに関する注記（libz）を参照してください。

#### OPENSSL_EXTRA

さらに多くのOpenSSL互換性をライブラリに組み込み、OpenSSLで動作するように設計された既存アプリケーションへのwolfSSLの移植を容易にするwolfSSL OpenSSL互換性レイヤーを有効化します。デフォルトでは無効です。

#### HAVE_EXT_CACHE

（内部のものではなく）外部セッションキャッシュの使用をサポートする機能を有効化します。

#### WOLFSSL_WPAS_SMALL

WPAサプリカントサポート用の、互換性レイヤーのより小さなサブセットを有効化します。

#### OPENSSL_ALL

統合テスト用に、すべての互換性関数のサポートを有効化します。

#### OPENSSL_COEXIST

OpenSSL互換レイヤー。旧名称の無効化が必要です。wolfSSLとOpenSSLの共存を可能にするモードです。

#### OPENSSL_VERSION_NUMBER

OpenSSL互換性を実装するバージョン番号を指定します。


#### OLD_HELLO_ALLOWED

SSLv2レコード形式を使用してより高いプロトコルバージョンをネゴシエートするレガシークライアントとの後方互換性のため、SSLv2形式のClientHelloメッセージを許可します。

#### WOLFSSL_NGINX

OpenSSL互換性のアプリケーション固有マクロ。nginxでは`(--enable-nginx) WOLFSSL_NGINX`を使用します。

#### WOLFSSL_ERROR_CODE_OPENSSL

OpenSSL互換API wolfSSL_EVP_PKEY_cmpは成功時に0、失敗時に-1を返します。この動作はOpenSSLとは異なります。EVP_PKEY_cmpは以下を返します:
1: 2つの鍵が一致
0: 一致しない
-1: 鍵の種別が異なる
-2: 操作がサポートされていない
この関数をopenSSLと同じ動作にしたい場合は、WOLFSSL_ERROR_CODE_OPENSSLを定義することで、WS_RETURN_CODEがリターンコードをOpenSSLの同等の動作に一致するように変換します。

#### WOLFSSL_HARDEN_TLS

[RFC9325](https://tex2e.github.io/rfc-translater/html/rfc9325)で規定された推奨事項を実装します。このマクロは、望むセキュリティビット数の値で定義する必要があります。現在実装されている値は112ビットおよび128ビットです。以下のマクロは特定のチェックを無効化します。
- WOLFSSL_HARDEN_TLS_ALLOW_TRUNCATED_HMAC
- WOLFSSL_HARDEN_TLS_ALLOW_OLD_TLS
- WOLFSSL_HARDEN_TLS_NO_SCR_CHECK
- WOLFSSL_HARDEN_TLS_NO_PKEY_CHECK
- WOLFSSL_HARDEN_TLS_ALLOW_ALL_CIPHERSUITES

#### WOLFSSL_ASIO

OpenSSL互換性固有のマクロです。

#### WOLFSSL_QT

OpenSSL互換性固有のマクロです。QT向けにDH Extra、OpenSSL all、OpenSSH、および静的エフェメラルを有効化します。


#### WOLFSSL_QNX_CAAM

QNXベースのシステムでハードウェアアクセラレーションによる暗号処理を行うためのQNX CAAM（Cryptographic Acceleration and Assurance Module）サポートを有効化します。

#### WOLFSSL_HAPROXY

OpenSSL互換性固有のマクロです。

#### WOLFSSL_ASYNC_IO

非同期クリーンアップで使用されます。


#### WOLFSSL_ASYNC_CRYPT_SW

テスト用のソフトウェアベース非同期暗号シミュレーションを有効化します。実際の非同期ハードウェアを必要とせずに非同期動作をシミュレートします。

#### WOLFSSL_ATMEL

`atmel_get_random_number`関数を使用してランダムデータをシードするASFフックを有効化します。

#### WOLFSSL_CMAC

追加のCMACアルゴリズムを有効化します。注記: `WOLFSSL_AES_DIRECT`が必要です。

#### WOLFSSL_ESPIDF_ERROR_PAUSE

test.cでのみ使用され、テストエラー時にデバッグ目的で遅延を追加します。

#### TEST_IPV6

テストアプリケーションでIPv6のテストを有効にします。wolfSSL本体はIPに対して中立ですが、テストアプリケーションはデフォルトでIPv4を使用します。

#### TEST_NONBLOCK_CERTS

ノンブロッキングOCSP応答のテスト専用に使用されます。`WOLFSSL_NONBLOCK_OCSP`および`OCSP_WANT_READ`と共に有効化します。

#### TEST_OPENSSL_COEXIST

ビルドオプション`./configure --enable-opensslcoexist`を有効化する際に使用します。

#### TEST_PK_PRIVKEY

PKコールバックのテスト専用に使用されます。wolfssl/test.hでは、コンテキストを使用して、ロードされPKコールバックで使用される実際の秘密鍵を渡します。

#### TEST_BUFFER_SIZE

サンプルのクライアント/サーバーの`-B`オプションで使用されるTLSベンチマークテストのバッファサイズを上書きできます。

#### FORCE_BUFFER_TEST

ファイルシステムの代わりにtest_certs.hのバッファの使用を強制します。wolfssl/test.hでの内部テスト専用に使用されます。

#### WOLFSSL_FORCE_MALLOC_FAIL_TEST

ランダムなmalloc失敗を誘発する内部テスト用に定義します。

#### WOLFSSL_POST_HANDSHAKE_AUTH

TLS拡張。ハンドシェイク後認証に使用されます。

#### WOLFSSL_PSK_MULTI_ID_PER_CS

TLS 1.3のPSKにおいて、`WOLFSSL_PSK_MULTI_ID_PER_CS`が定義されている場合、1つの暗号スイートに対して複数のIDを扱えます。

#### WOLFSSL_PUBLIC_ASN

内部で使用されているASN.1 APIを公開します。内部のasn.h APIを使用して解析を行いたい利用者にとって有用です。

#### WOLFSSL_QUIC

QUICプロトコルのサポートを有効化します。詳細は(https://github.com/wolfSSL/wolfssl/blob/master/doc/QUIC.md)を参照してください。

#### WOLFSSL_QUIC_MAX_RECORD_CAPACITY

QUICの最大容量を1024*1024（1 MB）として定義します。

#### WOLFSSL_RENESAS_FSPSM_TLS

まだサポートされていないTLS関連機能です。


#### WOLFSSL_REFCNT_ERROR_RETURN

SSLオブジェクトの参照カウント失敗時に、黙って継続するのではなくエラーを返します。リソース管理の問題の検出に役立ちます。

#### WOLFSSL_RENESAS_TSIP_TLS

TSIPのTLS連携共通鍵暗号化方式のみを無効化するためのものです。

#### WOLFSSL_SM2

SM暗号を使用するために定義します。

#### WOLFSSL_SM3

SM暗号を使用するために定義します。

#### WOLFSSL_SM4

SM暗号を使用するために定義します。

#### WOLFSSL_SM4_CBC

SM4 CBC用のSM設定です。

#### WOLFSSL_SM4_CCM

SM4 CCM用のSM設定です。

#### WOLFSSL_SM4_GCM

SM4 GCM用のSM設定です。

#### WOLFSSL_SNIFFER_CHAIN_INPUT

Chain Inputオプションを使うと、スニファは生パケットへのポインタではなく、struct iovecのリストとして入力を受け取れるようになります。

#### XSLEEP_MS

テスト専用に使用されます。カスタム遅延を定義できます。

#### XSNPRINTF

snprintf関数を上書きできます。

#### DEFAULT_TIMEOUT_SEC

`HAVE_IO_TIMEOUT`と共に使用し、wolfio.cのソケットタイムアウトを秒単位で指定します。OCSPおよびCRLのHTTP用の内部ソケットコードで使用されます。

#### HAVE_IO_TIMEOUT

証明書失効。IOオプションで接続タイムアウトのサポートを有効化しますが、デフォルトでは無効です。

#### HAVE_OCSP

オンライン証明書ステータスプロトコル（OCSP）サポートを有効化します。

#### HAVE_CSHARP

C#ラッパーに必要な設定オプションを有効にします。

#### HAVE_CURVE25519

curve25519アルゴリズムの使用を有効にします。

#### HAVE_ED25519

ed25519アルゴリズムの使用を有効にします。

#### WOLFSSL_DH_CONST

Diffie Hellman演算実行時の浮動小数点値の使用を無効にし、`XPOW()`および`XLOG()`にテーブルを使用します。外部数学ライブラリへの依存を削除します。

#### WOLFSSL_TRUST_PEER_CERT

信頼済みピア証明書の使用を有効にします。これにより、CAを使用するのではなく、接続と照合するためのピア証明書をロードできます。有効時に信頼済みピア証明書が一致した場合、ピア証明書チェーンはロードされず、ピアは検証済みとみなされます。CAの使用が推奨されます。

#### WOLFSSL_STATIC_MEMORY

静的メモリバッファおよび関数の使用を有効にします。これにより、動的メモリの代わりに静的メモリを使用できます。

#### WOLFSSL_STATIC_MEMORY_LEAN

`WOLFSSL_STATIC_MEMORY`の定義が必要です。65k未満のメモリプールサイズを必要とする構造体に対してより小さな型サイズを使用し、フットプリントサイズを削減するためにIOバッファなどの利用可能な機能を制限します。

#### WOLFSSL_SESSION_EXPORT

DTLSセッションのエクスポートとインポートの使用を有効にします。これにより、DTLSセッションの現在の状態をシリアライズして送受信できます。


#### WOLFSSL_SESSION_EXPORT_DEBUG

セッションのエクスポートおよびインポート操作のデバッグログを有効化します。セッションシリアライズに関する問題の診断に役立ちます。


#### WOLFSSL_SESSION_EXPORT_NOPEER

ピア証明書情報を含めずにセッションをエクスポートします。ピア証明書が不要な場合に、エクスポートされるセッションのサイズを削減します。

#### WOLFSSL_ARMASM

ARMv8ハードウェアアクセラレーションの使用を有効にします。

#### WC_RSA_NONBLOCK

RSA演算をより小さな作業単位に分割するための、fast math RSAノンブロッキングサポートを有効にします。この機能は[`wc_RsaSetNonBlock()`](group__RSA.md#function-wc_rsasetnonblock)を呼び出し、`FP_WOULDBLOCK`リターンコードをチェックすることで有効化されます。

#### WC_RSA_BLINDING

タイミング耐性を有効化するために使用されます。

#### WC_RSA_PSS

RSA PSSパディングを有効化します。TLS 1.3でサポートされる唯一のRSAパディング方式はPSSです（仕様による）。PSSパディングはランダムなパディングを使用します。

#### WOLFSSL_RSA_VERIFY_ONLY

RSA検証専用の小さなビルドを有効にします。マクロ[`WOLFSSL_RSA_PUBLIC_ONLY`](#wolfssl_rsa_public_only)、[`WOLFSSL_RSA_VERIFY_INLINE`](#wolfssl_rsa_verify_inline)、[`NO_SIG_WRAPPER`](#no_sig_wrapper)、および[`WOLFCRYPT_ONLY`](#wolfcrypt_only)と共に使用すべきです。

#### WOLFSSL_RSA_PUBLIC_ONLY

RSA公開鍵専用の小さなビルドを有効にします。マクロ[`WOLFCRYPT_ONLY`](#wolfcrypt_only)と共に使用すべきです。

#### WOLFSSL_SHA3

SHA3を使用するビルドを有効にします。これはSHA3-224、SHA3-256、SHA3-384、SHA3-512のサイズに対応するSHA3 Keccakのサポートです。加えて、`WOLFSSL_SHA3_SMALL`を使用すると、パフォーマンスとリソース使用量をトレードオフできます。

#### USE_ECDSA_KEYSZ_HASH_ALGO

エフェメラルECDHE鍵サイズに一致する、または次に大きい利用可能なハッシュアルゴリズムを選択します。この回避策は、P-256鍵をSHA512でハッシュするといったシナリオを適切にサポートしない一部のピアとの問題を解決します。

#### WOLFSSL_ALT_CERT_CHAINS

有効なチェーンの一部ではないCAをピアが提示することを許可します。wolfSSLのデフォルトの動作は、提示されたすべてのピア証明書の検証を要求します。これにより、中間CAを信頼済みとしてロードし、ルートまでのチェーン上のCAに対する署名者不在の失敗を無視することもできます。代替証明書チェーンモードでは、ピア証明書が信頼済みCAに対して検証できることのみを要求します。

#### WOLFSSL_SYS_CA_CERTS

[`wolfSSL_CTX_load_system_CA_certs()`](group__CertsKeys.html#function-wolfssl_ctx_load_system_ca_certs)が呼び出された際に、wolfSSLの証明書マネージャーにロードするか、システムの認証APIを呼び出すことで、wolfSSLが検証に信頼済みシステムCA証明書を使用できるようにします。詳細は[`wolfSSL_CTX_load_system_CA_certs()`](group__CertsKeys.html#function-wolfssl_ctx_load_system_ca_certs)を参照してください。このプリプロセッサマクロは、configureオプション`--enable-sys-ca-certs`によって自動的に設定されます。


#### WOLFSSL_SYS_CRYPTO_POLICY

利用可能なアルゴリズムと鍵サイズを制限するために、システムレベルの暗号ポリシー設定（例: `/etc/crypto-policies`）を尊重します。

#### WOLFSSL_APPLE_NATIVE_CERT_VERIFICATION

TLSピア証明書の認証時にAppleのネイティブトラストAPIの使用を有効化します。[WOLFSSL_SYS_CA_CERTS](#WOLFSSL_SYS_CA_CERTS)の定義が必要です。iOSやその他のAppleデバイス上で`configure`または`CMake`でビルドする場合、このマクロをユーザーが設定する必要はありませんが、MacOSでネイティブ検証メソッドを使用したい場合は明示的に設定する必要があります。


#### WOLFSSL_TEST_APPLE_NATIVE_CERT_VALIDATION

Appleネイティブ証明書検証のテストモードを有効化します。Apple証明書検証統合のユニットテストに使用されます。

#### WOLFSSL_CUSTOM_CURVES

非標準の曲線を許可します。計算に曲線の変数「a」を含めます。[`HAVE_ECC_SECPR2`](#have_ecc_secpr2)、[`HAVE_ECC_SECPR3`](#have_ecc_secpr3)、[`HAVE_ECC_BRAINPOOL`](#have_ecc_brainpool)、[`HAVE_ECC_KOBLITZ`](#have_ecc_koblitz)を使用して追加の曲線タイプを有効化できます。

#### HAVE_COMP_KEY

ECC圧縮鍵サポートを有効化します。

#### WOLFSSL_EXTRA_ALERTS

TLS接続中に追加のアラートを送信できるようにします。この機能は[`--enable-opensslextra`](#--enable-opensslextra)の使用時にも自動的に有効化されます。


#### WOLFSSL_EXTRA

標準セットを超える追加のSSLセッション情報の追跡とAPIを有効化します。デバッグおよび監視のための追加のセッション詳細を提供します。

#### WOLFSSL_DEBUG_TLS

TLS接続中の追加のデバッグ出力を有効化します

#### WOLFSSL_DEBUG_TRACE_ERROR_CODES

デバッグ用にエラーコードの発生元のトレースを有効化します。wolfSSLソースコード内でエラーコードが生成された場所をログに記録します。

#### WOLFSSL_DEBUG_MEMORY

メモリ割り当てのデバッグを有効化します。`malloc()`および`free()`の呼び出しをファイル名と行番号情報と共にログに記録します。

#### WOLFSSL_DEBUG_OPENSSL

OpenSSL互換性レイヤー関数のデバッグログを有効化します。どのOpenSSL互換APIが呼び出されているかのトレースに役立ちます。

#### HAVE_BLAKE2

Blake2bアルゴリズムのサポートを有効化します

#### HAVE_FALLBACK_SCSV

サーバー側でSignaling Cipher Suite Value(SCSV)サポートを有効化します。これは、TLSバージョンのダウングレードを許可すべきでないことを通知するためにクライアントから送信される暗号スイート0x56 0x00を処理します。

#### HAVE_AEAD

AEADの使用を実装します。TLS 1.3に必須です。

#### HAVE_AES_CBC

AES CBCのオプションを有効にします。

#### HAVE_ALPN

アプリケーション層プロトコルネゴシエーションのオプションを有効にします。

#### HAVE_CAVIUM_OCTEON_SYNC

Marvell Cavium/Octeonハードウェアのブロッキング（同期）バージョンを有効化します。

#### HAVE_CERTIFICATE_STATUS_REQUEST

証明書失効のための証明書ステータスリクエスト機能として使用されます。

#### HAVE_CERTIFICATE_STATUS_REQUEST_V2

証明書失効のための証明書ステータスリクエスト機能として使用されます。

#### HAVE_CURL

cURLとリンクする際に、wolfSSLライブラリのサブセットをビルドするために使用されます。

#### HAVE_CURVE448

Curve448サポートのために定義します。追加のマクロ設定を変更できます。デフォルトでは、共有秘密、鍵エクスポート、およびインポートが有効化されます。

#### HAVE_CURVE448_SHARED_SECRET

Curve448共有秘密の計算を有効化します。[`HAVE_CURVE448`](#have_curve448)が有効な場合、デフォルトで有効です。

#### HAVE_CURVE448_KEY_EXPORT

Curve448公開鍵エクスポートを有効化します。[`HAVE_CURVE448`](#have_curve448)が有効な場合、デフォルトで有効です。

#### HAVE_CURVE448_KEY_IMPORT

Curve448公開鍵インポートを有効化します。[`HAVE_CURVE448`](#have_curve448)が有効な場合、デフォルトで有効です。

#### WOLFSSL_ECDHX_SHARED_NOT_ZERO

ECDH共有秘密がすべてゼロでないことを検証します。不正曲線攻撃に対する保護を提供します。

#### HAVE_DANE

このオプションは`HAVE_RPK`（Raw Public Keys）でのみサポートされ、将来追加される可能性に備えたプレースホルダーです。

#### HAVE_DILITHIUM

ポスト量子暗号/署名アルゴリズムのDILITHIUMを含めるために有効化します。


#### HAVE_DH_DEFAULT_PARAMS

アプリケーションが明示的に独自のDHパラメータをロードしない場合に、鍵交換用のデフォルトDHパラメータを含めます。

#### HAVE_ED25519_KEY_IMPORT

ED25519の設定。署名、検証、共有秘密、インポート、エクスポートをきめ細かく制御するためのEd255519およびCurve25519オプションを有効化します。

#### HAVE_EX_DATA

CTX/WOLFSSLでユーザー情報のための「extra」EXデータAPIを有効化します。

#### HAVE_EX_DATA_CLEANUP_HOOKS

指定のインデックスでRSA鍵に対して追加データとクリーンアップコールバックを設定します。


#### HAVE_EX_DATA_CRYPTO

SSL/CTX/X509オブジェクトに加えて、RSA鍵やECC鍵などのwolfCryptオブジェクトに対する追加データ（ex_data）サポートを有効化します。

#### HAVE_FALCON

OpenQuantumSafeのポスト量子暗号FALCONを有効化します。

#### HAVE_FIPS

異なるFIPSバージョンを実装する際に使用されます。


#### HAVE_FUZZER

セキュリティテスト用のファジングコールバックサポートを有効化します。処理中にTLSレコードを変更または検査できるコールバックを設定できるようにします。

#### HAVE_KEYING_MATERIAL

[RFC 8446](https://tex2e.github.io/rfc-translater/html/rfc8446#7-5--Exporters)のセクション7.5に基づく鍵素材（keying material）のエクスポートを有効化します。

#### HAVE_OID_DECODING

ASNテンプレートコードに含まれます。一部のケースでのデコードに使用されます。

#### HAVE_MAX_FRAGMENT

最大フラグメントサイズを設定します。TLS拡張です。


#### HAVE_MEMCACHED

wolfSSLとmemcachedの互換性に必要なAPIおよび動作を有効化します。

#### WOLFSSL_PSK_ONE_ID

TLS 1.3で1つのPSK IDのみのサポートを有効化します。


#### WOLFSSL_PSK_IDENTITY_ALERT

ハンドシェイク中にPSK IDの検索が失敗した場合、汎用的なハンドシェイク失敗ではなく、特定のTLSアラートを送信します。

#### SHA256_MANY_REGISTERS

すべてのデータをレジスタに保持し、ループを部分的に展開するSHA256バージョンです。

#### WOLFCRYPT_HAVE_SRP

wolfCryptのセキュアリモートパスワードサポートを有効化します

#### WOLFSSL_MAX_STRENGTH

最も強力なセキュリティ機能のみを有効化し、弱いまたは非推奨の機能を無効化します。タイミングベースのサイドチャネル攻撃から保護するためのほぼ一定時間の実行により、パフォーマンスは低下します。

#### MAX_RECORD_SIZE

最大レコードサイズを決定します。標準による最大サイズは2^14です。

#### MAX_CERTIFICATE_SZ

証明書メッセージペイロードの最大サイズを定義します。証明書1つあたり2kbでMAX_CHAIN_DEPTH個の証明書を想定しています。

#### MAX_CHAIN_DEPTH

最大チェーン深度を定義します。

#### MAX_CIPHER_NAME

最大暗号名を定義します。

#### MAX_DATE_SIZE

byte lastdateまたはbyte nextdateとして使用される日付の最大サイズを定義します。

#### MAX_EARLY_DATA_SZ

早期データ（early data）の最大サイズを定義するために使用されます。


#### MAX_EX_DATA

SSL/CTX/X509オブジェクトごとに格納できる追加データ（ex_data）エントリの最大数を設定します。未定義の場合、デフォルトは5です。

#### WOLFSSL_MAX_SEND_SZ

最大送信サイズを指定するために定義します。

#### WOLFSSL_MAX_SUITE_SZ

最大スイートサイズを指定するために定義します。小さすぎる場合はエラーになります。

#### MAX_WOLFSSL_FILE_SIZE

4 MBの割り当てサイズ制限です。

#### WOLFSSL_MAXQ10XX_TLS

使用しているTLSバージョンをmaxq10xxに知らせます。

#### WOLFSSL_MAX_SIGALGO

最大署名アルゴリズム数を上書きする機能を有効化します。

#### WOLFSSL_MEM_GUARD

指定されたメモリガードを割り当てられます。

#### WOLFSSL_STATIC_EPHEMERAL

TLSスニファサポート。

#### SSL_SNIFFER_EXPORTS

WIN32スニファエクスポート。

#### WOLFSSL_SNIFFER_KEYLOGFILE

SSL Keylogファイルオプションを使うと、スニファは[NSS keylogファイル](https://web.archive.org/web/20220531072242/https://firefox-source-docs.mozilla.org/security/nss/legacy/key_log_format/index.html)から取得したマスターシークレットを使用してTLSトラフィックを復号できるようになります。これにより、エフェメラル暗号スイートを使用するTLS接続であっても、スニファはすべてのTLSトラフィックを復号できます。Keylogファイルスニッフィングは、TLSバージョン1.2および1.3でサポートされています。wolfSSLは、スニファ機能とは独立して、configureオプション`--enable-keylog-export`を使用してkeylogファイルをエクスポートするように設定できます（注記: 本質的に安全でないため、本番環境では決して行わないでください）。keylogファイルのスニファサポートを有効化するには、次のconfigureコマンドラインを使用し、通常どおりビルドします: ./configure --enable-sniffer CPPFLAGS=-DWOLFSSL_SNIFFER_KEYLOGFILE`。

#### WOLFSSL_SNIFFER_STORE_DATA_CB

Store Data Callbackオプションを使うと、スニファは、再割り当てされたデータポインタではなく、カスタムバッファにアプリケーションデータを格納する際に呼び出されるコールバックを受け取れるようになります。コールバックは、すべてのデータが消費されるまでループ内で呼び出されます。このオプションを有効化するには、次のconfigureコマンドラインを使用し、通常どおりビルドします: `./configure --enable-sniffer CPPFLAGS=-DWOLFSSL_SNIFFER_STORE_DATA_CB`。

#### WOLFSSL_SNIFFER_WATCH

Session Watchingオプションを使うと、スニファは初期セットアップなしに提供された任意のパケットを監視できるようになります。すべてのTLSセッションのデコードを開始し、サーバーの証明書が検出されると、その証明書はユーザーが提供するコールバック関数に渡されます。この関数は適切な秘密鍵を提供する必要があります。このオプションを有効化するには、次のconfigureコマンドラインを使用し、通常どおりビルドします: `./configure --enable-sniffer CPPFLAGS=-DWOLFSSL_SNIFFER_WATCH`。

#### STATIC_BUFFER_LEN

レコードヘッダーからメモリを断片化しません。次のように展開されます: `RECORD_HEADER_SZ`。

#### STATIC_CHUNKS_ONLY

小さな静的バッファ（デフォルト）を使用していて、SSL_writeが保有するレコードより大きいデータを書き込もうとする場合、ユーザーが静的バッファのチャンク単位でのみ書き込むよう指定しない限り動的に取得しますが、このオプションで16K出力オプションを無効にできます。

#### WOLFSSL_DEF_PSK_CIPHER

ユーザー定義のPSK暗号を有効化します。

#### WOLFSSL_OLD_PRIME_CHECK

より高速なDHおよびRSAの素数チェックを使用する機能を有効化します。


#### WOLFSSL_OLD_SET_CURVES_LIST

旧形式で曲線を設定するアプリケーションとの後方互換性のため、旧スタイルの曲線リスト解析を使用します。

#### WOLFSSL_OLD_TIMINGPADVERIFY

CBC暗号スイートに対して、旧来のタイミングベースのパディング検証を使用します。新しい方式はより良い一定時間動作を提供します。

#### WOLFSSL_OLDTLS_AEAD_CIPHERSUITES

1.2より前のTLSバージョンでAEAD暗号スイート（GCM、CCM）を有効化します。これらのスイートは通常、TLS 1.2以降でのみ利用可能です。


#### WOLFSSL_OLDTLS_SHA2_CIPHERSUITES

1.2より前のTLSバージョンでSHA-2ベースの暗号スイートを有効化します。これらのスイートは通常、TLS 1.2以降でのみ利用可能です。

#### WOLFSSL_STATIC_RSA

静的暗号は強く非推奨であり、避けられるなら決して使用すべきではありません。しかし、静的暗号スイートのみをサポートするレガシーシステムがまだ存在します。そのため、静的RSA暗号スイートのみをサポートするレガシーピアに接続する必要がある場合は、これを使用してwolfSSLで静的RSAのサポートを有効化してください。（[`WOLFSSL_STATIC_PSK`](#wolfssl_static_psk)および[`WOLFSSL_STATIC_DH`](#wolfssl_static_dh)も参照してください）


#### WOLFSSL_STRONGEST_HASH_SIG

TLSハンドシェイク中の署名操作の実行時に、利用可能な最も強力なハッシュアルゴリズムを優先します。

#### WOLFSSL_STATIC_PSK

静的PSK暗号スイートを有効化する機能です。静的暗号は強く非推奨です。[`WOLFSSL_STATIC_RSA`](#wolfssl_static_rsa)を参照してください

#### WOLFSSL_STATIC_DH

静的ECDH暗号スイートを有効化する機能です。静的暗号は強く非推奨です。[`WOLFSSL_STATIC_RSA`](#wolfssl_static_rsa)を参照してください

#### HAVE_NULL_CIPHER

NULL暗号のサポートを有効にします。このオプションはセキュリティの観点から強く非推奨ですが、暗号化/復号操作を実行するには小さすぎるシステムも存在し、メッセージ改ざんを防ぐために、何もしないよりは少なくともメッセージとピアを認証する方がましです！

#### HAVE_ANON

匿名暗号スイートのサポートを有効にします。（決して推奨されませんが、Webから切り離された閉域または私設ネットワークに関わる正当なユースケースがいくつかあります）


#### HAVE_ATEXIT

プログラム終了時の自動クリーンアップのために、`wolfSSL_Cleanup()`を`atexit()`ハンドラとして登録します。

#### HAVE_LIBOQS

OpenQuantumSafeチームのliboqs統合のサポートを有効にします。詳細は本ドキュメントの付録「量子耐性暗号の実験」を参照してください。


#### HAVE_LIGHTY

wolfSSLとlighttpd Webサーバーの互換性に必要なAPIおよび動作を有効化します。

#### WOLFSSL_SP_4096

RSA/DH 4096ビットの単精度（SP）サポートを有効化します。

#### WOLFSSL_SP_384

ECC SECP384R1の単精度（SP）サポートを有効化します。`WOLFSSL_SP_MATH`にのみ適用されます。

#### WOLFSSL_SP_1024

SAKKEペアリングベース暗号の単精度（SP）サポートを有効化します。

#### ATOMIC_USER

Atomic Record Layerコールバックを有効化します。

#### BIG_ENDIAN_ORDER

エンディアン - デフォルトはリトルエンディアンです。すなわち、ビッグエンディアンにします。

#### WOLFSSL_32BIT_MILLI_TIME

TimeNowInMilliseconds()関数が符号なし32ビット値を返すようにします。デフォルトの動作は符号付き64ビット値を返します。


#### WOLFSSL_MAX_DHKEY_BITS

DHの最大ビットサイズは8の倍数でなければなりません。DHの最大ビットサイズは16384を超えたり、`WOLFSSL_MIN_DHKEY_BITS`より大きくなってはなりません。

#### WOLFSSL_MIN_DHKEY_BITS

DHの最小ビットサイズは8の倍数でなければなりません。112ビットのセキュリティのためには、DHは少なくとも2048ビットの鍵を必要とし、最小ビットサイズは16000より大きくてはなりません。

#### WOLFSSL_MAX_MTU

想定される最大MTU。UDPおよびIPヘッダーを考慮して1500 - 100バイトです。

#### IGNORE_NETSCAPE_CERT_TYPE

netscape証明書タイプを入力するために定義しますが、場所を確保するだけです。

#### SESSION_CERTS

証明書用のTLSセッションキャッシュです。

#### WOLFSSL_DUAL_ALG_CERTS

デュアルアルゴリズム証明書に必要な機能です。

#### CRL_MAX_REVOKED_CERTS

RevokedCertsを保持するバッファの数を指定します。デフォルト値は4に設定されています。

#### CRL_STATIC_REVOKED_LIST

二分探索を可能にするために、RevokedCertsの固定静的リストを有効化します。

#### SESSION_INDEX

キャッシュ内のセッションの位置を識別します。インデックスのセッション/行シフトを指定します。

#### SESSION_TICKET_HINT_DEFAULT

チケットヒントのデフォルトは、デフォルトのヒント値を設定するために使用されます。チケット鍵のライフタイムはチケットライフヒントより長くなければなりません。

#### WOLFSSL_DTLS13

wolfSSL DTLS 1.3を有効化します。

#### WOLFSSL_TLS13

TLS 1.3プロトコル実装を有効化します。

#### WOLFSSL_TLS13_IGNORE_AEAD_LIMITS

https://tex2e.github.io/rfc-translater/html/rfc9147#4-5-3--AEAD-Limits で指定されている制限です。鍵更新を行う必要がある制限を、復号のハード失敗制限の中間点として指定します。


#### WOLFSSL_TLS13_DRAFT

ドラフト実装に対するテストのために、ドラフトTLS 1.3仕様のパラメータを使用します。本番環境での使用向けではありません。

#### WOLFSSL_TLS13_MIDDLEBOX_COMPAT

TLS 1.3ハンドシェイクでミドルボックス互換性を有効化します。これには、暗号化メッセージの前にChangeCipherSpecを送信することと、セッションIDを含めることが含まれます。


#### WOLFSSL_TLS13_IGNORE_PT_ALERT_ON_ENC

TLS 1.3で暗号化レコードが期待されているときに受信した平文アラートを無視します。一部の実装との相互運用性が向上する可能性があります。

#### WOLFSSL_TLS13_SHA512

ハンドシェイクでのSHA-512ダイジェストの生成を許可します - 現時点でSHA-512を必要とする暗号スイートはありません。これにより、TLS v1.3ではまだ使用されていないものの、ハンドシェイクメッセージに対するSHA2-512ハッシュの計算が有効化されます。

#### WOLFSSL_TLS13_TICKET_BEFORE_FINISHED

サーバーがクライアントのFinishedメッセージを受信する前にNewSessionTicketメッセージを送信することを許可します。TLS 1.3仕様のセクション4.6.1、第4段落を参照してください。

#### WOLFSSL_EARLY_DATA

TLS 1.3の0-RTT（Zero Round Trip Time）early dataサポートを有効化します。より高速な接続確立のために、クライアントがハンドシェイクの最初のフライトでアプリケーションデータを送信できるようにします。PSKまたはセッションチケットによるセッション再開が必要です。

#### WOLFSSL_EARLY_DATA_GROUP

送信時にearly dataメッセージをClientHelloとグループ化します。メッセージを結合することでネットワークの往復回数を削減します。

#### WOLFSSL_CHECK_SIG_FAULTS

フォールトインジェクション攻撃を検出するために、署名後にECC署名を検証します。ハードウェアフォールト攻撃が懸念される環境で有用です。


#### WOLFSSL_CIPHER_INTERNALNAME

APIを通じて暗号スイート情報を報告する際に、IANA標準名の代わりにwolfSSL内部の暗号スイート名を使用します。

#### WOLFSSL_PSK_ID_PROTECTION

TLS 1.3でPSKアイデンティティ保護を有効化します。受動的な観測者がPSKアイデンティティによってクライアントを追跡することを防ぐために、PSKアイデンティティを暗号化します。

#### WOLFSSL_NO_CLIENT_CERT_ERROR

有効化すると、サーバーはクライアントに有効な証明書の送信を要求します。クライアントが証明書を提供しない場合、ハンドシェイクはエラーで失敗します。

#### WOLFSSL_NONBLOCK_OCSP

ノンブロッキングのOCSPステープリング処理を有効化します。TLSハンドシェイク中にOCSPルックアップを非同期で実行できるようにします。

#### WOLFSSL_TLS_OCSP_MULTI

TLSでの複数のOCSPレスポンスのサポートを有効化し、エンドエンティティ証明書に加えて中間証明書に対するOCSPレスポンスのステープリングを可能にします。

#### WOLFSSL_CERT_SETUP_CB

TLS 1.3ハンドシェイク中に呼び出される証明書セットアップコールバックを有効化します。ClientHelloの内容に基づいた動的な証明書および鍵の選択を可能にします。

#### WOLFSSL_RW_THREADED

読み取り/書き込みスレッディングサポートを有効化し、別々のスレッドが同一のSSLセッション上でTLSの読み取りと書き込み操作を同時に実行できるようにします。

#### WOLFSSL_PRIORITIZE_PSK

TLS 1.3ハンドシェイク中に、暗号スイートを選択する際に暗号スイートの順序ではなくPSKの順序を優先します。PSKコールバックの順序が優先度を決定します。

#### WOLFSSL_UIP

CONTIKIが定義されている場合、UIPの実装です。

#### TLS13_MAX_TICKET_AGE

チケットの最大有効期間を指定します。TLS 1.3では、これは7日間です。

#### TLS13_TICKET_NONCE_STATIC_SZ

TLS13_TICKET_NONCE_STATIC_SZはこの`FIPS_VERSION_GE`ではサポートされていません。


#### TIME_OVERRIDES

システムの時刻関数を使用する代わりに、アプリケーションがカスタム時刻関数（`XTIME`、`XGMTIME`など）を提供します。

#### TLS13_TICKET_NONCE_MAX_SZ

チケットノンスの最大サイズのバージョンを定義します。最大サイズは255バイトとして定義されています。

#### WOLFSSL_TICKET_ENC_AES128_GCM

デフォルトコールバックでセッションチケットの暗号化/復号にAES128-GCMを使用します。これはサーバー専用です。ChaCha20/Poly1305がコンパイルされていない場合、これがデフォルトのアルゴリズムです。

#### WOLFSSL_TICKET_ENC_AES256_GCM

デフォルトコールバックでセッションチケットの暗号化/復号にAES256-GCMを使用します。サーバー専用です。

#### WOLFSSL_TICKET_ENC_CHACHA20_POLY1305

デフォルトコールバックでセッションチケットの暗号化/復号にChaCha20-Poly1305を使用します。何も定義されていない場合、デフォルトのアルゴリズムが使用され、アルゴリズムがコンパイルされます。これはサーバー専用です。


#### WOLFSSL_TICKET_ENC_CBC_HMAC

セッションチケットの暗号化に、AEAD暗号の代わりにCBC+HMACを使用します。AEADサポートのないビルドに代替手段を提供します。

#### WOLFSSL_TICKET_EXTRA_PADDING_SZ

チケットの追加パディングサイズを定義します。32として定義されています。

#### WOLFSSL_TICKET_HAVE_ID

チケットがIDを持っていることを確認するために使用します。サポートが組み込まれており、チケットにIDが含まれている場合にのみキャッシュに追加します。そうでなければ、キャッシュからチケットを実際に取得する方法がありません。

#### WOLFSSL_TICKET_KEY_LIFETIME

デフォルトのライフタイムは、その鍵で最初のチケットが発行されてから1時間です。ヒントより長くなければなりません。

#### WOLFSSL_TICKET_NONCE_MALLOC

チケットノンスの動的割り当てを有効化します。HKDF expandコールバックを無効化する必要があります。

#### SHOW_CERTS

定義すると、証明書を出力します。組込みデバッグに使用します。

#### SHOW_SECRETS

デバッグに使用します。該当するシークレットを表示します。


#### SHOW_SIZES

初期化時に主要なwolfSSL構造体のサイズを表示します。制約のあるシステムでのデバッグとメモリ分析に有用です。

#### DEBUG_UNIT_TEST_CERTS

名前制約テストのデバッグ時に使用されます。複雑なdefineガードを伴う複数の場所で使用できるようにstaticではありません。

#### DEBUG_WOLFSSL_VERBOSE

`OPENSSL_EXTRA`または`DEBUG_WOLFSSL_VERBOSE`マクロを使用すると、`WOLFSSL_ERROR`は新しい関数`WOLFSSL_ERROR_LINE`にマップされます。この関数は`WOLFSSL_ERROR`が呼び出された行番号と関数名を取得します。

#### SOCKET_INVALID

無効なソケットを定義するために使用され、-1として定義されています。

#### WOLFSSL_SOCKET_INVALID

テストに使用され、無効なソケットを示すために使用される値のオーバーライドのみを許可します。通常は-1です。

#### WOLFSSL_SOCKET_IS_INVALID

ソケット処理で使用されます。

#### WOLFSSL_SRTP

SRTPを有効化するために使用します。

#### WOLFSSL_CIPHER_CHECK_SZ

暗号チェックサイズとして定義されており、暗号化操作が成功したことを確認するために64ビットを必要とします。

#### DTLS_CID_MAX_SIZE

DTLS 1.3のパース処理コードは、レコードを復号するためにレコードヘッダーを静的バッファにコピーします。CIDの最大サイズを増やすとこのバッファも大きくなり、セッションごとの実行時メモリフットプリントに影響します。DTLS CIDの最大サイズは255バイトです。

#### DTLS13_EPOCH_SIZE

DTLS 1.3エポックに関する移植性の改善です。DTLSエポックに紐づく鍵を保存し、必要なときに正しい鍵/エポックを設定する方法を実装します。

#### DTLS13_RETRANS_RN_SIZE

DTLS 1.3に関する移植性の改善です。DTLS 1.3で再送前のサイズを識別するために使用されます。

#### WOLFSSL_DTLS_FRAG_POOL_SZ

指定された時間あたりに許可されるフラグメント数を定義します。


#### WOLFSSL_DTLS_MTU

DTLS通信中の最大データグラムサイズを制御するための、DTLS MTU（Maximum Transmission Unit）管理APIを有効化します。

#### WOLFSSL_CLIENT_SESSION_DEFINED

APIが使用する不透明な構造体を宣言します。

#### WOLFSSL_COND

このシステムがシグナリングをサポートしている場合に定義されます。COND_TYPE - シグナリングAPIに渡すべき型です。

#### WOLFSSL_DTLS_CH_FRAG

サーバーが、フラグメント化された2番目の/検証済み（有効なクッキーレスポンスを含む）ClientHelloメッセージを処理できるようにします。最初の/未検証（クッキー拡張のない）ClientHelloは、DTLSサーバーがステートレスに処理できるように、フラグメント化されていない必要があります。これはDTLS 1.3に対してのみ実装されています。実行時にこれを明示的に有効化するには、ユーザーはサーバー側で`wolfSSL_dtls13_allow_ch_frag()`を呼び出す必要があります。注: `WOLFSSL_DTLS_CH_FRAG`なしでDTLS 1.3 + pqcを使用すると、おそらく失敗します。その場合は`--enable-dtls-frag-ch`を使用して有効化してください。

#### WOLFSSL_DTLS_MTU_ADDITIONAL_READ_BUFFER

自分とわずかに異なるMTUを持つピアと連携できるように、読み取り用の追加バイトが必要です。

#### WOLFSSL_DTLS_WINDOW_WORDS

ストレージサイズをチェックしたり、インデックスがウィンドウに対して有効であるかを検証するために使用されます。

#### WOLFSSL_EXPORT_SPC_SZ

CipherSpecsから使用されるバイト量を指定するために定義します。

#### WOLFSSL_MIN_DOWNGRADE

最小ダウングレードバージョンを指定します。

#### WOLFSSL_MIN_DTLS_DOWNGRADE

DTLSの最小ダウングレードバージョンを指定します。

#### WOLFSSL_MIN_ECC_BITS

許可される最小のECC鍵サイズを設定できます。

#### WOLFSSL_MIN_RSA_BITS

デフォルトでは、wolfSSLはRSA鍵サイズを最小1024ビットに制限しています。512ビット鍵のような、より小さく安全性の低いRSA鍵のデコードを許可するには、コンパイラフラグ`-DWOLFSSL_MIN_RSA_BITS=512`をCFLAGSまたはCPPFLAGSに追加するか、ユーザー設定ヘッダーで定義する必要があります。

#### WOLFSSL_MODE_AUTO_RETRY_ATTEMPTS

無限リトライループの可能性を制限するために使用されます。

#### WOLFSSL_MULTICAST

DTLSマルチキャスト機能です。

#### WOLFSSL_MULTICAST_PEERS

マルチキャスト機能で、許可される最大ピア数を100として定義しています。


#### WOLFSSL_MYSQL_COMPATIBLE

wolfSSLでMySQLプロトコル互換の動作を有効化します。MySQLのTLSプロバイダーとしてwolfSSLを使用する場合に必要です。

#### WOLFSSL_NAMES_STATIC

位置独立コード（PIC）のために静的なECC構造体を使用します。

#### WOLFSSL_SEND_HRR_COOKIE

DTLS 1.3で使用されるTLS拡張です。

#### WOLFSSL_SEP

証明書ポリシーセット拡張の機能です。


#### WOLFSSL_SECURE_RENEGOTIATION_ON_BY_DEFAULT

明示的なAPI呼び出しを必要とせずに、すべての新しいSSLコンテキストに対してデフォルトでセキュアリネゴシエーションを有効化します。

#### WOLFSSL_SESSION_ID_CTX

アプリケーションのセッションコンテキストIDをコピーするために使用されます。

#### WOLFSSL_SESSION_TIMEOUT

デフォルトのセッション再開キャッシュのタイムアウト（秒単位）で、タイムアウトを手動で定義するために使用されます。


#### WOLFSSL_SET_CIPHER_BYTES

名前文字列の代わりに生の2バイト値で暗号スイートを設定できるようにします。プログラムによる暗号スイート設定に有用です。

#### KEEP_OUR_CERT

SSL証明書を返す機能を保証するために使用されます。

#### KEEP_PEER_CERT

ピア証明書を保持します。OpenSSL互換レイヤーの一部はピア証明書を必要とします。

#### WOLFSSL_SIGNER_DER_CERT

これは署名に使用されたDER/ASN.1の保持を有効化します。これは互換レイヤーで使用され、その例として`wolfSSL_X509_STORE_get1_certs`があります。

#### CA_TABLE_SIZE

wolfSSL証明書マネージャーの署名者テーブルで使用されます。デフォルトの`CA_TABLE_SIZE`は11ですが、実際のニーズに基づいて調整できます。各`WOLFSSL_CTX`は独自の証明書マネージャー（CM）を持ちます。

#### ECDHE_SIZE

これをコンパイル時にオーバーライドできるように定義します。ECDHEサーバーサイズはデフォルトで256ビットで、事前に決められたECDHE曲線サイズを設定できます。デフォルトは32バイトです。

#### CIPHER_NONCE

認証で実装される暗号学的な数値として使用されます。これは疑似乱数であり、完全性のみの暗号スイートです。

#### WOLFSSL_USE_POPEN_HOST

CRLおよびOCSPで使用されるwolfio.cのソケットオープンコードにおいて、ホストとポストを使用したソケット作成にpopenを使用します。

#### CloseSocket

ソケットのクローズに使用される関数をオーバーライドする方法です。CRL、OCSP、BIOで使用されます。

#### CONFIG_POSIX_API

ネットワークシステムコールのPOSIX名を有効化します。

#### WOLFSSL_USER_CURRTIME

マクロ`WOLFSSL_USER_CURRTIME`を使用して、gettimeofday関数を使わずにtest.hで使用するオプションを追加します。

#### WOLFSSL_USER_MUTEX

`WOLFSSL_USER_MUTEX`によるユーザー定義ミューテックスのオプションです。

#### DEFAULT_MIN_ECCKEY_BITS

ECC鍵の最小ビット数を識別します。

#### DEFAULT_MIN_RSAKEY_BITS

RSA鍵の最小ビット数を識別します。

#### EXTERNAL_SERIAL_SIZE

X509シリアル番号を符号なしバイナリでバッファに書き込む、生のシリアル番号バイトです。いずれの場合も、バッファは少なくとも`EXTERNAL_SERIAL_SIZE`（32）である必要があります。成功時には`WOLFSSL_SUCCESS`を返します。注: これはユーザーが定義できない内部マクロです。

#### LARGE_STATIC_BUFFERS

組込みコールバックには大きな静的バッファが必要です。バッファを16Kに拡大するオプションが与えられていることを確認してください。

#### LIBWOLFSSL_VERSION_STRING

これは、リリースバンドル用に、または`./configure`実行時に設定されるwolfSSLバージョン文字列です。`LIBWOLFSSL_VERSION_HEX`には、その32ビットHEX版もあります。これらはwolfssl/version.hに由来します。


### wolfSSLのカスタマイズおよび移植

#### WOLFSSL_USER_SETTINGS

定義すると、ユーザー固有の設定ファイルの使用が可能になります。ファイルは`user_settings.h`という名前で、インクルードパスに存在する必要があります。標準の`settings.h`ファイルより前にインクルードされるため、デフォルト設定をオーバーライドできます。

#### WOLFSSL_CALLBACKS

この拡張は、デバッガのない環境でシグナルを使用したデバッグコールバックを可能にします。デフォルトではオフです。ブロッキングソケットでタイマーを設定するためにも使用できます。詳細については[コールバック](chapter06.md#callbacks)を参照してください。

#### WOLF_CRYPTO_CB

 暗号コールバックサポートを有効化します。この機能は、[`--enable-cryptocb`](#--enable-cryptocb)を使用した場合にも自動的に有効化されます。

#### WOLF_CRYPTO_CB_FIND

デバイスIDによって登録済みの暗号デバイスを検索するための、デバイス検索コールバック関数を有効化します。[`WOLF_CRYPTO_CB`](#wolf_crypto_cb)が必要です。

#### WOLF_CRYPTO_CB_CMD

暗号コールバックデバイスの登録および登録解除時に呼び出されるコマンドコールバック関数を有効化します。[`WOLF_CRYPTO_CB`](#wolf_crypto_cb)が必要です。

#### WOLF_CRYPTO_CB_COPY

アルゴリズム構造体のコピーコールバックを有効化し、ハッシュおよび暗号の状態を暗号コールバックフレームワーク経由でコピーできるようにします。[`WOLF_CRYPTO_CB`](#wolf_crypto_cb)が必要です。

#### WOLF_CRYPTO_CB_FREE

アルゴリズム構造体の解放コールバックを有効化し、暗号オブジェクトのクリーンアップを暗号コールバックフレームワーク経由で行えるようにします。[`WOLF_CRYPTO_CB`](#wolf_crypto_cb)が必要です。

#### WOLF_CRYPTO_CB_AES_SETKEY

AES鍵セットアップ操作に対する暗号コールバックサポートを有効化します。ハードウェアが鍵スケジューリングを処理できるようにします。[`WOLF_CRYPTO_CB`](#wolf_crypto_cb)が必要です。

#### WOLF_CRYPTO_CB_RSA_PAD

RSAパディング操作に対する暗号コールバックサポートを有効化し、ハードウェアまたは外部モジュールによるカスタムパディング処理を可能にします。[`WOLF_CRYPTO_CB`](#wolf_crypto_cb)が必要です。

#### DEBUG_CRYPTOCB

暗号コールバック操作に対するデバッグ用InfoString関数を有効化します。どの暗号操作がハードウェアにルーティングされているかをデバッグする際に有用です。

#### WC_USE_DEVID

ハードウェア固有のデバイス（CAAMなど）が検出されない場合に、暗号コールバックに使用するデフォルトのデバイスIDを指定します。

#### WC_NO_DEFAULT_DEVID

暗号コールバックフレームワークでの自動的なデフォルトデバイスID選択を無効化します。定義すると、アプリケーションはすべての暗号操作に対してデバイスIDを明示的に渡す必要があります。

#### WOLFSSL_CAAM_DEVID

NXP CAAMハードウェア暗号用のデバイスID定数（値7）を定義します。デフォルトデバイスID選択ロジックで使用されます。

#### NO_SHA2_CRYPTO_CB

SHA-384およびSHA-512操作に対する暗号コールバックサポートを無効化します。定義すると、これらのハッシュ操作は常にソフトウェア実装を使用します。

#### WOLF_CRYPTO_CB_ONLY_RSA

RSA操作を暗号コールバックのみの使用に制限し、すべてのソフトウェアRSA実装を無効化します。RSAを完全にハードウェアに委譲すべき場合に有用です。

#### WOLFSSL_DYN_CERT

WOLFSSL_NO_MALLOCが設定されている場合でも、証明書のパース時にsubjectCNおよびpublicKeyフィールドの割り当てを許可します。WOLFSSL_NO_MALLOCオプションをRSA証明書とともに使用する場合、ピアの証明書上の証明書を検証するために、CAの公開鍵を保持する必要があります。ca->publicKeyがNULLであるため、これはConfirmSignatureエラー -173 BAD_FUNC_ARGとして現れます。

#### WOLFSSL_USER_IO

デフォルトのI/O関数[`EmbedSend()`](wolfio_8h.md#function-embedsend)および[`EmbedReceive()`](wolfio_8h.md#function-embedreceive)の自動設定をユーザーが削除できるようにします。カスタムI/O抽象化レイヤーに使用されます（詳細については[抽象化レイヤー](chapter05.md#abstraction-layers)を参照してください）。

#### NO_FILESYSTEM

証明書や鍵ファイルの読み込みにstdioが利用できない場合に使用されます。これにより、ファイル用の拡張の代わりにバッファ用の拡張の使用が有効化されます。

#### NO_INLINE

小さく頻繁に使用される関数の自動インライン化を無効化します。これをオンにするとwolfSSLは遅くなり、これらは通常、関数呼び出しのセットアップ/リターンよりずっと小さい関数であるため、実際にはサイズが大きくなります。autoconfを使用していない場合は、コンパイル対象ファイルのリストに`wolfcrypt/src/misc.c`を追加する必要もあります。

#### NO_DEV_RANDOM

デフォルトの`/dev/random`乱数生成器の使用を無効化します。定義した場合、ユーザーはOS固有の`GenerateSeed()`関数（`wolfcrypt/src/random.c`にあります）を書く必要があります。

#### NO_MAIN_DRIVER

通常のビルド環境において、テストアプリケーションが単独で呼び出されるのか、testsuiteドライバーアプリケーションを通じて呼び出されるのかを判定するために使用されます。テストファイル`test.c`、`client.c`、`server.c`、`echoclient.c`、`echoserver.c`、`testsuite.c`でのみ使用する必要があります

#### NO_WRITEV

`writev()`セマンティクスのシミュレーションを無効化します。

#### SINGLE_THREADED

ミューテックスの使用をオフにするスイッチです。wolfSSLは現在、セッションキャッシュに対して1つだけ使用しています。wolfSSLの使用が常にシングルスレッドである場合、これをオンにできます。

#### USER_TICKS

time(0)を使用したくない場合に、ユーザーが独自のクロックティック関数を定義できるようにします。カスタム関数は秒単位の精度が必要ですが、Epochと相関している必要はありません。`wolfssl_int.c`の`LowResTimer()`関数を参照してください。

#### USER_TIME

ユーザーが独自の構造体を使用したい（または使用する必要がある）場合に、time.hの構造体の使用を無効化します。実装の詳細については`wolfcrypt/src/asn.c`を参照してください。ユーザーは`XTIME()`、`XGMTIME()`、`XVALIDATE_DATE()`を定義および/または実装する必要があります。

#### USE_CERT_BUFFERS_256

`<wolfssl_root>/wolfssl/certs_test.h`にある256ビットのテスト証明書および鍵バッファを有効化します。ファイルシステムのない組込みシステム上でのテストや移植の際に役立ちます。

#### USE_CERT_BUFFERS_1024

`<wolfssl_root>/wolfssl/certs_test.h`にある1024ビットのテスト証明書および鍵バッファを有効化します。ファイルシステムのない組込みシステム上でのテストや移植の際に役立ちます。

#### USE_CERT_BUFFERS_2048

`<wolfssl_root>/wolfssl/certs_test.h`にある2048ビットのテスト証明書および鍵バッファを有効化します。ファイルシステムのない組込みシステム上でのテストや移植の際に役立ちます。

#### USE_CERT_BUFFERS_3072

`<wolfssl_root>/wolfssl/certs_test.h`にある3072ビットのテスト証明書および鍵バッファを有効化します。ファイルシステムのない組込みシステム上でのテストや移植の際に役立ちます。

#### USE_CERT_BUFFERS_4096

`<wolfssl_root>/wolfssl/certs_test.h`にある4096ビットのテスト証明書および鍵バッファを有効化します。ファイルシステムのない組込みシステム上でのテストや移植の際に役立ちます。

#### USE_CERT_BUFFERS_25519

`<wolfssl_root>/wolfssl/certs_test.h`にあるEd25519のテスト証明書および鍵バッファを有効化します。ファイルシステムのない組込みシステム上でのテストや移植の際に役立ちます。

#### USE_WOLFSSL_IO

このマクロはsend/recvへのコールバックを有効化します。使用例はこちらにあります: (https://github.com/wolfSSL/wolfssl-examples/blob/master/tls/client-tls-callback.c#L6)

#### CUSTOM_RAND_GENERATE_SEED

ユーザーが`wc_GenerateSeed(byte* output, word32 sz)`と等価なカスタム関数を定義できるようにします。

#### CUSTOM_RAND_GENERATE_BLOCK

ユーザーがカスタム乱数生成関数を定義できるようにします。使用例は以下の通りです。

```sh
./configure --disable-hashdrbg
CFLAGS="-DCUSTOM_RAND_GENERATE_BLOCK= custom_rand_generate_block"
```

または

```c
/* RNG */
/* #define HAVE_HASHDRBG */
extern int custom_rand_generate_block(unsigned char* output, unsigned int sz);
```

#### WC_NO_RNG

すべてのRNG（乱数生成器）サポートを無効化します。アプリケーションが独自の乱数データを提供する場合や、乱数が不要な場合（決定的な操作のみなど）に使用します。

#### HAVE_HASHDRBG

NIST SP 800-90Aに準拠したハッシュベースの決定的乱数ビット生成器（DRBG）を有効化します。これは、SHA-256を基盤ハッシュとして使用するwolfSSLのデフォルトRNG実装です。

#### WC_RNG_BLOCKING

RNG操作をブロッキングにし、エラーを返すのではなく一時的な失敗時にリトライします。エントロピー源が一時的に利用できなくなる可能性のあるプラットフォームで有用です。

#### WC_VERBOSE_RNG

RNG操作に対する詳細なデバッグ出力を有効化します。シード生成、DRBGの状態、ヘルステストの結果に関する詳細情報を出力します。


#### WC_X25519_NONBLOCK

ノンブロッキングのX25519鍵合意操作を有効化します。X25519の計算を中断・再開できるようにし、協調的マルチタスクに有用です。

#### WC_RNG_SEED_CB

DRBG用のカスタムシードコールバック関数を有効化します。アプリケーションが`wc_SetSeed_Cb()`を介して独自のエントロピー源を提供できるようにします。

#### WC_RNG_BANK_SUPPORT

乱数データを事前生成するためのRNGバンクサポートを有効化します。乱数バイトを事前にバッファリングし、以降の乱数要求を高速化できるようにします。

#### WOLFSSL_RNG_USE_FULL_SEED

DRBGのシードに、必要最小限ではなくフルシード長（384ビット）を使用します。追加のエントロピーマージンを提供します。

#### WOLFSSL_GENSEED_FORTEST

テスト目的で決定的なシード源を使用します。警告: これは予測可能な乱数出力を生成するため、本番環境では決して使用してはなりません。

#### WOLFSSL_KEEP_RNG_SEED_FD_OPEN

シード操作のたびに開閉するのではなく、`/dev/random`または`/dev/urandom`のファイルディスクリプタをシード操作間で開いたまま保持します。頻繁に再シードするシステムでのオーバーヘッドを削減します。

#### CUSTOM_RAND_GENERATE

ユーザーがカスタムのランダムワード生成関数を定義できるようにします。この関数は単一のランダムワード（`unsigned int`）を返す必要があります。

#### CUSTOM_RAND_GENERATE_SEED_OS

ユーザーがOSレベルのカスタムシード生成関数を定義できるようにし、デフォルトのプラットフォーム固有の`GenerateSeed()`を置き換えつつ、その上でwolfSSLのDRBGを引き続き使用します。

#### HAVE_ENTROPY_MEMUSE

メモリ使用ベースのエントロピー源を有効化します。このエントロピー源は、メモリアクセスパターンのタイミング変動（キャッシュヒット/ミス）を測定して、DRBGシード用のエントロピーを生成します。

#### ENTROPY_MEMUSE_FORCE_FAILURE

メモリ使用エントロピー源を強制的に失敗させます。エントロピー収集コードのエラー処理パスをテストするために使用されます。

#### HAVE_GETRANDOM

エントロピー収集にLinuxの`getrandom()`システムコールが利用可能であることを示します。サポートされているプラットフォームでは自動的に検出されます。

#### WOLFSSL_GETRANDOM

Linuxシステム上でDRBGシード用のエントロピー源として`getrandom()`システムコールの使用を有効化します。十分なエントロピーが利用可能になるまでブロックするため、`/dev/urandom`から読み取るよりも信頼性が高くなります。

#### FORCE_FAILURE_GETRANDOM

`getrandom()`システムコールを強制的に失敗させます。フォールバックのエントロピー源パスをテストするために使用されます。

#### NO_DEV_URANDOM

乱数シードのための`/dev/urandom`の使用を無効化します。[`NO_DEV_RANDOM`](#no_dev_random)とともに定義した場合、代替のシード源を提供する必要があります。

#### HAVE_AMD_RDSEED

直接的なハードウェアエントロピーのために、AMDのRDSEED命令の使用を有効化します。[`HAVE_INTEL_RDSEED`](#have_intel_rdseed)と同様ですが、AMDプロセッサ用です。

#### IDIRECT_DEV_RANDOM

iDirectプラットフォームで、デフォルトの`/dev/random`の代わりにランダムデバイスのカスタムパスを指定します。

#### WIN_REUSE_CRYPT_HANDLE

Windowsの`CryptContext`ハンドルを、毎回取得と解放を行うのではなく、乱数生成呼び出し間で再利用します。Windowsでのパフォーマンスを改善します。

#### WC_RNG_SEED_APT_CUTOFF

DRBGの適応比率テスト（APT）のカットオフ値を設定します。APTは、ウィンドウ内で単一の値が頻繁に出現しすぎていないかをチェックすることで、エントロピー源の劣化を検出します。

#### WC_RNG_SEED_APT_WINDOW

DRBGの適応比率テスト（APT）のウィンドウサイズを設定します。各テストウィンドウで検査されるサンプル数を定義します。

#### WC_RNG_SEED_RCT_CUTOFF

DRBGの反復カウントテスト（RCT）のカットオフ値を設定します。RCTは、連続した同一の出力をチェックすることで、エントロピー源の致命的な障害を検出します。

#### STM32_RNG

エントロピー収集のためのSTM32ハードウェア乱数生成器ペリフェラルを有効化します。

#### STM32_NUTTX_RNG

NuttX RTOSの`/dev/random`インターフェースを通じたSTM32ハードウェアRNGアクセスを有効化します。

#### WOLFSSL_STM32F427_RNG

STM32F427マイクロコントローラ固有のハードウェアRNGサポートを有効化します。

#### WOLFSSL_STM32_RNG_NOLIB

STM32 HALライブラリを使用せずに、STM32 RNGペリフェラルへの直接レジスタアクセスを有効化します。ベアメタルの展開に有用です。

#### WOLFSSL_PIC32MZ_RNG

エントロピー収集のためのMicrochip PIC32MZハードウェア乱数生成器を有効化します。

#### FREESCALE_RNGA

Freescale/NXP RNGA（乱数生成器アクセラレータ）ハードウェアペリフェラルを有効化します。

#### FREESCALE_K70_RNGA

Freescale/NXP Kinetis K70マイクロコントローラファミリ固有のRNGAサポートを有効化します。

#### FREESCALE_RNGB

Freescale/NXP RNGB（乱数生成器バージョンB）ハードウェアペリフェラルを有効化します。

#### FREESCALE_KSDK_2_0_RNGA

KSDK 2.0 SDKドライバーインターフェースを通じたFreescale/NXP RNGAを有効化します。

#### FREESCALE_KSDK_2_0_TRNG

KSDK 2.0 SDKドライバーインターフェースを通じたFreescale/NXP TRNG（真性乱数生成器）を有効化します。

#### MAX3266X_RNG

Maxim MAX3266Xハードウェア乱数生成器を有効化します。

#### QAT_ENABLE_RNG

Intel QuickAssist Technology（QAT）アクセラレータを通じたハードウェアRNGを有効化します。

#### WOLFSSL_ATECC_RNG

Microchip ATECC508A/ATECC608AセキュアエレメントからのハードウェアRNGを有効化します。

#### WOLFSSL_SILABS_TRNG

エントロピー収集のためのSilicon Labs真性乱数生成器（TRNG）を有効化します。

#### WOLFSSL_SCE_NO_TRNG

Renesas Secure Crypto Engine（SCE）上のTRNGを無効化します。AESやその他のSCE機能は引き続き利用可能ですが、RNGはソフトウェア実装を使用します。

#### WOLFSSL_SCE_TRNG_HANDLE

乱数生成に使用するRenesas SCE TRNGハンドルを指定します。



#### WOLFSSL_SE050_NO_TRNG

NXP SE050セキュアエレメント上のTRNGを無効化します。その他のSE050暗号操作は引き続き利用可能です。

#### WOLFSSL_PSA_NO_RNG

Platform Security Architecture (PSA)暗号APIを通じたRNGを無効化します。PSAが有効化されているがRNGには別のソースを使用したい場合に使用します。

#### HAVE_IOTSAFE_HWRNG

IoT-Safe準拠のSIMカードまたはセキュアエレメントからのハードウェアRNGを有効化します。

#### WOLFSSL_XILINX_CRYPT_VERSAL

Xilinx Versalプラットフォーム上での暗号ハードウェアサポートを有効化します。これにはエントロピー収集用のハードウェアTRNGが含まれます。

#### NO_PUBLIC_GCM_SET_IV

独自のカスタムハードウェアポートを行い、[`wc_AesGcmSetIV()`](group__AES.md#function-wc_aesgcmsetiv)のパブリックな実装を提供していない場合に使用してください

#### NO_PUBLIC_CCM_SET_NONCE

独自のカスタムハードウェアポートを行い、`wc_AesGcmSetNonce()`のパブリックな実装を提供していない場合に使用してください

#### NO_GCM_ENCRYPT_EXTRA

独自のカスタムハードウェアポートを行い、`wc_AesGcmEncrypt_ex()`の実装を提供していない場合に使用してください

#### WOLFSSL_STM32[F1 | F2 | F4 | F7 | L4]

対応するSTM32デバイス向けにビルドする場合、これらの定義のいずれかを使用してください。wolfSSL移植ガイド（<https://www.wolfssl.com/docs/porting-guide/>）に従って、`wolfssl-root/wolfssl/wolfcrypt/settings.h`のセクションを適宜更新してください。

#### WOLFSSL_STM32_CUBEMX

CubeMXツールを使用してHardware Abstraction Layer (HAL) APIを生成する場合、この設定を使用してwolfSSLに適切なサポートを追加してください。

#### WOLFSSL_CUBEMX_USE_LL

CubeMXツールを使用してAPIを生成する場合、HAL (Hardware Abstraction Layer)またはLow Layer (LL)の2つのオプションがあります。この定義を使用して、`wolfssl-root/wolfssl/wolfcrypt/settings.h`の`WOLFSSL_STM32[F1/F2/F4/F7/L4]`セクションでどのヘッダーをインクルードするかを制御します。

#### NO_STM32_CRYPTO

STM32パーツがハードウェア暗号サポートを提供していない場合に使用します

#### NO_STM32_HASH

STM32パーツがハードウェアハッシュサポートを提供していない場合に使用します

#### NO_STM32_RNG

STM32パーツがハードウェアRNGサポートを提供していない場合に使用します

#### XTIME_MS

TLS 1.3使用時にミリ秒単位の時刻を取得するために使用する関数をマップするマクロです。例:

```c
extern time_t m2mb_xtime_ms(time_t * timer);
#define XTIME_MS(tl) m2mb_xtime_ms((tl))
```

#### WOLFSSL_CIPHER_TEXT_CHECK

TLS接続中のAES暗号化操作に対するグリッチング攻撃の可能性をチェックするには、これを定義してください。

#### RTTHREAD

RT-THREADマクロは、rtthread IoTをwolfSSLに移植する際に使用されます。

#### SO_REUSEPORT

ローカルアドレスとポートの再利用を許可します。

#### INTIME_RTOS

INtime RTOS向けの移植設定です。

#### WOLFSSL_SGX

SGXへの移植時に使用されます。


### メモリまたはコード使用量の削減

#### TFM_TIMING_RESISTANT

スタックサイズが小さいシステム上でfast math（[`USE_FAST_MATH`](#use_fast_math)）を使用する場合に定義できます。これにより大きな静的配列が取り除かれます。

#### ECC_TIMING_RESISTANT

これはタイミング耐性機能として使用され、サイドチャネル攻撃および差分電力解析（DPA）攻撃を防ぐためにecc.c内のコードを有効化します。

#### FUSION_RTOS

Fusion RTOSの実装は、チケットが最初に確認された時刻と送信された時刻の差を表すために使用されます。時刻をミリ秒単位の32ビット値として返します。

#### WOLFSSL_SMALL_STACK

スタックサイズが小さいデバイスに使用できます。これにより`wolfcrypt/src/integer.c`での動的メモリの使用が増えますが、パフォーマンスの低下につながる可能性があります。

#### WOLFSSL_PTHREADS

pthreadベースのミューテックスおよびスレッド実装を使用します。ほとんどのPOSIXシステムで自動検出されます。

#### WOLFSSL_MUTEX_INITIALIZER

動的な`pthread_mutex_init`の代わりに静的ミューテックス初期化（例: `PTHREAD_MUTEX_INITIALIZER`）を使用します。初期化オーバーヘッドの削減に役立ちます。

#### WC_MUTEX_OPS_INLINE

関数呼び出しの代わりにインライン化されたミューテックス操作を使用します。ミューテックス操作が頻繁に行われるプラットフォームでパフォーマンスを向上させることができます。

#### WOLFSSL_USE_RWLOCK

読み取りが多いワークロードにおける並行性を向上させるためのリーダー・ライターロックサポートを有効化します。

#### WOLFSSL_THREAD_NO_JOIN

join機能なしでスレッドを作成します（デタッチされたスレッド）。スレッドのjoinをサポートしていないプラットフォームで役立ちます。


#### WOLFSSL_THREADED_CRYPT

マルチコアシステムでのパフォーマンス向上のためにマルチスレッド暗号操作を有効化します。暗号化/復号を並列に実行できるようにします。

#### WOLFSSL_ALGO_HW_MUTEX

AES、ハッシュ、公開鍵、およびRNG操作に対するアルゴリズムごとのハードウェアミューテックスロックを有効化します。ハードウェア暗号エンジンがシリアル化されたアクセスを必要とする場合に役立ちます。

#### WOLFSSL_CRYPT_HW_MUTEX

ハードウェア暗号ミューテックス初期化のマスターコントロールです。有効化すると、`wolfSSL_CryptHwMutexInit`、`Lock`、`UnLock`関数が提供されます。

#### USE_WOLFSSL_MEMORY

カスタムメモリ割り当てフック（`wolfSSL_SetAllocators`）を有効化します。デフォルトでオンです。malloc/realloc/freeをカスタム実装で置き換えることができます。

#### WOLFSSL_TRACK_MEMORY

メモリ割り当ての追跡と統計を有効化します。wolfSSL/wolfCryptのメモリ使用量のプロファイリングに役立ちます。

#### WOLFSSL_TRACK_MEMORY_VERBOSE

割り当てごとの詳細を含む詳細なメモリ追跡出力を有効化します。[`WOLFSSL_TRACK_MEMORY`](#wolfssl_track_memory)を拡張します。

#### WOLFSSL_MEM_FAIL_COUNT

テストのためにmallocの失敗をカウントします。指定した回数の割り当て後に失敗させることで、エラー処理パスのテストが可能になります。

#### WOLFSSL_CHECK_MEM_ZERO

機密メモリ（鍵素材など）が解放時に適切にゼロ化されていることを検証します。鍵素材漏洩の可能性を検出するためのデバッグツールです。

#### WOLFSSL_GMTIME

標準Cライブラリの時刻関数を持たないプラットフォーム向けにカスタムgmtime実装を提供します。

#### STRING_USER

ユーザーがすべての文字列関数の実装を提供します。組み込みの文字列関数ラッパーを無効化します。

#### USE_WOLF_STRTOK

strtok_rを持たないプラットフォームでの移植性のために、wolfSSL組み込みのstrtok実装を使用します。

#### USE_WOLF_STRSEP

移植性のために、wolfSSL組み込みのstrsep実装を使用します。

#### USE_WOLF_STRLCPY

BSD strlcpyを持たないプラットフォームでの移植性のために、wolfSSL組み込みのstrlcpy実装を使用します。

#### USE_WOLF_STRLCAT

移植性のために、wolfSSL組み込みのstrlcat実装を使用します。

#### USE_WOLF_STRCASECMP

移植性のために、wolfSSL組み込みの大文字小文字を区別しない文字列比較を使用します。

#### USE_WOLF_STRNCASECMP

移植性のために、wolfSSL組み込みの長さ制限付きの大文字小文字を区別しない文字列比較を使用します。

#### USE_WOLF_STRDUP

移植性のために、wolfSSL組み込みのstrdup実装を使用します。

#### WOLFSSL_ATOMIC_OPS

完全なミューテックスを必要とせずに、スレッドセーフな参照カウントおよびその他の操作のためのアトミック操作を有効化します。


#### WOLFSSL_ATOMIC_INITIALIZER

スレッドセーフなクリーンアップ操作で使用されるアトミック変数用の静的イニシャライザを提供します。

#### WOLFSSL_USER_DEFINED_ATOMICS

ユーザー提供のアトミック操作実装です。プラットフォームがカスタムのアトミックプリミティブを必要とする場合にこれを定義してください。

#### WOLFSSL_LEANPSK

最小限の機能を持つLean PSK（Pre-Shared Key）ビルドです。必須ではない機能を無効化することでコードサイズを削減します。

#### WOLF_C89

C89互換モードを有効化します。コードベースが厳密なC89/ANSI Cコンパイラでコンパイルできることを保証します。

#### ALT_ECC_SIZE

fast mathとRSA/DHを使用している場合、これを定義することでECCのメモリ消費を削減できます。ECCポイントにスタックを使用する代わりに、ヒープから割り当てます。

#### ECC_SHAMIR

わずかに高速なECC数学のバリエーションを使用しますが、ヒープ使用量が2倍になります。

#### RSA_LOW_MEM

定義するとCRTが使用されなくなり、メモリをいくらか節約できますが、RSA操作は遅くなります。デフォルトではオフです。

#### WOLFSSL_SHA3_SMALL

SHA3が有効化されている場合、このマクロによりビルドサイズが削減されます。


#### WOLFSSL_SHUTDOWNONCE

`wolfSSL_shutdown()`が複数回呼び出された場合でも、`close_notify`アラートを1回だけ送信することを保証します。重複したシャットダウンメッセージを防ぎます。

#### WOLFSSL_SHAKE128

SHA-3に基づくSHAKE128拡張可能出力関数（XOF）を有効化します。可変長の出力を提供します。

#### WOLFSSL_SHAKE256

SHA-3に基づくSHAKE256拡張可能出力関数（XOF）を有効化します。可変長の出力を提供します。

#### SHA3_BY_SPEC

仕様順のKeccak-f置換を使用します。デフォルトでは、wolfSSLは最適化されたビットインターリーブ順を使用します。

#### WC_SHA3_NO_ASM

アセンブリ最適化されたSHA-3実装を無効化します。ポータブルなC実装の使用を強制します。

#### WC_SHA3_FAULT_HARDEN

冗長な計算を実行して一貫性を検証することで、SHA-3をフォールトインジェクション攻撃に対して堅牢化します。

#### WC_ASYNC_ENABLE_SHA3

wolfSSL非同期暗号フレームワークを介した非同期SHA-3操作を有効化します。

#### STM32_HASH_SHA3

STM32ハードウェアSHA-3アクセラレーションを有効化します。

#### PSOC6_HASH_SHA3

Cypress/Infineon PSoC6ハードウェアSHA-3アクセラレーションを有効化します。

#### WOLFSSL_SMALL_CERT_VERIFY

DecodedCertを使用せずに証明書署名を検証します。一部のコードが重複しますが、ピーク時のヒープメモリ使用量を小さくできます。[`WOLFSSL_NONBLOCK_OCSP`](#wolfssl_nonblock_ocsp)とは併用できません。

#### GCM_SMALL

テーブルを使用する代わりに実行時に計算することで、AES GCMのコードサイズを削減するオプションです。可能なオプションは`GCM_SMALL`、`GCM_WORD32`、`GCM_TABLE`です。

#### CURVED25519_SMALL

[`CURVE25519_SMALL`](#curve25519_small)と[`ED25519_SMALL`](#ed25519_small)を定義します。

#### CURVE25519_SMALL

curve25519に省メモリオプションを使用します。使用メモリは少なくなりますが、遅くなります。

#### ED25519_SMALL

ed25519に省メモリオプションを使用します。使用メモリは少なくなりますが、遅くなります。

#### USE_SLOW_SHA

ループを展開しないことでコードサイズを削減しますが、SHAのパフォーマンスは低下します。

#### WC_HASH_DATA_ALIGNMENT

ハッシュ入力に必要なデータアラインメントを指定します。一部のハードウェアバックエンドはアラインされた入力バッファを必要とします。

#### WC_ASYNC_ENABLE_SHA

wolfSSL非同期暗号フレームワークを介した非同期SHA-1操作を有効化します。

#### WOLFSSL_PIC32MZ_HASH

SHA-1およびSHA-256向けのMicrochip PIC32MZハードウェアハッシュアクセラレーションを有効化します。

#### WOLFSSL_TI_HASH

Texas Instrumentsハードウェアハッシュアクセラレーションを有効化します。

#### WOLFSSL_RENESAS_RX64_HASH

ルネサスRX64ハードウェアハッシュアクセラレーションを有効化します。

#### FREESCALE_LTC_SHA

Freescale/NXP LTC（Low Power Trusted Cryptography）SHAアクセラレーションを有効化します。

#### FREESCALE_MMCAU_SHA

Freescale/NXP MMCAU（Memory-Mapped Cryptographic Acceleration Unit）SHAアクセラレーションを有効化します。

#### PSOC6_HASH_SHA1

Cypress/Infineon PSoC6ハードウェアSHA-1アクセラレーションを有効化します。

#### USE_SLOW_SHA256

ループを展開しないことでコードサイズを削減しますが、SHAのパフォーマンスは低下します。約2k小さくなり、約25%遅くなります。

#### USE_SLOW_SHA512

ループを展開しないことでコードサイズを削減しますが、SHAのパフォーマンスは低下します。サイズは半分以下になりますが、50%遅くなります。

#### WOLFSSL_NOSHA512_224

SHA-512/224バリアントを無効化します。SHA-512/224が不要な場合にコードサイズを削減します。

#### WOLFSSL_NOSHA512_256

SHA-512/256バリアントを無効化します。SHA-512/256が不要な場合にコードサイズを削減します。

#### USE_SLOW_SHA2

すべてのSHA-2ファミリーアルゴリズムのループ展開を無効化します。パフォーマンスと引き換えにコードサイズを削減します。

#### WOLFSSL_HASH_FLAGS

ハッシュ状態（ハッシュがファイナライズされたかどうかなど）を追跡するためのハッシュフラグを有効化します。一部のハードウェアバックエンドで使用されます。

#### WOLFSSL_HASH_KEEP

再利用の可能性に備えてハッシュ入力データをメモリに保持します。データを再投入することなく再ハッシュが可能になります。

#### WC_ASYNC_ENABLE_SHA512

wolfSSL非同期暗号フレームワークを介した非同期SHA-512操作を有効化します。

#### WC_ASYNC_ENABLE_SHA384

wolfSSL非同期暗号フレームワークを介した非同期SHA-384操作を有効化します。

#### WOLFSSL_KCAPI_HASH

Linuxカーネル暗号API（AF_ALG）を通じたハッシュ操作を有効化します。SHAおよびその他のハッシュをカーネルにオフロードします。

#### WOLFSSL_SE050_HASH

NXP SE050セキュアエレメント上でのハッシュアクセラレーションを有効化します。

#### WOLFSSL_SILABS_SHA384

SHA-384向けのSilicon Labsハードウェアアクセラレーションを有効化します。

#### WOLFSSL_SILABS_SHA512

SHA-512向けのSilicon Labsハードウェアアクセラレーションを有効化します。

#### WOLFSSL_ARMASM_CRYPTO_SHA512

SHA-512アクセラレーション向けのARM暗号拡張命令を有効化します。

#### WOLFSSL_RENESAS_RSIP

ハッシュおよび暗号操作向けのルネサスRSIP（Renesas Security IP）ハードウェアアクセラレーションを有効化します。

#### ECC_USER_CURVES

有効化するECC曲線サイズをユーザーが選択できるようにします。デフォルトでは256ビット曲線のみが有効です。他を有効化するには`HAVE_ECC192`、`HAVE_ECC224`などを使用してください...

#### WOLFSSL_SP_NO_MALLOC

SPコードにおいて常にスタックを使用し、ヒープのXMALLOC()/XREALLOC()/XFREE()呼び出しを一切行いません。

#### WOLFSSL_SP_NO_DYN_STACK

動的スタックアイテムの使用を無効化します。小さいコードサイズと小さくないスタックとともに使用されます。

#### WOLFSSL_SP_FAST_MODEXP

コードサイズと引き換えに、より高速なmod_exp実装をコンパイルに含めます。

#### WC_DISABLE_RADIX_ZERO_PAD

16進数文字列出力における先頭のゼロの出力を無効化します。例えば、このマクロが定義されている場合、値8は文字列"0x8"として出力されますが、定義されていない場合は"0x08"として出力されます。このマクロを定義するとコードサイズを削減できます。

#### WC_ASN_NAME_MAX

X.509証明書フィールドの最大名前サポートを上書きできるようにします。

#### OPENSSL_EXTRA_X509_SMALL

証明書向けの特別に小さいOpenSSL互換レイヤーです。

#### OPENSSL_EXTRA_NO_ASN1

OpenSSL extra互換APIを有効化しますが、ASN1オブジェクト関数を除外します。コードサイズ削減のためにASN1互換性が不要な場合に役立ちます。

### パフォーマンスの向上

#### USE_INTEL_SPEEDUP

AES、ChaCha20、Poly1305、SHA256、SHA512、ED25519、Curve25519を高速化するためのIntelのAVX/AVX2命令の使用を有効化します。

#### WOLFSSL_AESNI

一部のIntelおよびAMDチップセットに組み込まれているAES高速化操作の使用を有効化します。この定義を使用する場合、Intel AES新命令セット（AESNI）による最適化のために`aes_asm.asm`（at&t構文のWindows用）または`aes_asm.S`ファイルが使用されます。

#### WOLFSSL_AESNI_BY4

4ブロック並列AES-NI処理を有効化します。スループット向上のために、AES-NIパイプライニングを使用して4つのAESブロックを同時に処理します。[`WOLFSSL_AESNI`](#wolfssl_aesni)が必要です。

#### WOLFSSL_AESNI_BY6

6ブロック並列AES-NI処理を有効化します。最大スループットのために、AES-NIパイプライニングを使用して6つのAESブロックを同時に処理します。[`WOLFSSL_AESNI`](#wolfssl_aesni)が必要です。

#### WOLFSSL_AES_SMALL_TABLES

より小さいAES S-boxルックアップテーブルを使用します。AES操作がわずかに遅くなる代わりに、コード/データサイズを削減します。メモリ制約のある組み込みターゲットに役立ちます。

#### WOLFSSL_AES_NO_UNROLL

AESラウンド関数におけるループ展開を無効化します。パフォーマンスと引き換えにコードサイズを削減します。速度よりもコードサイズが重要な制約のある環境で役立ちます。

#### WOLFSSL_AES_TOUCH_LINES

サイドチャネル耐性を提供するために、ルックアップ前にすべてのAESテーブルのキャッシュラインにタッチします。使用前にすべてのテーブルエントリがキャッシュに入っていることを保証することで、キャッシュタイミング攻撃を緩和します。

#### WC_AES_BITSLICED

ビットスライスAES実装を有効化します。複数のブロックを同時に処理するビット並列手法を使用し、サイドチャネル耐性のための定数時間実行を提供します。

#### AES_GCM_GMULT_NCT

非定数時間のGCM GMULT実装を有効化します。高速ですが、キャッシュタイミングサイドチャネル攻撃に対する保護がありません。サイドチャネル耐性が不要な場合にのみ使用してください。

#### NO_WOLFSSL_ALLOC_ALIGN

AESコンテキストのアラインされたメモリ割り当てを無効化します。デフォルトでは、パフォーマンスのためにAESコンテキストはキャッシュライン境界にアラインされます。アラインされた割り当てをサポートしていないプラットフォームでは無効化してください。

#### WC_ASYNC_ENABLE_AES

非同期AES操作を有効化します。wolfSSL非同期暗号フレームワークを使用して、AES暗号化/復号をハードウェアアクセラレータにオフロードできるようにします。

#### WOLFSSL_CRYPTOCELL_AES

ARM CryptoCellハードウェアを使用したAESアクセラレーションを有効化します。CryptoCell SDKと[`WOLFSSL_CRYPTOCELL`](#wolfssl_cryptocell)が必要です。

#### WOLFSSL_DEVCRYPTO_AES

Linuxの`/dev/crypto`インターフェースを介したAESアクセラレーションを有効化します。[`WOLFSSL_DEVCRYPTO`](#wolfssl_devcrypto)が必要です。

#### WOLFSSL_DEVCRYPTO_CBC

Linuxの`/dev/crypto`インターフェースを介したAES-CBCアクセラレーションを有効化します。[`WOLFSSL_DEVCRYPTO`](#wolfssl_devcrypto)が必要です。

#### WOLFSSL_KCAPI_AES

Linuxカーネル暗号API（AF_ALG）を通じたAES操作を有効化します。AESをカーネルの暗号サブシステムにオフロードします。

#### WOLFSSL_NO_KCAPI_AES_CBC

[`WOLFSSL_KCAPI_AES`](#wolfssl_kcapi_aes)が有効な場合に、KCAPIを通じたAES-CBCを無効化します。カーネル暗号APIを通じてCBC以外のAESモードのみが必要な場合に役立ちます。

#### WOLFSSL_PSA_NO_AES

Platform Security Architecture (PSA)暗号APIを通じたAESを無効化します。PSAが有効化されているがAESには代わりにソフトウェア実装を使用したい場合に使用します。

#### WOLFSSL_SCE_NO_AES

ルネサスSecure Crypto Engine (SCE)を通じたAESを無効化します。SCEが有効化されているがAESにはソフトウェア実装を使用したい場合に使用します。

#### NO_IMX6_CAAM_AES

NXP i.MX6 CAAM（Cryptographic Acceleration and Assurance Module）上でのAESアクセラレーションを無効化します。CAAMが有効化されているがAESにはソフトウェア実装を使用したい場合に使用します。

#### WOLFSSL_AFALG_XILINX_AES

Xilinxプラットフォーム上でAF_ALGを通じたAESアクセラレーションを有効化します。Linux AF_ALGインターフェースを介してXilinx暗号ハードウェアを使用します。

#### NO_WOLFSSL_ESP32_CRYPT_AES

ESP32ハードウェアAESアクセラレーションを無効化します。ESP32向けにビルドするがAESにはソフトウェア実装を使用したい場合に使用します。

#### STM32_CRYPTO_AES_ONLY

STM32ハードウェア暗号をAES操作のみに制限します。STM32暗号ハードウェアが利用可能であっても、他のアルゴリズムはソフトウェア実装を使用します。

#### WC_DEBUG_CIPHER_LIFECYCLE

AES暗号コンテキストのライフサイクルイベント（init、set key、free）のデバッグログを有効化します。AESコンテキストのリソースリークや二重解放の問題のデバッグに役立ちます。

#### WOLFSSL_HW_METRICS

ハードウェアアクセラレーション使用状況メトリクスの追跡を有効化します。有効化すると、wolfSSLはハードウェアにオフロードされた操作とソフトウェアで処理された操作の数をカウントし、`wolfCrypt_GetHwMetrics()`でアクセスできます。

#### HAVE_INTEL_RDSEED

DRBGシードソースとしてIntelのRDSEEDを有効化します。

#### HAVE_INTEL_RDRAND

wolfSSLのランダムソースとしてIntelのRDRAND命令を有効化します。

#### FP_ECC

ECC Fixed Point Cacheを有効化します。これにより、同じ秘密鍵に対する繰り返しの操作が高速化されます。`FP_ENTRIES`と`FP_LUT`を使用してエントリ数とLUTビット数を定義し、デフォルトの静的メモリ使用量を削減することもできます。

#### FP_ENTRIES

ECC固定点乗算ルックアップテーブルのキャッシュエントリ数（デフォルト15）を定義します。[`FP_ECC`](#fp_ecc)が必要です。メモリ使用量とパフォーマンスのバランスを取るために調整してください。

#### FP_LUT

ECC固定点事前計算のルックアップテーブルビットサイズ（2〜12、デフォルト8）を設定します。値を大きくするとメモリ使用量が増えますが、検証は高速になります。[`FP_ECC`](#fp_ecc)が必要です。

#### FP_ECC_CONTROL

[`WOLFSSL_HAVE_SP_ECC`](#wolfssl_have_sp_ecc)が利用可能な場合、SP関数を使用したキャッシュ付き固定点ECC検証を自動選択します。適用可能な場合はデフォルトで有効です。

#### HAVE_ECC_CHECK_PUBKEY_ORDER

無効な鍵を検出するために、インポート時のECC公開鍵の位数検証を有効化します。[`NO_ECC_CHECK_PUBKEY_ORDER`](#no_ecc_check_pubkey_order)が定義されている場合、またはハードウェアアクセラレータが使用されている場合を除き、自動的に有効化されます。

#### HAVE_ECC_MAKE_PUB

秘密鍵から公開鍵を計算する`wc_ecc_make_pub`関数を有効化します。デフォルトで有効です。

#### HAVE_ECC_VERIFY_HELPER

ECC署名検証ヘルパー関数を有効化します。ハードウェアアクセラレータが使用されている場合を除き、自動的に有効化されます。

#### NO_ECC_CHECK_PUBKEY_ORDER

鍵インポート時のECC公開鍵の位数検証チェックを無効化します。重要なセキュリティ検証をスキップするため、本番環境での使用は推奨されません。

#### WC_NO_CACHE_RESISTANT

オーバーヘッド削減のために、ECCスカラー乗算におけるキャッシュ耐性のある操作（条件付きスワップ）を無効化します。キャッシュベースのサイドチャネル攻撃に操作をさらす可能性があるため、推奨されません。

#### WOLFSSL_ECC_NO_SMALL_STACK

ECC操作に対する`WOLFSSL_SMALL_STACK`最適化を無効化し、ヒープの代わりにスタック割り当てを強制します。スタック領域が十分にあり、ヒープ割り当てのオーバーヘッドが望ましくない場合に役立ちます。

#### WOLFSSL_PUBLIC_ECC_ADD_DBL

`ecc_projective_add_point`と`ecc_projective_dbl_point`を内部専用関数ではなくパブリックAPIにします。ECC点演算への直接アクセスを必要とするアプリケーションに役立ちます。


#### WOLFSSL_PYTHON

Python wolfSSLモジュールの互換性に必要なAPIと動作を有効化します。

#### SQRTMOD_USE_MOD_EXP

圧縮鍵の展開において、ヤコビ記号法の代わりにモジュラー冪乗を使用して素数を法とする平方根を計算します。デフォルトではオフです。

#### WOLFSSL_ECIES_OLD

公開鍵が共有秘密素材に含まれない、元のwolfSSL ECIES形式を使用します。デフォルトではオフです。


#### WOLFSSL_ECDSA_MATCH_HASH

ECDSA署名のハッシュアルゴリズムが曲線の推奨ハッシュと一致することを要求します（例: P-256はSHA-256を、P-384はSHA-384を使用）。

#### WOLFSSL_ECIES_ISO18033

共有秘密の導出に公開鍵を含めるISO 18033 ECIES標準を使用します。デフォルトではオフです。

#### WOLFSSL_ECIES_GEN_IV

KDF出力から導出する代わりに、ECIES暗号化のためにランダムなIVを生成します。デフォルトではオフです。

#### WOLFSSL_SP_521

P-521 ECC曲線向けの単精度（SP）数学最適化実装を有効化します。デフォルトではオフです。[`WOLFSSL_SP_MATH`](#wolfssl_sp_math)または[`WOLFSSL_SP_MATH_ALL`](#wolfssl_sp_math_all)が設定され、`HAVE_ECC521`が定義されている場合に自動的に有効化されます。

#### WOLFSSL_SP_SM2

SM2曲線（中国の暗号標準）向けの単精度（SP）数学最適化実装を有効化します。[`WOLFSSL_SM2`](#wolfssl_sm2)が設定されている場合に自動的に有効化されます。

#### WOLF_CRYPTO_CB_ONLY_ECC

ECC操作を暗号コールバックのみの使用に制限し、すべてのソフトウェアECC実装を無効化します。すべてのECC操作をハードウェアまたは外部モジュールに委譲すべき場合に役立ちます。デフォルトではオフです。

#### WC_ASYNC_ENABLE_ECC

暗号コールバックを使用した非同期（ノンブロッキング）ECC操作を有効化します。[`WOLFSSL_ASYNC_CRYPT`](#wolfssl_async_crypt)が必要です。デフォルトではオフです。

#### WC_ASYNC_ENABLE_ECC_KEYGEN

非同期ECC鍵生成を有効化し、鍵生成をハードウェアアクセラレータにオフロードできるようにします。[`WOLFSSL_ASYNC_CRYPT`](#wolfssl_async_crypt)が必要です。デフォルトではオフです。

#### PLUTON_CRYPTO_ECC

ECC操作にARM Pluton信頼実行環境の使用を有効化します。デフォルトではオフです。

#### WOLFSSL_CAAM_BLACK_KEY_SM

ECC操作において、暗号化されたブラックキーの保存にNXP CAAMセキュアメモリを使用します。デフォルトではオフです。

#### WOLFSSL_KCAPI_ECC

ハードウェアアクセラレーションのために、ECC操作をLinux Kernel Crypto API（kcAPI）にオフロードします。デフォルトではオフです。

#### WOLFSSL_ASYNC_CRYPT

Intel QuickAssistやMarvell (Cavium) Nitrox Vなどのハードウェアベースのアダプタを使用した非同期暗号のサポートを有効化します。非同期コードは公開ディストリビューションには含まれておらず、[info@wolfssl.jp](mailto:info@wolfssl.jp)宛にメールでお問い合わせいただくことで評価版を入手できます。

#### WOLFSSL_NO_ASYNC_IO

非同期I/Oネットワーキングを無効化します。非同期I/Oはデフォルトでオンであり、ハンドシェイク処理中に最大約140バイトを消費する可能性があります。ネットワークインターフェースが書き込み時に`SOCKET_EWOULDBLOCK`または`SOCKET_EAGAIN`（カスタムI/Oコールバックの場合は`WOLFSSL_CBIO_ERR_WANT_WRITE`）を返さない場合、`WOLFSSL_NO_ASYNC_IO`を定義することで、wolfSSLがハンドシェイクメッセージの構築中に状態を保存しないようにできます。

### GCMパフォーマンスチューニング

GCMのパフォーマンスには4つのバリアントがあります:

* `GCM_SMALL` - 最小フットプリント、最も低速（FIPS検証済み）
* `GCM_WORD32` - 中程度（FIPS検証済み）
* `GCM_TABLE` - 高速（FIPS検証済み）
* `GCM_TABLE_4BIT` - 最速（FIPS検証済み）

### wolfSSLの数学オプション

wolfSSLには3つの数学ライブラリがあります。

* Big Integer
* Fast Math
* Single Precision Math

wolfSSLをビルドする際は、これらのうちいずれか1つのみを使用する必要があります。

Big Integerライブラリは、アセンブリを一切使用せずCで書かれているため、最も移植性の高いオプションです。そのため、特定のアーキテクチャ向けには最適化されていません。すべての数学変数はヒープ上に生成され、スタック使用量は最小限です。残念ながら、Big Integerライブラリはタイミング耐性がありません。

Fast Mathライブラリは良い選択肢です。Cとアセンブリの両方を使用して実装されています。そのため、特定のアーキテクチャ向けの最適化があります。すべての数学変数はスタック上に生成されるため、ヒープ使用量が少なくなります。`TFM_TIMING_RESISTANT`マクロを定義すればタイミング耐性を持たせることができます。当社はFIPS 140-2および140-3認証を取得しています。

Single Precision (SP) Mathライブラリは、当社が推奨するライブラリです。Cとアセンブリの両方を使用して実装されています。そのため、特定のアーキテクチャ向けの最適化があります。すべての数学変数はスタック上に生成されるため、ヒープ使用量が少なくなります。常にタイミング耐性があります。一般的にコードサイズと引き換えに速度が最適化されていますが、不要なコードをコンパイル対象から除外できるよう高度に設定可能です。当社はDO-178C認証を取得しています。

#### Big Integer数学ライブラリ（廃止予定）

このライブラリは2023年末までにwolfSSL/wolfCryptライブラリから廃止・削除される予定です。必要であれば、`--enable-heapmath`または`CFLAGS=-DUSE_INTEGER_HEAP_MATH`で有効化できます。

パブリックドメインのLibTomMathライブラリからフォークされています。LibTomMathの詳細については、<https://www.libtom.net/LibTomMath/>を参照してください。当社のフォークは、元のパブリックドメインコードよりもはるかに活発に開発され、安全性が高いことにご留意ください。

このライブラリは一般的に最も移植性が高く、最も簡単に使い始めることができます。通常のbig integerライブラリの欠点は、遅いこと、すべてのメモリがヒープから割り当てられるためヒープメモリを大量に使用すること、`XREALLOC()`の実装を必要とすること、そしてタイミング耐性がないことです。実装は`integer.c`にあります。

#### Fast Math

##### USE_FAST_MATH

パブリックドメインのLibTomFastMathライブラリからフォークされています。LibTomFastMathの詳細については、<https://www.libtom.net/TomsFastMath>を参照してください。当社のフォークは、LibTomFastMathの元のパブリックドメインコードよりもはるかに活発に開発され、安全性が高いことにご留意ください。当社はパフォーマンス、セキュリティ、コード品質を改善しました。また、FastMathコードはFIPS 140-2および140-3認証を取得しています。

FastMathライブラリは可能であればアセンブリを使用し、RSA、DH、DSAなどの非対称秘密鍵/公開鍵操作を高速化します。アセンブリの組み込みは、コンパイラとプロセッサの組み合わせに依存します。組み合わせによっては追加のconfigureフラグが必要な場合や、不可能な場合があります。新しいアセンブリルーチンによるFastMathの最適化支援は、コンサルティングベースで提供しています。「アーキテクチャ固有の最適化」を参照してください。

FastMathでは、すべてのメモリがスタック上に割り当てられます。FastMath使用時はスタックメモリ使用量が大きくなる可能性があるため、このオプションを使用する場合は[`TFM_TIMING_RESISTANT`](#tfm_timing_resistant)も併せて定義することを推奨します。TFM_TIMING_RESISTANTが定義されている場合、FastMathコードはタイミング耐性を持ちます。これにより、定数時間のために大きな数学ウィンドウの一部が縮小され、使用メモリが少なくなります。ショートカットがないため秘密鍵操作中の分岐が少なくなり、スタックの使用も少なくなります。タイミング攻撃は現実的な脅威であり、悪意のある第三者に秘密鍵を再現するのに十分な情報を与える可能性があるため、これにより実装はより安全にもなります。

例えばia32では、すべてのレジスタが利用可能である必要があるため、高い最適化とフレームポインタの省略に注意する必要があります。wolfSSLは非デバッグビルドの場合、GCCに`-O3 -fomit-frame-pointer`を追加します。別のコンパイラを使用している場合は、configure時に`CFLAGS`へこれらを手動で追加する必要があるかもしれません。

OS Xでは、`CFLAGS`に`-mdynamic-no-pic`も追加する必要があります。さらに、OS X上でia32向けに共有モードでビルドする場合は、`LDFLAGS`にもオプションを渡す必要があります:

```sh
LDFLAGS="-Wl,-read_only_relocs,warning"
```

これにより、一部のシンボルに対してエラーの代わりに警告が出るようになります。

FastMathは、動的メモリとスタックメモリの使用方法も変更します。通常の数学ライブラリはbig integerに動的メモリを使用します。FastMathはデフォルトで4096ビット整数を保持する固定サイズバッファを使用し、2048ビット×2048ビットの乗算が可能です。4096ビット×4096ビットの乗算が必要な場合は、`wolfssl/wolfcrypt/tfm.h`の`FP_MAX_BITS`を変更してください。`FP_MAX_BITS`を増やすと、公開鍵操作で使用されるバッファが大きくなるため、実行時のスタック使用量も増加します。`FP_MAX_BITS`は最大鍵サイズの2倍にする必要があります。例えば、最大の鍵が2048ビットの場合、`FP_MAX_BITS`は4096にすべきで、4096ビットの場合は`FP_MAX_BITS`を8192にすべきです。ECCのみを使用する場合は、最大ECC鍵サイズの2倍まで減らすことができます。ライブラリ内のいくつかの関数は複数の一時的なbig integerを使用するため、スタックが比較的大きくなる可能性があります。これは、組み込みシステムや、スタックサイズが低い値に設定されているスレッド環境でのみ問題になるはずです。そのような環境で公開鍵操作中にFastMathによるスタック破壊が発生した場合は、スタック使用量に対応できるようスタックサイズを増やしてください。

autoconfシステムを使用せずにFastMathを有効化する場合は、`USE_FAST_MATH`を定義し、wolfSSLビルドに`tfm.c`を追加して`integer.c`を削除する必要があります。`ALT_ECC_SIZE`を定義すると、ECCポイントはスタックではなくヒープからのみ割り当てられます。

##### アーキテクチャ固有の最適化

USE_FAST_MATH使用時のアセンブリ最適化のために、以下のマクロを定義できます。

* `TFM_ARM`
* `TFM_SSE2`
* `TFM_AVR32`
* `TFM_PPC32`
* `TFM_PPC64`
* `TFM_MIPS`
* `TFM_X86`
* `TFM_X86_64`

これらのいずれも定義されていない場合、またはTFM_NO_ASMが定義されている場合、`TFM_ISO`が定義され、ISO Cのポータブルコードが使用されます。

##### アルゴリズム固有の最適化

有効化すると、対応するECC曲線に対して、乗算と二乗の最適化された実装が使用されます。

* `TFM_ECC192`
* `TFM_ECC224`
* `TFM_ECC256`
* `TFM_ECC384`
* `TFM_ECC521`

##### TFM_SMALL_SET

小さい数の乗算の速度最適化です。1〜16ワードのComba乗算と二乗の実装を含みます。ECC操作のパフォーマンス向上に役立ちます。

##### TFM_HUGE_SET

大きい数の乗算の速度最適化です。ビットサイズが許す場合、20、24、28、32、48、64ワードのComba乗算と二乗の実装を含みます。RSA/DH/DSA操作のパフォーマンス向上に役立ちます。

##### TFM_SMALL_MONT_SET

Intelアーキテクチャ上での小さい数のモンゴメリリダクションの速度最適化です。1〜16ワードのモンゴメリリダクションの実装を含みます。ECC操作のパフォーマンス向上に役立ちます。

#### 独自のSingle Precision（SP）数学サポート

SP数学は当社が推奨するデフォルトオプションであり、DO-178C認証を取得しています。一般的な特定の鍵サイズおよび曲線に対する公開鍵演算を高速化するために使用してください。以下のような正しいコードファイルを含めるようにしてください:

* `sp_c32.c`
* `sp_c64.c`
* `sp_arm32.c`
* `sp_arm64.c`
* `sp_armthumb.c`
* `sp_cortexm.c`
* `sp_int.c`
* `sp_x86_64.c`
* `sp_x86_64_asm.S`
* `sp_x86_64_asm.asm`

##### WOLFSSL_SP

Single Precision数学サポートを有効化します。

#### WOLFSSL_SP_MATH

SP数学とアルゴリズムのみを有効化します。通常（`integer.c`）や高速（`tfm.c`）などの多倍長整数数学コードを除去します。鍵サイズと曲線をSPがサポートするもののみに制限します。

#### WOLFSSL_SP_MATH_ALL

SP数学とアルゴリズムを有効化します。SPがサポートしていない鍵サイズと曲線に対しては、通常（`integer.c`）や高速（`tfm.c`）などの多倍長整数数学コードを実装します。

#### WOLFSSL_SP_SMALL

SP数学を使用している場合、これによりコードの小型版が使用され、大きなスタック変数を回避します。

##### SP_WORD_SIZE

数値の1ワードを格納するデータ型を32ビットまたは64ビットに強制します。

##### WOLFSSL_SP_NONBLOCK

Single Precision数学の「非ブロッキング」モードを有効化します。時間のかかる演算に対してFP_WOULDBLOCKを返し、完了するまで関数を再度呼び出す必要があります。現在、これはECCでのみサポートされており、`WC_ECC_NONBLOCK`と組み合わせて使用します。

##### WOLFSSL_SP_FAST_NCT_EXPTMOD

より高速な非定数時間のモジュラーべき乗実装を有効化します。公開鍵演算にのみ使用され、秘密鍵演算には使用されません。

##### WOLFSSL_SP_INT_NEGATIVE

多倍精度数が負の値を取ることを許可します。（暗号演算には不要です。）

##### WOLFSSL_SP_INT_DIGIT_ALIGN

sp_int_digitポインタの非アライメントアクセスが許可されない場合に有効化します。

##### WOLFSSL_HAVE_SP_RSA

2048、3072、4096ビットのSingle Precision RSAです。

##### WOLFSSL_HAVE_SP_DH

2048、3072、4096ビットのSingle Precision DHです。

##### WOLFSSL_HAVE_SP_ECC

SECP256R1およびSECP384R1のSingle Precision ECCです。

##### WOLFSSL_SP_LARGE_CODE

バイナリサイズの増加と引き換えにSingle-Precision（SP）の高速化を許可します。一部の組み込みプラットフォームには適さない場合があります。

##### WOLFSSL_SP_DIV_WORD_HALF

倍長ワードを使用した除算が利用できないことを示します。例えば32ビットCPUで、ライブラリからの64ビット除算をコンパイルに含めたくない場合、このマクロを定義すると、半ワードサイズの部分を使用して除算を行う実装が有効になります。

##### WOLFSSL_SP_DIV_32

32ビット除算が利用できず、wolfSSLが独自のSingle-Precision（SP）実装を使用すべきであることを示します。

##### WOLFSSL_SP_DIV_64

64ビット除算が利用できず、wolfSSLが独自のSingle-Precision（SP）実装を使用すべきであることを示します。

##### WOLFSSL_SP_ASM

より高速なSingle-Precision（SP）プラットフォーム固有のアセンブリコード実装を有効化します。プラットフォームは自動検出されます。

##### WOLFSSL_SP_X86_64_ASM

Single-Precision（SP）Intel x64アセンブリ実装を有効化します。

##### WOLFSSL_SP_ARM32_ASM

Single-Precision（SP）Aarch32アセンブリ実装を有効化します。

##### WOLFSSL_SP_ARM64_ASM

Single-Precision（SP）Aarch64アセンブリ実装を有効化します。

##### WOLFSSL_SP_ARM_CORTEX_M_ASM

Single-Precision（SP）Cortex-Mファミリ（Cortex-M4を含む）アセンブリ実装を有効化します。

##### WOLFSSL_SP_ARM_THUMB_ASM

Single-Precision（SP）ARM Thumbアセンブリ実装を有効化します（-mthumbとともに使用）。

##### WOLFSSL_SP_X86_64

Single-Precision（SP）Intel x86 64ビットアセンブリ高速化マクロを有効化します。`WOLFSSL_SP_MATH_ALL`が定義されている場合にのみ適用されます。`sp_int.c`を参照してください。

##### WOLFSSL_SP_X86

Single-Precision（SP）Intel x86アセンブリ高速化マクロを有効化します。`WOLFSSL_SP_MATH_ALL`が定義されている場合にのみ適用されます。`sp_int.c`を参照してください。

##### WOLFSSL_SP_PPC64

Single-Precision（SP）PPC64アセンブリ高速化マクロを有効化します。`WOLFSSL_SP_MATH_ALL`が定義されている場合にのみ適用されます。`sp_int.c`を参照してください。

##### WOLFSSL_SP_PPC

Single-Precision（SP）PPCアセンブリ高速化マクロを有効化します。`WOLFSSL_SP_MATH_ALL`が定義されている場合にのみ適用されます。`sp_int.c`を参照してください。

##### WOLFSSL_SP_MIPS64

Single-Precision（SP）MIPS64アセンブリ高速化マクロを有効化します。`WOLFSSL_SP_MATH_ALL`が定義されている場合にのみ適用されます。`sp_int.c`を参照してください。

##### WOLFSSL_SP_MIPS

Single-Precision（SP）MIPSアセンブリ高速化マクロを有効化します。`WOLFSSL_SP_MATH_ALL`が定義されている場合にのみ適用されます。`sp_int.c`を参照してください。

##### WOLFSSL_SP_RISCV64

Single-Precision（SP）RISCV64アセンブリ高速化マクロを有効化します。`WOLFSSL_SP_MATH_ALL`が定義されている場合にのみ適用されます。`sp_int.c`を参照してください。

##### WOLFSSL_SP_RISCV32

Single-Precision（SP）RISCV32アセンブリ高速化マクロを有効化します。`WOLFSSL_SP_MATH_ALL`が定義されている場合にのみ適用されます。`sp_int.c`を参照してください。

##### WOLFSSL_SP_S390X

Single-Precision（SP）S390Xアセンブリ高速化マクロを有効化します。`WOLFSSL_SP_MATH_ALL`が定義されている場合にのみ適用されます。`sp_int.c`を参照してください。

#### SP_INT_BITS

sp_intが保持するビット数です。ライブラリが扱える最大の多倍長整数を決定します。


### スタックまたはチップ固有の定義

wolfSSLはさまざまなプラットフォームおよびTCP/IPスタック向けにビルドできます。以下の定義のほとんどは`./wolfssl/wolfcrypt/settings.h`にあり、デフォルトではコメントアウトされています。それぞれのコメントを解除することで、以下に示す特定のチップまたはスタックのサポートを有効化できます。

#### IPHONE

iOSで使用するためにビルドする場合に定義できます。

#### THREADX

ThreadX RTOS（<https://www.rtos.com>）で使用するためにビルドする場合に定義できます。

#### MICRIUM

MicriumのµC/OS-III RTOS（<https://www.micrium.com>）のサポートを有効化してwolfSSLをビルドする場合に定義できます。

#### MBED

mbedプロトタイピングプラットフォーム（<https://www.mbed.org>）向けにビルドする場合に定義できます。

#### MICROCHIP_PIC32

MicrochipのPIC32プラットフォーム（<https://www.microchip.com>）向けにビルドする場合に定義できます。

#### MICROCHIP_TCPIP_V5

microchip tcp/ipスタックのバージョン5専用に定義できます。

#### MICROCHIP_TCPIP

microchip tcp/ipスタックのバージョン6以降に対して定義できます。

#### WOLFSSL_MICROCHIP_PIC32MZ

PIC32MZハードウェア暗号エンジンに対して定義できます。

#### FREERTOS

FreeRTOS（<https://www.freertos.org>）向けにビルドする場合に定義できます。LwIPを使用する場合は、[`WOLFSSL_LWIP`](#wolfssl_lwip)も併せて定義してください。

#### FREERTOS_WINSIM

FreeRTOS Windowsシミュレータ（<https://www.freertos.org>）向けにビルドする場合に定義できます。

#### WOLFSSL_CHIBIOS

ChibiOS RTOS向けにビルドする場合に定義できます。


#### WOLFSSL_CLEANUP_THREADSAFE_BY_ATOMIC_OPS

アトミック操作を使用して、ミューテックスを必要とせずに`wolfSSL_Cleanup()`をスレッドセーフにします。

#### WOLFSSL_CMSIS_RTOS

Mbed CMIS-RTOS向けにビルドする場合に定義できます。

#### WOLFSSL_CMSIS_RTOSv2

Mbed CMIS-RTOSv2向けにビルドする場合に定義できます。

#### WOLFSSL_LWIP_NATIVE

LWIPネイティブのプラットフォームで使用します。

#### WOLFSSL_DEOS

この定義を使用すると、[こちら](https://www.ddci.com/products_deos_do_178c_arinc_653/)で入手可能なDeos RTOSに対するwolfSSLのサポートを有効化できます。

#### WOLFSSL_ESPIDF

ESP-IDF向けにビルドする場合に定義できます。

#### WOLFSSL_LINUXKM

Linuxカーネルモジュール向けにビルドする場合に使用します。

#### WORD64_AVAILABLE

64ビット型がサポートされていることを示す移植性マクロです。通常は`SIZEOF_LONG_LONG` 8を使用する方が適切です。

#### WOLFSSL_NUCLEUS_1_2

Nucleus 1.2向けにビルドする場合に使用します。

#### WOLFSSL_PICOTCP

PicoTCPでビルドする場合に使用します。

#### WOLFSSL_RENESAS_RA6M3G

RENESAS RA6M3Gでビルドする場合に使用します。

#### WOLFSSL_RENESAS_RA6M4

RENESAS RA6M4でビルドする場合に使用します。

#### WOLFSSL_RIOT_OS

RIOT-OSでビルドする場合に使用します。

#### WOLFSSL_uITRON4

uITRON4向けにビルドする場合に使用します。

#### WOLFSSL_uTKERNEL2

uT-Kernelでビルドする場合に使用します。

#### WOLFSSL_VXWORKS

VxWorksでビルドする場合に使用します。

#### DEVKITPRO

devkitPro向けにビルドする場合に使用します。

#### WOLFSSL_VXWORKS_6_x

VxWorks 6.x専用の実装でのみ使用されます。


#### WOLFSSL_VERIFY_CB_ALL_CERTS

ピア証明書だけでなく、チェーン内のすべての証明書に対して証明書検証コールバックを呼び出します。

#### WOLFSSL_WICED

WICED Studio向けにビルドする場合に使用します。

#### FREESCALE_KSDK_FREERTOS

これの古い名称は`FREESCALE_FREE_RTOS`ですが、Freescale KSDK FreeRTOS向けにビルドする場合に使用します。

#### FREESCALE_KSDK_MQX

Freescale KSDK MQX/RTCS/MFS向けにビルドする場合に使用します。

#### FREESCALE_MQX_5_0

Freescale Classic MQXバージョン5.0向けにビルドする場合に使用します。

#### WOLFSSL_KEIL_TCP_NET

TCPスタック（`MDK_CONF_NETWORK`）を設定します。デフォルトではKeil TCP `WOLFSSL_KEIL_TCP_NET`を使用します。なしの場合は0、ユーザーIOコールバックの場合は2を使用します。

#### INTEL_GALILEO

ARDUINOとwolfSSLを設定する際に使用します。Intel Galileoプラットフォーム向けにビルドする場合は次を追加してください: `#define INTEL_GALILEO`

#### HAVE_KEIL_RTX

MDK-RTX-TCP-FS向けのwolfSSL設定です。

#### EBSNET

EBSnet製品およびRTIPを使用する場合に定義できます。

#### WOLFSSL_EMBOS

SEGGER embOS（<https://www.segger.com/products/rtos/embos/>）向けにビルドする場合に定義できます。emNETを使用する場合は、[`WOLFSSL_EMNET`](#wolfssl_emnet)も併せて定義してください。

#### WOLFSSL_EMNET

SEGGER emNET TCP/IPスタック（<https://www.segger.com/products/connectivity/emnet/>）向けにビルドする場合に定義できます。

#### WOLFSSL_LWIP

LwIP TCP/IPスタック（<https://savannah.nongnu.org/projects/lwip/>）とともにwolfSSLを使用する場合に定義できます。

#### WOLFSSL_ISOTP

主にCANバスで使用されるISO-TPトランスポートプロトコルとともにwolfSSLを使用する場合に定義できます。使用例は[wolfssl-examplesリポジトリ](https://github.com/wolfssl/wolfssl-examples)にあります。


#### WOLFSSL_IOTSAFE

セキュアエレメント操作のためのIoTSAFE（GSMA）アプレットサポートを有効化します。TLSの鍵と暗号処理をIoTSAFE準拠のSIMで扱えるようになります。

#### WOLFSSL_GAME_BUILD

ゲームコンソール向けにwolfSSLをビルドする場合に定義できます。

#### WOLFSSL_LSR

LSR向けにビルドする場合に定義できます。


#### WOLFSSL_LOCAL_X509_STORE

グローバルなX509証明書ストアの代わりに、SSLコンテキストごとにローカルなX509証明書ストアを使用します。コンテキスト間の分離性が向上します。

#### FREESCALE_MQX

Freescale MQX/RTCS/MFS（<https://www.freescale.com>）向けにビルドする場合に定義できます。これにより`FREESCALE_K70_RNGA`が定義され、Kinetis H/W乱数生成アクセラレータのサポートが有効化されます。

#### WOLFSSL_STM32F2

STM32F2向けにビルドする場合に定義できます。この定義により、wolfSSLでのSTM32F2ハードウェア暗号およびハードウェアRNGサポートも有効化されます（<https://www.st.com/internet/mcu/subclass/1520.jsp>）。

#### COMVERGE

Comverge設定を使用する場合に定義できます。

#### WOLFSSL_QL

QL SEP設定を使用する場合に定義できます。

#### WOLFSSL_EROAD

EROAD向けにビルドする場合に定義できます。

#### WOLFSSL_IAR_ARM

IAR EWARM向けにビルドする場合に定義できます。

#### WOLFSSL_TIRTOS

TI-RTOS向けにビルドする場合に定義できます。

#### WOLFSSL_ROWLEY_ARM

Rowley CrossWorksでビルドする場合に定義できます。

#### WOLFSSL_NRF51

Nordic nRF51へ移植する場合に定義できます。

#### WOLFSSL_NRF51_AES

Nordic nRF51へ移植する際、AES 128 ECB暗号化に内蔵AESハードウェアを使用するために定義できます。

#### WOLFSSL_CONTIKI

Contikiオペレーティングシステムのサポートを有効化するために定義できます。


#### WOLFSSL_COPY_CERT

SSLオブジェクトに読み込む際に証明書バッファを参照するのではなくコピーし、SSLオブジェクトが自身のデータのコピーを所有することを保証します。


#### WOLFSSL_COPY_KEY

SSLオブジェクトに読み込む際に秘密鍵バッファを参照するのではなくコピーし、SSLオブジェクトが自身のデータのコピーを所有することを保証します。

#### WOLFSSL_APACHE_MYNEWT

Apache Mynewtポートレイヤーを有効化するために定義できます。

#### WOLFSSL_APACHE_HTTPD

Apache HTTPDウェブサーバーのサポートを有効化するために定義できます。

#### ASIO_USE_WOLFSSL

wolfSSLをASIO互換バージョンとしてビルドするために定義できます。この場合、ASIOは`BOOST_ASIO_USE_WOLFSSL`プリプロセッサ定義に依存します。

#### WOLFSSL_CRYPTOCELL

ARM CRYPTOCELLの使用を有効化するために定義できます。

#### WOLFSSL_SIFIVE_RISC_V

RISC-V SiFive/HiFiveポートの使用を有効化するために定義できます。

#### WOLFSSL_MDK_ARM

MDK ARMのサポートを追加します

#### WOLFSSL_MDK5

MDK5 ARMのサポートを追加します

### OS固有の定義

#### USE_WINDOWS_API

Unix/Linux APIではなくWindowsライブラリAPIを使用することを指定します

#### WIN32_LEAN_AND_MEAN

Microsoft win32のlean and meanビルドのサポートを追加します。

#### FREERTOS_TCP

FREERTOS TCPスタックのサポートを追加します

#### WOLFSSL_SAFERTOS

SafeRTOSのサポートを追加します

## ビルドオプション

以下は、wolfSSLライブラリのビルド方法をカスタマイズするために`./configure`スクリプトに付加できるオプションです。

デフォルトでは、wolfSSLは共有モードのみでビルドされ、静的モードは無効化されています。これによりビルド時間が2倍高速化されます。必要に応じて、どちらのモードも明示的に無効化または有効化できます。

### `--enable-debug`

wolfSSLのデバッグサポートを有効化します。デバッグサポートを有効化すると、デバッグ情報付きでコンパイルされ、`stderr`にメッセージを出力する定数[`DEBUG_WOLFSSL`](#debug_wolfssl)が定義されるため、デバッグが容易になります。実行時にデバッグをオンにするには、[`wolfSSL_Debugging_ON()`](group__Debug.md#function-wolfssl_debugging_on)を呼び出します。実行時にデバッグログをオフにするには、[`wolfSSL_Debugging_OFF()`](group__Debug.md#function-wolfssl_debugging_off)を呼び出します。詳細については、[デバッグ](chapter08.md#debugging)を参照してください。

### `--enable-distro`

wolfSSLのディストロビルドを有効化します。

### `--enable-singlethread`

シングルスレッドモードを有効化し、マルチスレッド保護を無効にします。

シングルスレッドモードを有効化すると、セッションキャッシュのマルチスレッド保護がオフになります。シングルスレッドモードは、アプリケーションがシングルスレッドであることが分かっている場合、またはアプリケーションがマルチスレッドであっても一度に1つのスレッドのみがライブラリにアクセスする場合にのみ有効化してください。

### `--enable-dtls`

wolfSSLのDTLSサポートを有効化します

DTLSサポートを有効化すると、ライブラリの使用者はTLSおよびSSLに加えてDTLSプロトコルも使用できるようになります。詳細については、[DTLS](chapter04.md#dtls)のセクションを参照してください。

### `--disable-rng`

RNGのコンパイルと使用を無効化します

### `--enable-sctp`

wolfSSLのDTLS-SCTPサポートを有効化します

### `--enable-openssh`

OpenSSH互換ビルドを有効化します

### `--enable-apachehttpd`

Apache httpd互換ビルドを有効化します

### `--enable-openvpn`

OpenVPN互換ビルドを有効化します

### `--enable-opensslextra`

追加のOpenSSL API互換性を有効化します。サイズが増加します

OpenSSL Extraを有効化すると、より大きなOpenSSL互換関数のセットが含まれます。基本ビルドでも大半のTLS/SSLのニーズには十分な関数が有効化されますが、数十から数百のOpenSSL呼び出しを使用するアプリケーションを移植する場合、これを有効化することでより良いサポートが得られます。wolfSSLのOpenSSL互換レイヤーは活発に開発が進められているため、必要な関数が不足している場合はご連絡ください。サポートに努めます。OpenSSL互換レイヤーの詳細については、[OpenSSL互換性](chapter13.md#openssl-compatibility)を参照してください。

### `--enable-opensslall`

すべてのOpenSSL APIを有効化します。

### `--enable-maxstrength`

Max Strengthビルドを有効化し、TSLv1.2-AEAD-PFS暗号のみを許可します。相互運用性の問題を引き起こす可能性があるため、これはデフォルトでは無効化されています。また、グリッチング検出も有効化されます。

### `--disable-harden`

ハードニング、タイミング耐性、RSAブラインディングを無効化します。この機能を無効化するとパフォーマンスが向上する場合があります。

**注記** ハードニングはサイドチャネル攻撃に対する緩和策を提供します。この機能を無効化するのは、慎重に検討した上でのみにしてください。

user_settings.hで無効化する場合、同等の設定は次のとおりです:

* `#define WC_NO_CACHE_RESISTANT`
* `#define WC_NO_HARDEN`
* 設定`WC_RSA_BLINDING`が存在する場合は削除するか未定義にする
* 設定`ECC_TIMING_RESISTANT`が存在する場合は削除するか未定義にする
* 設定`TFM_TIMING_RESISTANT`が存在する場合は削除するか未定義にする

### `--enable-ipv6`

IPv6のテストを有効化します。wolfSSL本体はIPニュートラルです

IPV6を有効化すると、テストアプリケーションがIPv4の代わりにIPv6を使用するように変わります。wolfSSL本体はIPニュートラルであり、どちらのバージョンも使用できますが、現在のところテストアプリケーションはIPに依存しています。

### `--enable-bump`

SSL Bumpビルドを有効化します

### `--enable-leanpsk`

Lean PSKビルドを有効化します。

PSKを使用し、ライブラリから多くの機能を除去した非常に小さなビルドです。これを有効化した場合の組み込みシステムにおけるwolfSSLのおおよそのビルドサイズは21kBです。

### `--enable-leantls`

TLS 1.2クライアントのみ（クライアント認証なし）、ECC256、AES128、SHA256をShamirなしで実装するリーンなTLSです。現時点では単独で使用することを想定しており、他のビルドオプションとの併用は想定していません。

有効化すると、TLS 1.2クライアントのみ（クライアント認証なし）、ECC256、AES128、SHA256をShamirなしでサポートする小さなフットプリントのTLSクライアントが生成されます。現時点では単独で使用することを想定しており、他のビルドオプションとの併用は想定していません。

### `--enable-tinytls13`

TLS 1.3の極小フットプリントプロファイルを有効化します。これはTLS 1.3専用のビルドで、組み込みおよびリソース制約のあるターゲット向けに、wolfSSLを最小限のハンドシェイク機能まで削ぎ落としたものです。標準のwolfSSLマクロの上に構築された変換レイヤー（`settings.h`内の`WOLFSSL_TINY_TLS13`アンブレラ）として表現されているため、コアライブラリに新たな機能フラグを追加することはありません。

プロファイルはカンマ区切りのリストで選択します:

```sh
./configure --enable-tinytls13=LIST
```

フラグ単体の`--enable-tinytls13`は`--enable-tinytls13=psk`と同等です。このプロファイルは非FIPSであり、FIPSビルドと組み合わせることはできません。

#### 基本プロファイル

* `psk`（デフォルト）: X.509なしの事前共有鍵とECDHEによるビルドです。最低構成はX25519鍵交換、AES-128-GCM、SHA-256、HKDFです。これが最小の構成です。
* `cert`: 最低構成の上に最小限のX.509証明書チェーン検証を追加します。デフォルトではECDSA P-256を使用します。

証明書プロファイルはセキュリティを削減した検証です。コードサイズ削減のため、X.509名前制約の強制を無効化し、ASN.1の厳格性を緩和し、CRL失効チェックを削除します。既知またはピン留めされた認証局向けであり、一般の公開インターネットPKI向けではありません。証明書プロファイルが選択されると、configureは通知を表示します。

#### 追加項目（Adders）

以下をカンマ区切りリスト内で基本プロファイルと組み合わせることができます:

* `server`: TLS 1.3サーバーロールを追加します。デフォルトはクライアントのみです。
* `mutualauth`: X.509クライアント認証を伴う相互TLSです。`cert`を含意します。
* `staticmem`: 呼び出し側が提供する静的メモリプールからTLSの割り当てを行います。真のゼロヒープビルドには`WOLFSSL_NO_MALLOC`を別途追加してください。
* `asm`: 小さなCバックエンドの代わりにアセンブリ暗号を使用します。サイズは大きくなりますが高速です。
* `p256`: PSKの最低構成でX25519の代わりにP-256 ECDHEを使用します。
* `sha384`: SHA-384を追加します。例えばAES-256-GCM-SHA384用です。
* `mldsa`: ML-DSA-65証明書検証（検証のみ）を追加します。
* `rsaverify`: RSA-PSS証明書検証を追加します。証明書プロファイルはデフォルトではECDSAのみです。

例えば、ECDSA証明書を検証し、SHA-384もサポートするTLS 1.3サーバーは次のようになります:

```sh
./configure --enable-tinytls13=cert,server,sha384
```

#### フットプリント

以下の数値は、SHA-256のみの最低構成において、リンク時最適化とデッドコード除去（`-flto -ffunction-sections -fdata-sections -Wl,--gc-sections`）を伴う`-Os`でビルドした、リンク済みクライアントバイナリのフラッシュ（テキストとデータ）です。これはアプリケーションにリンクされるサイズであり、`libwolfssl.a`のサイズよりもはるかに小さくなります。ツールチェーン: newlib-nano使用のarm-none-eabi-gcc 14.2（Cortex-M33）、clang `-Os`（Intel x86_64）、newlib使用のaarch64-none-elf-gcc 14.2（ARM aarch64）。

| Configuration | Cortex-M33 | Intel x86_64 | ARM aarch64 |
| --- | --: | --: | --: |
| PSK, X25519 (floor) | 30,836 | 56,092 | 50,700 |
| PSK, P-256 | 37,347 | 66,068 | 59,292 |
| Cert, ECDSA P-256 | 62,765 | 110,817 | 95,404 |
| Mutual TLS | 66,554 | 116,789 | 99,836 |

Cortex-M33上での一般的な追加項目のコスト（PSK最低構成との比較）:

| Build | Flash (bytes) | Delta vs floor |
| --- | --: | --: |
| PSK floor (X25519) | 30,836 | 0 |
| plus static memory (zero heap) | 32,169 | +1,333 |
| plus P-256 | 37,347 | +6,511 |
| plus ML-DSA-65 verify | 43,780 | +12,944 |

Cortex-M33上のPSK P-256クライアント（37,347バイト）におけるフラッシュの内訳:

| Component | Bytes |
| --- | --: |
| TLS 1.3 handshake | 7,766 |
| TLS extensions | 5,678 |
| ECC P-256 (SP math) | 5,962 |
| CTX, SSL object and API | 4,050 |
| Record layer | 3,564 |
| AES-128-GCM | 2,650 |
| HMAC, HKDF, DRBG and RNG | 2,110 |
| SHA-256 | 1,370 |
| Key schedule | 1,120 |
| Memory and cleanup | 630 |
| C library (newlib) | 952 |
| Other and inlined | 1,486 |

#### アプリケーションに合わせたカスタマイズ

1. 最も近い基本プロファイルから始めてください。エンドポイント間で事前共有鍵を共有し、証明書が不要な場合は`psk`を使用します。既知の認証局からのX.509証明書を検証する必要がある場合は`cert`を使用します。
2. 必要な追加項目のみを追加してください。上の表が示すように、それぞれが意図的に計測されたサイズ増加のステップです。ビルドがサーバーの場合は`server`から始め、曲線や署名の追加項目はピアが必要とする場合にのみ追加してください。
3. 鍵交換曲線を選択してください。PSKの最低構成はX25519を使用し、証明書プロファイルはP-256を使用します。PSKの最低構成でP-256を使用したい場合、例えばECDSA証明書によってすでに組み込まれているP-256コードを再利用する場合は、`p256`を追加してください。代わりに独自のグループを指定する場合、例えばP-384やポスト量子ハイブリッドの場合は、その曲線を自分で選択し、`WOLFSSL_TINY_TLS13_NO_DEFAULT_CURVE`を定義してプロファイルがデフォルトのX25519やP-256を強制しないようにしてください。
4. メモリモデルを選択してください。デフォルトではシステムのアロケータを使用します。`wolfSSL_CTX_load_static_memory()`で供給される固定プールからTLSの割り当てを行うには、`staticmem`を追加してください。ヒープがまったくないターゲットの場合は、`WOLFSSL_NO_MALLOC`も定義してください。
5. ベアメタル向けにプラットフォームの接続部分を提供してください。サンプルテンプレートは`WOLFSSL_USER_IO`（送受信コールバックを自前で提供）、`NO_FILESYSTEM`、`WOLFSSL_NO_SOCK`を有効化しており、`CUSTOM_RAND_GENERATE_SEED`を通じてハードウェアエントロピー源を期待します。
6. サイズ重視でビルドしてください。未使用コードが除去されるよう、アプリケーションを`-Os -flto -ffunction-sections -fdata-sections -Wl,--gc-sections`でリンクしてください。

同じプロファイルは、autotoolsを使わずに`settings.h`の`WOLFSSL_TINY_TLS13`アンブレラを通じて利用できます。テンプレート`examples/configs/user_settings_tinytls13.h`は、`#if 0`と`#if 1`のブロックでプロファイルと追加項目を選択します。最小の結果は、デッドコード除去とリンク時最適化を行ったビルドから得られます:

```sh
cp ./examples/configs/user_settings_tinytls13.h user_settings.h
./configure --enable-usersettings --enable-static --disable-shared \
            --disable-examples --disable-crypttests
make
# link your application with:
#   -Os -flto -ffunction-sections -fdata-sections -Wl,--gc-sections
```

このプロファイルは完全に`WOLFSSL_TINY_TLS13`ファミリのマクロによって制御されます。各マクロはconfigureのリスト項目に対応しているため、`settings.h`によるビルドと`--enable-tinytls13`によるビルドは同じ機能を選択します:

| Macro | Configure item | Effect |
| --- | --- | --- |
| `WOLFSSL_TINY_TLS13` | `psk` | The base profile. TLS 1.3 only, PSK plus ECDHE, no X.509. X25519, AES-128-GCM, SHA-256 and HKDF. Every other macro implies this one. |
| `WOLFSSL_TINY_TLS13_CERT` | `cert` | Add minimal X.509 certificate chain verify (ECDSA P-256), and switch the default curve to P-256. Implies the base profile. |
| `WOLFSSL_TINY_TLS13_MUTUAL_AUTH` | `mutualauth` | Mutual TLS with X.509 client authentication. Implies `WOLFSSL_TINY_TLS13_CERT`. |
| `WOLFSSL_TINY_TLS13_SERVER` | `server` | Add the TLS 1.3 server role. The default is client only. |
| `WOLFSSL_TINY_TLS13_STATIC_MEM` | `staticmem` | Serve TLS allocations from a caller provided static memory pool. |
| `WOLFSSL_TINY_TLS13_ASM` | `asm` | Use assembly crypto instead of the small C backend. Larger but faster. |
| `WOLFSSL_TINY_TLS13_RSA_VERIFY` | `rsaverify` | Add RSA-PSS certificate verification. Pair with the certificate profile. |
| `WOLFSSL_TINY_TLS13_NO_DEFAULT_CURVE` | (none) | Suppress the automatic default curve. The profile then enables neither X25519 nor P-256, so you can select your own group (for example P-384 or a post quantum hybrid). |

configure項目の`p256`、`sha384`、`mldsa`には専用の極小マクロはありません。これらは標準のwolfSSLマクロ（`ECC_USER_CURVES`を伴う`HAVE_ECC`、`WOLFSSL_SHA384`、`WOLFSSL_MLDSA_VERIFY_ONLY`を伴う`WOLFSSL_HAVE_MLDSA`）で選択します。正確なブロックについては`examples/configs/user_settings_tinytls13.h`を参照してください。

このプロファイル用の、自己完結型のシングルプロセスTLS 1.3ハンドシェイクスモークテストが`examples/configs/tinytls13_smoke.c`に用意されています。ソケットやスレッドを使わずにメモリ内でクライアントとサーバーを接続するため、サンプルやテストハーネスが利用できない極小ビルドの検証に使用できます。

#### 推奨事項と禁止事項

* `cert`プロファイルは、既知またはピン留めされた認証局とのみ使用してください。その検証は意図的に削減されています（名前制約なし、ASN.1の緩和、CRLなし）。
* `cert`プロファイルを一般の公開インターネットPKIに使用しないでください。
* `mldsa`および`rsaverify`は`cert`プロファイルと組み合わせてください。これらは証明書検証アルゴリズムであるため、PSKの最低構成ではビルドがリンクできることを確認するだけになります。
* ベアメタルでは`CUSTOM_RAND_GENERATE_SEED`を通じて実際のハードウェアエントロピー源を提供してください。`NO_FILESYSTEM`などが設定されると、デフォルトのシード源は存在しなくなります。
* このプロファイルをFIPSビルドと組み合わせないでください。これは非FIPSのフットプリントプロファイルです。
* `WOLFSSL_NO_MALLOC`を定義した状態で標準の`make check`テストスイートが実行できると期待しないでください。このスイートはアロケータを必要とします。ゼロヒープビルドは、リンクしてスモークテストを実行することで検証してください。
* AES-256-GCM-SHA384はSHA-384のトランスクリプトを使用することに注意してください。外部の事前共有鍵を使用する場合は、暗号スイート固有のPSKコールバックを通じてSHA-384用に鍵をプロビジョニングするか、証明書プロファイル上でこの暗号を使用してください。そうしないとPSKバインダーが一致しません。

### `--enable-bigcache`

大きなセッションキャッシュを有効化します。

大きなセッションキャッシュを有効化すると、セッションキャッシュが33セッションから20,027セッションに増加します。デフォルトのセッションキャッシュサイズである33は、TLSクライアントおよび組み込みサーバーには十分です。大きなセッションキャッシュは、高負荷ではないサーバー、概ね毎分200程度の新規セッションを許容するサーバーに適しています。

### `--enable-hugecache`

巨大なセッションキャッシュを有効化します。

巨大なセッションキャッシュを有効化すると、セッションキャッシュサイズが65,791セッションに増加します。このオプションは高負荷のサーバー向けで、毎分13,000以上、または毎秒200以上の新規セッションが可能です。

### `--enable-smallcache`

小さなセッションキャッシュを有効化します。

小さなセッションキャッシュを有効化すると、wolfSSLは6セッションのみを保存するようになります。これは、デフォルトの約3kBではRAMが大きすぎる組み込みクライアントやシステムに有用です。この定義は500バイト未満のRAMを使用します。

### `--enable-savesession`

永続的なセッションキャッシュを有効化します。

このオプションを有効化すると、アプリケーションはwolfSSLセッションキャッシュをメモリバッファへ永続化（保存）し、そこから復元できるようになります。

### `--enable-savecert`

永続的な証明書キャッシュを有効化します。

このオプションを有効化すると、アプリケーションはwolfSSL証明書キャッシュをメモリバッファへ永続化（保存）し、そこから復元できるようになります。

### `--enable-atomicuser`

Atomic User Record Layerを有効化します。

このオプションを有効化すると、User Atomic Record Layer Processingコールバックが有効になります。これにより、アプリケーションは独自のMAC/暗号化および復号/検証コールバックを登録できるようになります。

### `--enable-pkcallbacks`

公開鍵コールバックを有効化します

このオプションを有効化すると、公開鍵コールバックが有効になり、アプリケーションは独自のECC署名/検証、RSA署名/検証および暗号化/復号のコールバックを登録できるようになります。

### `--enable-sniffer`

wolfSSLスニファサポートを有効化します。

スニファ（SSLインスペクション）サポートを有効化すると、SSLトラフィックパケットの収集に加え、正しい鍵ファイルを用いてそれらのパケットを復号する機能が利用できるようになります。

現在、スニファは以下のRSA暗号をサポートしています:

CBC暗号:

* AES-CBC
* Camellia-CBC
* 3DES-CBC

ストリーム暗号:

* RC4

### `--enable-aesgcm`

AES-GCMサポートを有効化します。

AES-GCMを有効化すると、これらの暗号スイートがwolfSSLに追加されます。wolfSSLは、速度とメモリ消費のバランスを取った4種類の異なるAES-GCM実装を提供しています。利用可能であれば、wolfSSLは64ビットまたは32ビット演算を使用します。組み込みアプリケーション向けには、RAMベースのルックアップテーブル（セッションあたり8KB）を使用する高速な8ビット版（速度は64ビット版に匹敵します）と、追加のRAMを消費しない低速な8ビット版があります。--enable-aesgcm configureオプションは、`=word32`、`=table`、`=small`のオプションで変更できます。例: `--enable-aesgcm=table`。

### `--enable-aesccm`

AES-CCMサポートを有効化します

### `--disable-aescbc`

[`--disable-aescbc`](#--disable-aescbc) と共に使用してAES-CBCをコンパイル対象から除外します

AES-GCMは、AESに対して8バイト認証付きのCounter with CBC-MACモード（CCM-8）を有効化します。

### `--enable-aescfb`

AES-CFBモードサポートを有効にします

### `--enable-aesctr`

wolfSSL AES-CTRサポートを有効化します

AES-CTRを有効化すると、Counterモードが有効になります。

### `--enable-aesni`

wolfSSL Intel AES-NIサポートを有効化します

AES-NIサポートを有効化すると、AES-NI対応チップの使用時に、チップから直接AES命令を呼び出せるようになります。これによりAES関数が高速化されます。AES-NIに関する詳細は[機能](chapter04.md#features)を参照してください。

### `--enable-intelasm`

IntelおよびAMDプロセッサ向けのASM高速化を有効化します。

wolfSSLのintelasmオプションを有効化すると、プロセッサの拡張機能を活用してAESの性能を劇的に向上させます。このconfigureオプションを有効化した際に活用される命令セットには、AVX1、AVX2、BMI2、RDRAND、RDSEED、AESNI、ADXが含まれます。これらは最初にIntelプロセッサに導入され、近年ではAMDプロセッサも採用を始めています。有効化すると、wolfSSLはプロセッサをチェックし、そのプロセッサがサポートする命令セットを活用します。

### `--enable-camellia`

Camelliaサポートを有効化します

### `--enable-md2`

MD2サポートを有効化します

### `--enable-nullcipher`

wolfSSL NULL暗号サポート（暗号化なし）を有効化します

### `--enable-ripemd`

wolfSSL RIPEMD-160サポートを有効化します

### `--enable-blake2`

wolfSSL BLAKE2サポートを有効化します

### `--enable-blake2s`

wolfSSL BLAKE2sサポートを有効化します

### `--enable-sha3`

x86\_64およびAarch64ではデフォルトで有効です。

wolfSSL SHA3サポートを有効化します（`=small`で小型ビルド）

### `--enable-sha512`

x86\_64ではデフォルトで有効です。

wolfSSL SHA-512サポートを有効化します

### `--enable-she`

SHE（Secure Hardware Extension）鍵更新メッセージ生成サポートを有効化します。SHEは、ECUにおけるセキュアな鍵管理のための車載セキュリティ標準です。このモジュールは、SHE鍵更新メッセージ（M1〜M5）のソフトウェアベースの生成と検証を提供し、cryptoコールバックによるオプションのハードウェアオフロードにも対応します。

2つのモードが利用できます:

- `--enable-she=standard` - 標準のSHEサポート（`WOLFSSL_SHE`を定義）
- `--enable-she=extended` - 標準に加え、カスタムKDF定数およびメッセージヘッダの拡張オーバーライドをサポート（`WOLFSSL_SHE`と`WOLFSSL_SHE_EXTENDED`を定義）

SHEは、その依存機能であるAES、AES-CBC、AES-direct、CMACを自動的に有効化します。

以下の定義を使用して、オプション機能をコンパイル対象から除外できます:

- `NO_WC_SHE_GETUID` - `wc_SHE_GetUID`コールバックをコンパイル対象から除外
- `NO_WC_SHE_GETCOUNTER` - `wc_SHE_GetCounter`コールバックをコンパイル対象から除外
- `NO_WC_SHE_IMPORT_M123` - `wc_SHE_ImportM1M2M3`をコンパイル対象から除外
- `NO_WC_SHE_EXPORTKEY` - `wc_SHE_ExportKey`コールバックをコンパイル対象から除外
- `NO_WC_SHE_LOADKEY` - すべての`wc_SHE_LoadKey` / `wc_SHE_LoadKey_Verify`便利ラッパーをコンパイル対象から除外
- `WC_SHE_SW_DEFAULT` - ハードウェアなしでのテスト用に、ソフトウェアのみのデフォルトUIDおよびカウンタを有効化

### `--enable-sessioncerts`

セッション証明書の保存を有効化します

### `--enable-keygen`

鍵生成を有効化します（RSA鍵生成のみに適用されます）

### `--enable-certgen`

証明書生成を有効化します

### `--enable-certext`

証明書拡張を有効化します（サポートされる拡張については第7章を参照）

### `--enable-certreq`

証明書リクエスト生成を有効化します

### `--enable-sep`

SEP拡張を有効化します

### `--enable-hkdf`

HKDF（HMAC-KDF）を有効化します

### `--enable-x963kdf`

X9.63 KDFサポートを有効化します

### `--enable-dsa`

デジタル署名アルゴリズム（DSA）を有効化します。

FIPS 186-4で定義されている、RSAおよびECDSAと並ぶNIST承認のデジタル署名アルゴリズムであり、Secure Hash Standard（FIPS 180-4）で定義されている承認済みハッシュ関数と組み合わせて使用することで、デジタル署名の生成と検証に用いられます。

### `--enable-eccshamir`

x86\_64ではデフォルトで有効です

ECC Shamirを有効化します

### `--enable-ecc`

x86\_64ではデフォルトで有効です

ECCを有効化します。

このオプションを有効化すると、ECCサポートと暗号スイートがwolfSSLにビルドされます。

### `--enable-ecccustcurves`

ECCカスタム曲線を有効化します（`=all`ですべての曲線タイプを有効化）

### `--enable-compkey`

圧縮鍵サポートを有効化します

### `--enable-curve25519`

Curve25519を有効化します（または`--enable-curve25519=small`でCURVE25519\_SMALL）。

128ビットのセキュリティを提供する楕円曲線であり、ECDH鍵合意で使用されます（[クロスコンパイル](#cross-compiling)を参照）。curve25519オプションを有効化すると、curve25519アルゴリズムが使用できるようになります。デフォルトのcurve25519は、より多くのメモリを使用する代わりに実行時間が速くなるよう設定されています。アルゴリズムのメモリ使用量を減らすには、`--enable-curve25519=small`オプションを使用できます。ただし、メモリ使用量が減る代わりに速度とのトレードオフがあります。

### `--enable-ed25519`

ED25519を有効化します（または`--enable-ed25519=small`でED25519\_SMALL）

ed25519オプションを有効化すると、ed25519アルゴリズムが使用できるようになります。デフォルトのed25519は、より多くのメモリを使用する代わりに実行時間が速くなるよう設定されています。アルゴリズムのメモリ使用量を減らすには、`--enable-ed25519=small`オプションを使用できます。curve25519と同様、このenableオプションの使用は速度とメモリのトレードオフとなります。

### `--enable-fpecc`

固定小数点キャッシュECCを有効化します

### `--enable-eccencrypt`

ECC暗号化を有効化します

### `--enable-psk`

PSK（事前共有鍵）を有効化します

### `--disable-errorstrings`

エラー文字列テーブルを無効化します

### `--disable-oldtls`

1.2未満の古いTLSバージョンを無効化します

### `--enable-sslv3`

SSLバージョン3.0を有効化します

### `--enable-stacksize`

サンプルプログラムでのスタックサイズ情報を有効化します

### `--disable-memory`

メモリコールバックを無効化します

### `--disable-rsa`

RSAを無効化します

### `--enable-rsapss`

RSA-PSSを有効化します

### `--disable-dh`

DHを無効化します

### `--enable-anon`

Anonymous（匿名）暗号スイートを有効化します

### `--disable-asn`

ASNを無効化します

### `--disable-aes`

AESを無効化します

### `--disable-coding`

Base 16/64コーディングを無効化します

### `--enable-base64encode`

x86\_64ではデフォルトで有効です

Base64エンコーディングを有効化します

### `--disable-des3`

DES3を無効化します

### `--enable-arc4`

ARC4を有効化します

### `--disable-md5`

MD5を無効化します

### `--disable-sha`

SHAを無効化します

### `--enable-webserver`

Webサーバーを有効化します。

これは、yaSSL Embedded Web Serverでビルドするための完全な機能を実現するために、標準ビルドに加えて必要な関数を有効にします。

### `--enable-fips`

FIPS 140-2を有効化します（実装にはライセンスが必要です。）

### `--enable-sha224`

x86\_64ではデフォルトで有効です

wolfSSL SHA-224サポートを有効化します

### `--disable-poly1305`

wolfSSL POLY1305サポートを無効化します

### `--disable-chacha`

CHACHAを無効化します

### `--disable-hashdrbg`

Hash DRBGサポートを無効化します

### `--disable-filesystem`

ファイルシステムサポートを無効化します。

これにより、ファイルシステムの使用を無効化しやすくなります。このオプションは[`NO_FILESYSTEM`](#no_filesystem)を定義します。

### `--disable-inline`

インライン関数を無効化します。

このオプションを無効化すると、wolfSSLにおける関数のインライン化が無効になります。関数のインライン化が有効な場合、関数のプレースホルダはリンクされず、代わりにコードブロックが関数呼び出し箇所に挿入されます。

### `--enable-ocsp`

オンライン証明書ステータスプロトコル（OCSP）を有効化します。

このオプションを有効化すると、OCSP（Online Certificate Status Protocol）サポートがwolfSSLに追加されます。これは、[RFC 6960](https://tex2e.github.io/rfc-translater/html/rfc6960)に記載されているとおり、x.509証明書の失効状態を取得するために使用されます。

### `--enable-ocspstapling`

OCSP Staplingを有効化します（`=no-multi`でTLS1.3証明書に対する複数OCSP Staplingを無効化）

### `--enable-ocspstapling2`

OCSP Staplingバージョン2を有効化します

### `--enable-crl`

CRL（証明書失効リスト）を有効化します

### `--enable-crl-monitor`

CRLモニターを有効化します。

このオプションを有効化すると、wolfSSLが特定のCRL（証明書失効リスト）ディレクトリを能動的に監視する機能が追加されます。

### `--enable-sni`

Server Name Indication（SNI）を有効化します。

このオプションを有効化すると、TLSのServer Name Indication（SNI）拡張が有効になります。

### `--enable-maxfragment`

最大フラグメント長を有効化します。

このオプションを有効化すると、TLSのMaximum Fragment Length拡張が有効になります。

### `--enable-alpn`

Application Layer Protocol Negotiation（ALPN）を有効化します

### `--enable-truncatedhmac`

Truncated Keyed-hash MAC（HMAC）を有効化します。

このオプションを有効化すると、TLSのTruncated HMAC拡張が有効になります。

### `--enable-renegotiation-indication`

Renegotiation Indicationを有効化します。

[RFC 5746](https://tex2e.github.io/rfc-translater/html/rfc5746)に記載されているとおり、この仕様は、再ネゴシエーションをそれが実行されるTLS接続に結び付けることにより、再ネゴシエーションのスプライシングを伴うSSL/TLS攻撃を防止します。

### `--enable-secure-renegotiation`

Secure Renegotiationを有効化します

### `--enable-supportedcurves`

Supported Elliptic Curvesを有効化します。

このオプションを有効化すると、TLSのSupported ECC Curves拡張が有効になります。

### `--enable-session-ticket`

セッションチケットを有効化します

### `--enable-extended-master`

Extended Master Secretを有効化します

### `--enable-tlsx`

すべてのTLS拡張を有効化します。

このオプションを有効化すると、wolfSSLが現在サポートしているすべてのTLS拡張が有効になります。

### `--enable-pkcs7`

PKCS#7サポートを有効化します

### `--enable-pkcs11`

PKCS#11アクセスを有効化します

### `--enable-ssh`

wolfSSHオプションを有効化します

### `--enable-scep`

wolfSCEP（Simple Certificate Enrollment Protocol）を有効化します

Internet Engineering Task Forceによって定義されているとおり、Simple Certificate Enrollment Protocolは、HTTP上でPKCS#7およびPKCS#10を活用するPKIです。CERTは、SCEPが証明書リクエストを強力に認証しないことを指摘しています。

### `--enable-srp`

Secure Remote Passwordを有効化します

### `--enable-smallstack`

小スタック使用を有効化します

### `--enable-valgrind`

ユニットテストでのvalgrindを有効化します。

このオプションを有効化すると、wolfSSLユニットテストの実行時にvalgrindが有効になります。これは、開発サイクルの早い段階で問題を発見するのに役立ちます。

### `--enable-testcert`

テスト証明書を有効化します。

このオプションを有効化すると、通常は公開されないASN証明書APIの一部が公開されます。これは、wolfCryptテストアプリケーション（`wolfcrypt/test/test.c`）で見られるように、テスト目的で役立ちます。

### `--enable-iopool`

I/Oプールのサンプルを有効化します

### `--enable-certservice`

証明書サービスを有効化します（Windowsサーバー）

### `--enable-jni`

wolfSSL JNIを有効化します

### `--enable-lighty`

lighttpd/lightyを有効化します

### `--enable-stunnel`

stunnelを有効化します

### `--enable-md4`

MD4を有効化します

### `--enable-pwdbased`

PWDBASEDを有効化します

### `--enable-scrypt`

SCRYPTを有効化します

### `--enable-cryptonly`

wolfCryptのみのビルドを有効化します

### `--disable-examples`

サンプルプログラムのビルドを無効化します。

有効な場合、wolfSSLのサンプルアプリケーションがビルドされます（[client](chapter03.md#client-example)、[server](chapter03.md#server-example)、[echoclient](chapter03.md#echoclient-example)、[echoserver](chapter03.md#echoserver-example)）。

### `--disable-crypttests`

Cryptベンチマーク/テストを無効化します

### `--enable-fast-rsa`

Intel IPPを使用するRSAを有効化します。

fast-rsaを有効化すると、IPPライブラリを使用してRSA演算が高速化されます。wolfSSLのデフォルトのRSAよりもメモリ消費が大きくなります。IPPライブラリが見つからない場合、configure時にエラーメッセージが表示されます。autoconfが最初に探す場所はディレクトリ`wolfssl_root/IPP`で、2番目はLinuxシステムの`/usr/lib/`のような、そのマシン上のライブラリの標準的な場所です。

RSA演算に使用されるライブラリはディレクトリ`wolfssl-X.X.X/IPP/`にあります（X.X.X は現在のwolfSSLバージョン番号）。同梱のライブラリからのビルドはIPPのディレクトリの場所と名前に依存するため、サブディレクトリIPPのファイル構造を変更しないでください。

メモリを割り当てる際、fast-rsa演算には`DYNAMIC_TYPE_USER_CRYPTO`というメモリタグが付きます。これにより、fast-rsaオプション使用時に実行時のRSA演算のメモリ消費を確認できます。

### `--enable-staticmemory`

静的メモリ使用を有効化します

### `--enable-mcapi`

Microchip APIを有効化します

### `--enable-asynccrypt`

非同期暗号を有効化します

### `--enable-sessionexport`

セッションのエクスポートおよびインポートを有効化します

### `--enable-aeskeywrap`

AES key wrapサポートを有効化します

### `--enable-jobserver`

値: `yes`（デフォルト）/ `no` / `#`

`make`使用時に、wolfSSLをマルチスレッドビルドでビルドします。`yes`（デフォルト）はCPUコア数を検出し、そのコア数に対する推奨ジョブ数でビルドします。`#`で正確な数を指定できます。これは`make -j`オプションと同様の仕組みで動作します。

### `--enable-shared[=PKGS]`

wolfSSL共有ライブラリをビルドします [default=yes]

共有ライブラリビルドを無効化すると、wolfSSL共有ライブラリがビルド対象から除外されます。時間とスペースを節約するため、デフォルトでは共有ライブラリのみがビルドされます。

### `--enable-static[=PKGS]`

wolfSSL静的ライブラリをビルドします [default=no]

### `--with-liboqs=PATH`

OpenQuantumSafeのインストールパス（デフォルト`/usr/local`）。

これは、wolfSSLがliboqsとのwolfSSL統合を介して、実験的なTLS 1.3の耐量子KEMグループ、ハイブリッド耐量子KEMグループ、およびFALCON署名スキームを使用する機能を有効にします。詳細は本ドキュメントの付録「Experimenting with Quantum-Safe Cryptography」を参照してください。

### `--with-libz=PATH`

オプションで圧縮用にlibzを含めます。

libzを有効化すると、libzライブラリによる圧縮サポートがwolfSSLで利用できるようになります。このオプションを含めて[`wolfSSL_set_compression()`](group__Setup.md#function-wolfssl_set_compression)を呼び出して使用するかどうかは、よく検討してください。送信前にデータを圧縮することで、送受信されるメッセージの実際のサイズは減少しますが、最も低速なネットワークを除けば、圧縮によって節約されるデータ量の分析にかかる時間は、通常、そのまま送信するよりも長くかかります。

### `--with-cavium`

cavium/softwareディレクトリへのパス。

### `--with-user-crypto`

USER\_CRYPTOのインストールパス（デフォルト`/usr/local`）。

### `--enable-rsavfy`

RSA検証のみのサポートを有効化します（**注記** [`--enable-cryptonly`](#--enable-cryptonly)が必要です）

### `--enable-rsapub`

RSA公開鍵のみのサポートを有効化します（**注記** [`--enable-cryptonly`](#--enable-cryptonly)が必要です）

### `--enable-armasm`

ARMv8 ASMサポートを有効化します。

デフォルトのconfigureは、64ビットか32ビットのシステムかに基づいてmcpuまたはmfpuを設定します。CPPFLAGSで渡されたmcpuまたはmfpuの設定を上書きすることはありません。一部のコンパイラでは制約により`-mstrict-align`が必要になる場合があり、ユーザーが`CPPFLAGS`でmcpu/mfpuフラグを渡さない限り、現在は`-mstrict-align`もデフォルトで設定されます。

### `--disable-tlsv12`

TLS 1.2サポートを無効化します

### `--enable-tls13`

TLS 1.3サポートを有効化します

このビルドオプションは、[`--disable-tlsv12`](#--disable-tlsv12)および[`--disable-oldtls`](#--disable-oldtls)と組み合わせることで、TLS 1.3のみのwolfSSLビルドを生成できます。

### `--enable-all`

SSL v3を除く、wolfSSLのすべての機能を有効化します

### `--enable-xts`

AES-XTSモードを有効化します

### `--enable-asio`

ASIOを有効化します。

wolfSSLのconfigure時に、オプション[`--enable-opensslextra`](#--enable-opensslextra)および[`--enable-opensslall`](#--enable-opensslall)が有効化されている必要があります。これら2つのオプションが有効化されていない場合、wolfSSLのconfigure時にASIOを有効にするため、autoconfツールが自動的にこれらのオプションを有効化します。

### `--enable-qt`

Qt 5.12以降のサポートを有効化します。

wolfSSL Qtポートと互換性のあるwolfSSLビルド設定を有効にします。Qtソースファイルへのパッチ適用には、wolfSSLからのパッチファイルが必要です。

### `--enable-qt-test`

Qtテスト互換ビルドを有効化します。

Qt組み込みテストの実行と互換性のあるwolfSSLビルドのサポートを有効にします。

### `--enable-apache-httpd`

Apache httpdサポートを有効化します

### `--enable-afalg`

ハードウェアアクセラレーションのためのLinuxモジュールAF\_ALGの使用を有効化します。`=xilinx`、`=xilinx-rsa`、`=xilinx-aes`、`=xilinx-sha3`でXilinx向けの追加利用が可能です

暗号演算のオフロードにLinuxカーネルモジュール（AF\_ALG）を活用するという点で、[`--enable-devcrypto`](#--enable-devcrypto)に似ています。一部のハードウェアでは、このモジュールはLinux暗号ドライバによる性能アクセラレーションを利用できます。XilinxのPetalinuxの場合、フラグ`--enable-afalg=xilinx`を使用して、AF\_ALGにXilinxインターフェースを使用するようwolfSSLに指示できます。

### `--enable-devcrypto`

ハードウェアアクセラレーションのためのLinux `/dev/crypto`の使用を有効化します。

引数を受け取ることができ、`aes`（すべてのAESサポート）、`hash`（すべてのハッシュアルゴリズム）、`cbc`（aes-cbcのみ）の任意の組み合わせを受け取れます。オプションが指定されない場合、デフォルトで`all`が使用されます。

### `--enable-mcast`

wolfSSL DTLSマルチキャストサポートを有効化します

### `--disable-pkcs12`

PKCS12コードを無効化します

### `--enable-fallback-scsv`

Signaling Cipher Suite Value(SCSV)を有効化します

### `--enable-psk-one-id`

TLS 1.3での単一PSK IDのサポートを有効化します

### `--enable-cryptocb`

cryptoコールバックを有効化します。wc_CryptoCb_RegisterDeviceを使用してcryptoコールバックを登録し、wolfSSL_CTX_SetDevIdを使用して関連付けるdevIdを設定してください。

以下の2つの定義を`--enable-cryptocb`と共に使用することで、RSAまたはECCのソフトウェアフォールバックをコンパイル対象から除外し、ソフトウェアRSA/ECCが不要な場合のフットプリント削減の最適化ができます。

* WOLF_CRYPTO_CB_ONLY_RSA - RSAソフトウェア暗号フォールバックをコンパイル対象から除外
* WOLF_CRYPTO_CB_ONLY_ECC - ECCソフトウェア暗号フォールバックをコンパイル対象から除外

WOLF_CRYPTO_CB_ONLY_*オプションの使用には、サンプルプログラムの無効化が必要です。[`--disable-examples`](#--disable-examples)を参照してください

### `--enable-reproducible-build`

バイナリのジッタ（タイムスタンプおよびその他の機能に影響しないメタデータ）を抑制し、同一のハッシュを持つビット単位で同一のバイナリパッケージの生成を可能にします。

### `--enable-sys-ca-certs`
[`wolfSSL_CTX_load_system_CA_certs()`](group__CertsKeys.html#function-wolfssl_ctx_load_system_ca_certs)が呼び出された際に、wolfSSLが検証に信頼済みシステムCA証明書を使用できるようにします。これは、wolfSSL証明書マネージャに証明書をロードするか、システム認証APIを呼び出すことによって行われます。詳細は[`wolfSSL_CTX_load_system_CA_certs()`](group__CertsKeys.html#function-wolfssl_ctx_load_system_ca_certs)を参照してください。

## 特殊な数学最適化フラグ

### `--enable-fastmath`

FastMath実装を有効化します。Single-Precision（SP）数学が有効な場合、FastMathとBig Integerライブラリは両方とも無効化されます。

USE_FAST_MATHおよびBig Integer Math Libraryのセクションを参照してください。

### `--enable-fasthugemath`

fast math + 巨大コードを有効化します。

fasthugemathを有効化するとFastMathライブラリのサポートが含まれ、公開鍵演算時に一般的な鍵サイズに対するループ展開を行うことで、コードサイズが大幅に増加します。fasthugemathの使用前後でベンチマークユーティリティを使用し、わずかな高速化がコードサイズの増加に見合うかどうかを確認してみてください。

### `--enable-sp-math`

制限されたアルゴリズムスイートによるSingle-Precision（SP）数学実装を有効化します。サポートされないアルゴリズムは無効化されます。`--enable-sp`、`--enable-sp-math-all`、`--enable-fastmath`、`--enable-fasthugemath`より優先されます。

- 数学実装をsp_int.c内のものに置き換えます
- 最小限の実装であり、sp_int.cの一部のみを有効にします
- RSA/ECC/DH演算を実行できるようにするには、sp_x86_64.cやsp_arm.cなど（ターゲットシステムに応じた以下のファイルリスト）のソリューションを有効にするため、必ず--enable-spと組み合わせる必要があります
- --enable-sp-math-all（下記）と組み合わせてはいけません

ファイルリスト（プラットフォーム依存で、システム仕様に基づいてconfigureが選択します。Makefile/IDEソリューションを使用する場合は手動で制御できます）:
sp_arm32.c
sp_arm64.c
sp_armthumb.c
sp_cortexm.c
sp_dsp32.c
sp_x86_64.c
sp_x86_64_asm.S
sp_x86_64_asm.asm

### `--enable-sp-math-all`

デフォルトで有効です。完全なアルゴリズムスイートによるSingle-Precision（SP）数学実装を有効化します。サポートされないアルゴリズムも有効化されますが、最適化はされません。`--enable-sp`、`--enable-fastmath`、`--enable-fasthugemath`より優先されます。

- 数学実装をsp_int.c内のものに置き換えます
- 完全な実装であり、動作に--enable-spを必要としません
- --enable-spと組み合わせることで、可能な場合には、32ビットではsp_c32.c、64ビットではsp_c64.cに記述されたポータブルCアセンブリ（ハードウェア非依存のアセンブリ）による実装を使用できます。それ以外の場合（不可能な場合）はsp_int.cの実装が使用されます。ポータブルCアセンブリは、ハードウェア最適化が利用できないターゲットにおいて大きな性能向上をもたらします
- --enable-sp-math（上記）と組み合わせてはいけません

**注記**: 鍵長（ビット）が[256, 384, 521, 1024, 2048, 3072, 4096]の非対称暗号を使用している場合は、フットプリントサイズは大きくなりますが最大の性能を得るために、--enable-sp-mathオプションの使用を検討してください。

### `--enable-sp-asm`

Single-Precision（SP）アセンブリ実装を有効化します。

Intel x86\_64およびARMアーキテクチャで、アセンブリによるSingle-Precisionの性能向上を有効にするために使用できます。

### `--enable-sp=OPT`

性能向上のため、RSA、DH、ECCに対するSingle-Precision（SP）数学を有効化します。

OPTには多くの値を指定できます。以下は、enable-spの呼び出し方と、その結果として定義されるマクロの一覧です。これらはすべてカンマ区切りリストで組み合わせることができます。例: `--enable-sp=ec256,ec384`。定義されるマクロの意味は、上記の[wolfSSL’s Proprietary Single Precision (SP) Math Support]セクションで定義されています。

**注記**:
1) "--enable-sp=small --enable-sp-math" は次より小さくなる可能性があります...
2) "--enable-sp-math-all=small"...
これは、(1)が特定の鍵サイズの実装のみを持つのに対し、(2)はすべての鍵サイズをサポートする実装を持つためです。

**注記**: これはx86_64で他のconfigureフラグを指定しない場合の話です。アーキテクチャや指定する他のconfigureフラグによって結果は異なる場合があります。例えば、WOLFSSL_SP_384およびWOLFSSL_SP_4096はIntel x86_64でのみ有効化されます。

#### `--enable-sp=no` or `--disable-sp`

新しいマクロは定義されません。`--enable-sp`を使用しない場合と同等です。

#### `--enable-sp` or `--enable-sp=yes`

* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_HAVE_SP_ECC
* WOLFSSL_HAVE_SP_DH
* WOLFSSL_SP_384
* WOLFSSL_SP_4096
* WOLFSSL_SP_LARGE_CODE

#### `--enable-sp=small`

* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_HAVE_SP_DH
* WOLFSSL_HAVE_SP_ECC
* WOLFSSL_SP_4096
* WOLFSSL_SP_384
* WOLFSSL_SP_4096
* WOLFSSL_SP_SMALL
* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_384

#### `--enable-sp=smallfast`

* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_HAVE_SP_DH
* WOLFSSL_HAVE_SP_ECC
* WOLFSSL_SP_4096
* WOLFSSL_SP_384
* WOLFSSL_SP_SMALL
* WOLFSSL_SP_4096
* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_FAST_MODEXP

#### `--enable-sp=ec256` or `--enable-sp=p256` or `--enable-sp=p256`

* WOLFSSL_HAVE_SP_ECC

#### `--enable-sp=smallec256` or `--enable-sp=smallp256` or `--enable-sp=small256`

* WOLFSSL_HAVE_SP_ECC
* WOLFSSL_SP_SMALL

#### `--enable-sp=ec384` or `--enable-sp=p384` or `--enable-sp=384`

* WOLFSSL_HAVE_SP_ECC
* WOLFSSL_SP_384
* WOLFSSL_SP_NO_256

#### `--enable-sp=smallec384` or `--enable-sp=smallp384` or `--enable-sp=small384`

* WOLFSSL_HAVE_SP_ECC
* WOLFSSL_SP_384
* WOLFSSL_SP_NO_256
* WOLFSSL_SP_SMALL

#### `--enable-sp=ec1024` or `--enable-sp=p1024` or `--enable-sp=1024`

* WOLFSSL_HAVE_SP_ECC
* WOLFSSL_SP_1024
* WOLFSSL_SP_NO_256

#### `--enable-sp=smallec1024` or `--enable-sp=smallp1024` or `--enable-sp=small1024`

* WOLFSSL_HAVE_SP_ECC
* WOLFSSL_SP_1024
* WOLFSSL_SP_NO_256
* WOLFSSL_SP_SMALL

#### `--enable-sp=2048`

* WOLFSSL_HAVE_SP_DH
* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_NO_3072

#### `--enable-sp=small2048`

* WOLFSSL_HAVE_SP_DH
* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_NO_3072
* WOLFSSL_SP_SMALL

#### `--enable-sp=rsa2048`

* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_NO_3072

#### `--enable-sp=smallrsa2048`

* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_NO_3072
* WOLFSSL_SP_SMALL

#### `--enable-sp=3072`

* WOLFSSL_HAVE_SP_DH
* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_NO_2048

#### `--enable-sp=small3072`

* WOLFSSL_HAVE_SP_DH
* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_NO_2048
* WOLFSSL_SP_SMALL

#### `--enable-sp=rsa3072`

* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_NO_2048

#### `--enable-sp=smallrsa3072`

* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_NO_2048
* WOLFSSL_SP_SMALL

#### `--enable-sp=4096` or `--enable-sp=+4096`

* WOLFSSL_HAVE_SP_DH
* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_SP_4096
* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_NO_2048
* WOLFSSL_SP_NO_3072

#### `--enable-sp=small4096`

* WOLFSSL_HAVE_SP_DH
* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_SP_4096
* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_NO_2048
* WOLFSSL_SP_NO_3072
* WOLFSSL_SP_SMALL

#### `--enable-sp=rsa4096`

* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_SP_4096
* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_NO_2048
* WOLFSSL_SP_NO_3072

#### `--enable-sp=smallrsa4096`

* WOLFSSL_HAVE_SP_RSA
* WOLFSSL_SP_4096
* WOLFSSL_SP_LARGE_CODE
* WOLFSSL_SP_NO_2048
* WOLFSSL_SP_NO_3072
* WOLFSSL_SP_SMALL

#### `--enable-sp=nomalloc`

* WOLFSSL_SP_NO_MALLOC

#### `--enable-sp=nonblock`

* WOLFSSL_SP_NO_MALLOC
* WOLFSSL_SP_NONBLOCK
* WOLFSSL_SP_SMALL

#### `asm`

他のアルゴリズムオプションと組み合わせて、それらのオプションに対してアセンブリコードを有効にすることを示します。例: `--enable-sp=rsa2048,asm`

## クロスコンパイル

組み込みプラットフォーム上の多くのユーザーは、それぞれの環境向けにwolfSSLをクロスコンパイルしています。ライブラリをクロスコンパイルする最も簡単な方法は、`./configure`システムを使用することです。これによりMakefileが生成され、それを使用してwolfSSLをビルドできます。

クロスコンパイルする際には、次のように`./configure`にホストを指定する必要があります。

```sh
./configure --host=arm-linux
```

また、使用するコンパイラやリンカなどの指定が必要になる場合もあります。

```sh
./configure --host=arm-linux CC=arm-linux-gcc AR=arm-linux-ar RANLIB=arm-linux
```

configureシステムには、クロスコンパイル時にユーザーによるmallocのオーバーライドを検出する際に発生する可能性のあるバグがあります。`rpl_malloc`や`rpl_realloc`への未定義参照が発生した場合は、`./configure`の行に以下を追加してください。

```sh
ac_cv_func_malloc_0_nonnull=yes ac_cv_func_realloc_0_nonnull=yes
```

クロスコンパイル用にwolfSSLを正しく設定した後は、標準的なautoconfの手順に従ってライブラリのビルドとインストールを行えるはずです。

```sh
make
sudo make install
```

wolfSSLのクロスコンパイルに関して追加のヒントやフィードバックがありましたら、[info@wolfssl.jp](mailto:info@wolfssl.jp)までお知らせください。

### ツールチェーンビルド向けのクロスコンパイルconfigureオプション例

#### armebv7-eabihf-glibc

```sh
./configure --host=armeb-linux \
        CC=armeb-linux-gcc LD=armeb-linux-ld \
        AR=armeb-linux-ar \
        RANLIB=armeb-linux-ranlib \
        CFLAGS="-DWOLFSSL_USER_IO -Os" \
        CPPFLAGS="-I./"
```

#### armv5-eabi-glibc

```sh
./configure --host=arm-linux \
        CC=arm-linux-gcc LD=arm-linux-ld \
        AR=arm-linux-ar \
        RANLIB=arm-linux-ranlib \
        CFLAGS="-DWOLFSSL_USER_IO -Os" \
        CPPFLAGS="-I./"
```

#### armv6-eabihf-glibc

```sh
./configure --host=arm-linux \
        CC=arm-linux-gcc LD=arm-linux-ld \
        AR=arm-linux-ar \
        RANLIB=arm-linux-ranlib \
        CFLAGS="-DWOLFSSL_USER_IO -Os" \
        CPPFLAGS="-I./"
```

#### armv7-eabihf-glibc

```sh
./configure --host=arm-linux \
        CC=arm-linux-gcc LD=arm-linux-ld \
        AR=arm-linux-ar \
        RANLIB=arm-linux-ranlib \
        CFLAGS="-DWOLFSSL_USER_IO -Os" \
        CPPFLAGS="-I./"
```

#### armv7m-uclibc

```sh
./configure --enable-static --disable-shared \--host=arm-linux CC=arm-linux-gcc \
        LD=arm-linux-ld AR=arm-linux-ar \
        RANLIB=arm-linux-ranlib \
        CFLAGS="-DWOLFSSL_USER_IO -Os" \
        CPPFLAGS="-I./"
```

#### arm-none-eabi-gcc

```sh
./configure --host=arm-none-eabi \
        CC=arm-none-eabi-gcc LD=arm-none-eabi-ld \
        AR=arm-none-eabi-ar RANLIB=arm-none-eabi-ranlib \
        CFLAGS="-DNO_WOLFSSL_DIR \
        -DWOLFSSL_USER_IO -DNO_WRITEV \
        -mcpu=cortex-m4 -mthumb -Os \
        -specs=rdimon.specs" CPPFLAGS="-I./"
```

#### mips32--glibc

```sh
./configure --host=mips-linux \
        CC=mips-linux-gcc LD=mips-linux-ld \
        AR=mips-linux-ar \
        RANLIB=mips-linux-ranlib \
        CFLAGS="-DWOLFSSL_USER_IO -Os" \
        CPPFLAGS="-I./"
```

#### PowerPc64le-Power8-Glibc

```sh
./configure --host=powerpc64le-buildroot-linux-gnu \
        CC=powerpc64le-buildroot-linux-gnu-gcc \
        LD=powerpc64le-buildroot-linux-gnu-ld \
        AR=powerpc64le-buildroot-linux-gnu-ar \
        RANLIB=powerpc64le-buildroot-linux-gnu-ranlib \
        CFLAGS="-DWOLFSSL_USER_IO -Os" \
        CPPFLAGS="-I./"
```

#### x86-64-core-i7-glibc

```sh
./configure --host=x86_64-linux \
        CC=x86_64-linux-gcc LD=x86_64-linux-ld \
        AR=x86_64-linux-ar \
        RANLIB=x86_64-linux-ranlib \
        CFLAGS="-DWOLFSSL_USER_IO -Os" \
        CPPFLAGS="-I./"
```

#### x86-64-core-i7-musl

```sh
./configure --host=x86_64-linux \
        CC=x86_64-linux-gcc LD=x86_64-linux-ld \
        AR=x86_64-linux-ar \
        RANLIB=x86_64-linux-ranlib \
        CFLAGS="-DWOLFSSL_USER_IO -Os" \CPPFLAGS="-I./"
```

#### x86-64-core-i7-uclibc

```sh
./configure --host=x86_64-linux \
        CC=x86_64-linux-gcc LD=x86_64-linux-ld \
        AR=x86_64-linux-ar \
        RANLIB=x86_64-linux-ranlib \
        CFLAGS="-DWOLFSSL_USER_IO -Os" \
        CPPFLAGS="-I./"
```

## 移植版のビルド

wolfSSLは多くの環境やデバイスに移植されています。これらの移植版の一部と、それらに付随するドキュメントは、`wolfssl-X.X.X/IDE`ディレクトリに置かれています（X.X.Xは現在のwolfSSLバージョン番号）。このディレクトリには、各環境向けにwolfSSLをビルドするために使用するIDEに関する有用な情報とコードも含まれています。

移植版リスト:

* Arduino
* LPCXPRESSO
* Wiced Studio
* CSBench
* SGX Windows and Linux
  * これらのディレクトリ（`wolfssl/IDE/WIN-SGX`および`wolfssl/IDE/LINUX-SGX`）には、Intel SGXプロジェクトで使用するライブラリとしてwolfSSLをビルドするためのMakefileとVisual Studioソリューションが含まれています。
* Hexagon
  * このディレクトリ（`wolfssl/IDE/HEXAGON`）には、Hexagonツールチェーンでビルドするための Makefileが含まれています。ECC検証処理をDSPプロセッサへオフロードするためのwolfSSLのビルドに使用できます。このディレクトリには、ビルドに必要な手順を説明するREADMEファイルが含まれています。
* Hexiwear
* NetBurner M68K
  * このディレクトリ（`wolfssl/IDE/M68K`）には、Netburner RTOSを使用するMCF5441Xデバイス向けにwolfSSLをビルドするためのMakefileがあります。
* Renesas
  * このディレクトリ（`wolfssl/IDE/Renesas`）には、さまざまなRenesasデバイス向けの複数のビルドが含まれています。また、ハードウェアアクセラレーションの使用方法を示すサンプルビルドもあります。
* XCode
* Eclipse
* Espressif
* IAR-EWARM
* Kinetis Design Studio (KDS)
* Rowley Crossworks ARM
* OpenSTM32
* RISCV
* Zephyr
* Mynewt
* INTIME-RTOS

## NXP CAAM向けのビルド

### i.MX8 (Linux)

#### 既知の問題
- 開いているHSMキーストアセッションを、HSMセッションを閉じる（wc_SECO_CloseHSMおよびwolfSSL_CLeanupまたはwolfCrypt_Cleanup）前に終了してしまうと、次にNVMを起動した際にセグメンテーションフォルトが発生します。回避策として電源の再投入が必要です。

#### 確認されている制限事項
- 大きな入力（例: 1メガバイト）を伴うAES処理は、SECOが「Not enough space in shared memory」となって失敗します。
- キーストアを2つ作成した後、3つ目を作成しようとすると失敗します。キーストアをリセットするには、rm -rf /etc/seco_hsm を実行してデバイスの電源を再投入してください。

#### はじめに
i.MX8デバイスには、セキュリティ強化のためのSECOハードウェアモジュールが用意されています。このモジュールは、AES処理と鍵の保管、限定的なECC処理と鍵の保管を扱い、RNGを提供します。wolfSSLは、可能な箇所でSECOを利用できるように拡張されています。一部のアルゴリズムについては、i.MX8のCAAMはサポートしているものの、SECOモジュールがまだサポートしていない場合があります。このような場合、wolfSSLは/dev/cryptoを通じて、CAAMのジョブを直接生成するLinux CAAMドライバへの呼び出しを行います。NXPのLinux CAAMドライバでデフォルトでサポートされているアルゴリズムもありますが、CAAMがサポートするすべてのアルゴリズムが対象というわけではありません。wolfSSLは、追加のアルゴリズムのサポートを加えるためにLinux CAAMドライバを拡張しました。同一アプリケーションからCAAMへアクセスする両方の経路を使用するには、それぞれのコードパスに関連付けられた「devId」（WOLFSSL_CAAM_DEVIDまたはWOLFSSL_SECO_DEVIDのいずれか）を設定します。これらのIDは、構造体の初回初期化時に設定され、その構造体のライフタイム全体を通じてどちらのコードパスを使用するかを決定します。ソフトウェアのみを使用する場合は、デフォルトのINVALID_DEVIDを設定してください。ただし、キーストアを使用しないSECOの項目（TRNGおよびハッシュ処理）は例外です。


使用したソフトウェアのバージョン:

- imx-seco-libs ブランチ imx_5.4.24_2.1.0
- NXPの「repo」ツールおよびYoctoビルド。Yoctoのセットアップに関するドキュメントはこちら
- wolfSSL 5.2.0 +（5.2.0リリース後に開発）


#### サポートされているアルゴリズム
サポートされているアルゴリズム、モード、処理は以下のとおりです。

- AES (ECB, CBC, CCM, GCM)
- AES CMAC
- SHA256, SHA384
- ECC 256/384（鍵生成、署名、検証、ecdh）
- RSA 3072（鍵生成、署名、検証）
- HMAC
- Curve25519
- TRNG


#### イメージのビルド
##### 「repo」のセットアップ
NXPの「repo」コマンドツールのセットアップはUbuntu 18.04 LTS上で行いました\

```
sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib
sudo apt-get install build-essential chrpath socat cpio python python3 python3-pip
sudo apt-get install python3-pexpect xz-utils debianutils iputils-ping python3-git
sudo apt-get install python3-jinja2 libegl1-mesa libsdl1.2-dev pylint3 xterm curl
sudo apt-get install ca-certificates
```


```
mkdir ~/bin
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
export PATH=~/bin:$PATH

git config --global user.name "Your Git Name"
git config --global user.email "Your Email"
```


2022年1月11日以降、GitHubは認証されていないgit接続を許可しなくなりました。このドキュメントの作成時点（2022年3月）では、NXPのrepoツールはまだこれに対応していません。回避策として、以下のコマンドで git://github.com/ を https://github.com/ にリダイレクトしてください。


```
git config --global url."https://github.com/".insteadOf git://github.com/
```


ビルド用のディレクトリを作成します。例:

```
mkdir imx-yocto-bsp
cd imx-yocto-bsp/
```

NXPの「repo」コマンドツールをセットアップした後、目的のバージョンのLinuxでディレクトリを初期化・同期します。この例では5.4.24_2.1.0です。\

```
repo init -u https://source.codeaurora.org/external/imx/imx-manifest -b imx-linux-zeus -m imx-5.4.24-2.1.0.xml
repo sync

DISTRO=fsl-imx-wayland MACHINE=imx8qxpc0mek source imx-setup-release.sh -b build-xwayland
```

##### 追加のYocto CAAMレイヤー
次に、cryptodev-module、cryptodev-linux、および`linux-imx/drivers/crypto/caam/*`内のファイルにパッチを適用するCAAMドライバ拡張レイヤーをダウンロードします。ベースのblobとECDSA（署名/検証/鍵生成）はこちら（https://source.codeaurora.org/external/imxsupport/imx_sec_apps/）にあります。RSAブラックキー、ECDH、Curve25519サポートを持つようにさらに拡張するレイヤーがmeta-imx-expand-caamです。これら両方のディレクトリを、sourcesディレクトリ内の既存の他のmeta-\*ディレクトリの隣に配置してください。


```sh
#<assuming in the build-xwayland directory from previous command>

#<either clone the current work or open from a delivered zip>
git clone -b caam_expansion https://github.com/JacobBarthelmeh/imx_sec_apps
cp -r imx_sec_apps/meta-imx-ecdsa-sec ../sources/
cp -r imx_sec_apps/meta-imx-expand-caam ../sources/


# or

git clone https://source.codeaurora.org/external/imxsupport/imx_sec_apps.git
#(meta-imx-expand-caam comes from wolfSSL)
unzip meta-imx-expand-caam.zip

cp -r imx_sec_apps/meta-imx-ecdsa-sec ../sources/
mv meta-imx-expand-caam ../sources/
```


これらのレイヤーを、ecdsa、次にCAAM拡張の順でビルドに追加します。

```
vim conf/bblayers.conf
BBLAYERS += "${BSPDIR}/sources/meta-imx-ecdsa-sec"
BBLAYERS += "${BSPDIR}/sources/meta-imx-expand-caam"
```


必要なモジュールとライブラリをローカルのconfファイルに追加します。

```sh
vim conf/local.conf
EXTRA_IMAGE_FEATURES_append = " dev-pkgs tools-sdk tools-debug ssh-server-openssh "
IMAGE_INSTALL_append = " cryptodev-module cryptodev-linux eckey "
```


このビルドでは、デバッグツールとSSHサーバーを追加していますが、サイズを削減したい場合には不要です。重要なのは「cryptodev-moduleとcryptodev-linux」を追加することです。「eckey」は、blobのカプセル化とカプセル化解除を行うNXP提供のデモツールです。

[オプション]
cryptodevモジュールを自動ロードするには、conf/local.confに次の行を追加します。


```
KERNEL_MODULE_AUTOLOAD += "cryptodev"
```


これを行わない場合は、電源を再投入するたびに「modprobe cryptodev」を使用してモジュールをロードする必要があります。


##### ビルドとデプロイ
イメージのビルドを開始するには、以下のコマンドを使用します。sdcardを使用する場合は、その後カードに書き込みます。


```
bitbake core-image-base

cd tmp/deploy/images/imx8qxpc0mek/
bzcat core-image-base-imx8qxpc0mek.sdcard.bz2 | sudo dd of=/dev/diskX bs=5m
```


注記: 5mはMac OS用で、Linuxでは5Mを使用してください。diskXはsdcardの場所に置き換えます（例: Macではdisk2、LinuxではsdbX）。実行前にsdcardのディスク番号を必ず確認してください。
後でwolfssl/examplesをビルドする際に使用するため、以下のようにインストールディレクトリをエクスポートしておきます。

```
export　CRYPTODEV_DIR=`pwd`/tmp/sysroots-components/aarch64/cryptodev-linux/usr/include/
```


クロスコンパイル用のツールチェーンをインストールするには、以下のYoctoコマンドを使用します。

```
bitbake meta-toolchain
sudo ./tmp/deploy/sdk/<version>.sh
```

#### NXP HSMのビルド
##### zlibのビルド
これにはいくつかの方法があります。1つはYoctoビルドに追加する方法、もう1つは以下のようにbitbakeを使用してビルドする方法です。


```
cd build-xwayland
bitbake zlib
```


これにより、結果は tmp/sysroots-components/aarch64/zlib/usr/ ディレクトリに配置されます。

後でwolfssl/examplesをビルドする際に使用するため、以下のようにインストールディレクトリをエクスポートしておきます。

```
export ZLIB_DIR=`pwd`/tmp/sysroots-components/aarch64/zlib/usr/
```

##### NXP HSMライブラリのビルド
NXP HSMライブラリをダウンロードし、必要なzlibが見つかるようにMakefile（または環境変数）を調整します。

```sh
git clone https://github.com/NXP/imx-seco-libs.git
cd imx-seco-libs
git checkout imx_5.4.24_2.1.0
```


```sh
vim Makefile


CFLAGS = -O1 -Werror -fPIC -I$(ZLIB_DIR)/include -L$(ZLIB_DIR)/lib
```


次に

```
make
make install
```

後でwolfssl/examplesをビルドする際に使用するため、以下のようにインストールディレクトリをエクスポートしておきます。


```sh
export HSM_DIR=`pwd`/export/usr/
```

make installは、デフォルトで結果をサブディレクトリ「export」に配置します。


#### wolfSSLのビルド

##### Autoconfを使用したビルド

開発ツールを含めてYoctoイメージをセットアップした場合は、システム上で直接wolfSSLをビルドできます。より最小構成のアプローチとしては、クロスコンパイルを使用できます。デバッグメッセージは–enable-debugで有効化できます。SECO関連の作業に固有の追加デバッグメッセージはマクロDEBUG_SECOを、/dev/crypto呼び出しについてはDEBUG_DEVCRYPTOを定義することで有効化できます。どちらの追加デバッグメッセージもprintfを使用し、stdoutパイプに出力されます。
SECOで使用する主要な有効化オプションがいくつかあります。--enable-caam=seco、--enable-devcrypto=seco、--with-seco=/hsm-lib/export です。


HSM SECOのみ（追加アルゴリズム用のdevcryptoサポートなし）でのビルド例


```sh
source /opt/fsl-imx-wayland/5.4-zeus/environment-setup-aarch64-poky-linux

# Install dependencies for building wolfSSL
sudo apt-get install autoconf automake libtool

./autogen.sh
./configure --host=aarch64-poky-linux --with-libz=$ZLIB_DIR --with-seco=$HSM_DIR \ --enable-caam=seco --enable-cmac --enable-aesgcm --enable-aesccm --enable-keygen \
CPPFLAGS="-DHAVE_AES_ECB"
make
```

HSM SECOに加えてdevcryptoサポートを追加したビルド例。crypto/cryptodev.hへのインクルードパスを設定する必要があります。


```sh
./configure --host=aarch64-poky-linux --with-libz=$ZLIB_DIR --with-seco=$HSM_DIR \
--enable-caam=seco --enable-cmac --enable-aesgcm --enable-aesccm --enable-keygen \
CPPFLAGS="-DHAVE_AES_ECB -I$CRYPTODEV_DIR" --enable-devcrypto=seco \
--enable-curve25519
make
```


wolfCrypt_Init / wolfSSL_Init関数の呼び出しで早期にエラーを返すフェイルセーフが用意されています。1つは、cryptodevモジュールがロードされていない、または目的の処理に対するサポートが利用できない場合です。もう1つ、init処理が失敗するケースは、NXP HSMをセットアップできなかった場合です。アプリケーションが初期化で失敗する場合は、wolfSSLのビルドに--enable-debugを追加し、wolfSSLの初期化の前に関数呼び出しwolfSSL_Debugging_ON()を行うことで、失敗の原因に関する有用なデバッグメッセージが出力されます。


デバッグオプションを有効にしたビルド例


```sh
./configure --host=aarch64-poky-linux --with-libz=$ZLIB_DIR --with-seco=$HSM_DIR \
--enable-caam=seco --enable-cmac --enable-aesgcm --enable-aesccm --enable-keygen \
CPPFLAGS="-DHAVE_AES_ECB -I$CRYPTODEV_DIR -DDEBUG_SECO -DDEBUG_DEVCRYPTO" \
--enable-devcrypto=seco --enable-curve25519
```

##### user_settings.hを使用したビルド

autotoolsを使用せずにビルドする場合に有効化できるマクロは以下のとおりです。

***CAAM***

- WOLFSSL_CAAM - CAAMサポートを有効化するメインのマクロスイッチ。
- WOLF_CRYPTO_CB - CAAMサポートはcryptoコールバックを利用します
- WOLFSSL_SECO_CAAM - CAAMでのSECO HSMの使用を有効化（AES-GCMが必須であり、平文鍵をHSMへインポートする際のアルゴリズムとして使用されます）。
- WOLFSSL_HASH_KEEP - SHA256などのアルゴリズムでハッシュする際、メッセージを蓄積しておき、Finalの呼び出し時にのみハッシュ処理に送ります。
- WOLFSSL_CAAM_ECC - CAAM ECCサポートを有効化。
- WOLFSSL_CAAM_CMAC - CAAM CMACサポートを有効化。
- WOLFSSL_CAAM_CIPHER - CAAM AESサポートを有効化。
- WOLFSSL_CAAM_HMAC - CAAM HMACサポートを有効化。
- WOLFSSL_CAAM_HASH - SHA256などのCAAMハッシュサポートを有効化。
- WOLFSSL_CAAM_CURVE25519 - CAAM Curve25519サポートを有効化。


***cryptodev-linux***

- WOLFSSL_DEVCRYPTO - cryptodev-linuxの使用を有効化するメインのマクロスイッチ。
- WOLFSSL_DEVCRYPTO_HMAC - cryptodev-linuxでのHMACサポートを有効化。
- WOLFSSL_DEVCRYPTO_RSA - cryptodev-linuxでのRSAサポートを有効化。
- WOLFSSL_DEVCRYPTO_CURVE25519 - cryptodev-linuxでのCurve25519サポートを有効化。
- WOLFSSL_DEVCRYPTO_ECDSA - cryptodev-linuxでのECDSAサポートを有効化。
- WOLFSSL_DEVCRYPTO_HASH_KEEP - cryptodev-linuxでのハッシュ蓄積のサポートを有効化。


***CAAMサポートのために追加でコンパイルが必要なファイルは以下のとおりです:***


- wolfssl/wolfcrypt/src/port/caam/wolfcaam_aes.c
- wolfssl/wolfcrypt/src/port/caam/wolfcaam_cmac.c
- wolfssl/wolfcrypt/src/port/caam/wolfcaam_rsa.c
- wolfssl/wolfcrypt/src/port/caam/wolfcaam_ecdsa.c
- wolfssl/wolfcrypt/src/port/caam/wolfcaam_x25519.c
- wolfssl/wolfcrypt/src/port/caam/wolfcaam_hash.c
- wolfssl/wolfcrypt/src/port/caam/wolfcaam_hmac.c
- wolfssl/wolfcrypt/src/port/caam/wolfcaam_init.c
- wolfssl/wolfcrypt/src/port/caam/wolfcaam_seco.c
- wolfssl/wolfcrypt/src/port/devcrypto/devcrypto_ecdsa.c
- wolfssl/wolfcrypt/src/port/devcrypto/devcrypto_x25519.c
- wolfssl/wolfcrypt/src/port/devcrypto/devcrypto_rsa.c
- wolfssl/wolfcrypt/src/port/devcrypto/devcrypto_hmac.c
- wolfssl/wolfcrypt/src/port/devcrypto/devcrypto_hash.c
- wolfssl/wolfcrypt/src/port/devcrypto/devcrypto_aes.c
- wolfssl/wolfcrypt/src/port/devcrypto/wc_devcrypto.c
- wolfssl/wolfcrypt/src/cryptocb.c

#### サンプル

##### Testwolfcryptの実行

wolfSSLに同梱されている単体テストは、wolfcrypt/test/test.cにあります。デバイス上でテストをビルドして実行する例を以下に示します。これはWOLFSSL_CAAM_DEVIDを使用しているため、NXP HSMライブラリではなくcryptodevモジュールを利用している点に注意してください。


```
./configure --host=aarch64-poky-linux --with-libz=$ZLIB_DIR \
--with-seco=$HSM_DIR --enable-caam=seco --enable-cmac --enable-aesgcm \
--enable-aesccm --enable-keygen CPPFLAGS="-DHAVE_AES_ECB -I$CRYPTODEV_DIR" \
--enable-devcrypto=seco --enable-curve25519 --enable-sha224 --enable-static \
--disable-shared --disable-filesystem
make

scp wolfcrypt/test/testwolfcrypt root@192.168.0.14:/tmp
ssh root@192.168.0.14
root@imx8qxpc0mek:~# /tmp/testwolfcrypt
------------------------------------------------------------------------------
 wolfSSL version 5.2.0
------------------------------------------------------------------------------
error	test passed!
MEMORY   test passed!
base64   test passed!
asn  	test passed!
RANDOM   test passed!
MD5  	test passed!
SHA  	test passed!
SHA-224  test passed!
SHA-256  test passed!
SHA-384  test passed!
SHA-512  test passed!
SHA-3	test passed!
Hash 	test passed!
HMAC-MD5 test passed!
HMAC-SHA test passed!
HMAC-SHA224 test passed!
HMAC-SHA256 test passed!
HMAC-SHA384 test passed!
HMAC-SHA512 test passed!
HMAC-SHA3   test passed!
HMAC-KDF	test passed!
GMAC 	test passed!
Chacha   test passed!
POLY1305 test passed!
ChaCha20-Poly1305 AEAD test passed!
AES  	test passed!
AES192   test passed!
AES256   test passed!
AES-GCM  test passed!
AES-CCM  test passed!
RSA  	test passed!
DH   	test passed!
PWDBASED test passed!
ECC  	test passed!
ECC buffer test passed!
CURVE25519 test passed!
CMAC 	test passed!
COMPRESS test passed!
logging  test passed!
time test passed!
mutex	test passed!
memcb	test passed!
crypto callback test passed!
Test complete
Exiting main with return code: 0
root@imx8qxpc0mek:~#
```


その他のサンプルは、wolfssl-examplesリポジトリのcaam/secoディレクトリにあります。


```sh
git clone https://github.com/wolfssl/wolfssl-examples
cd wolfssl-examples/caam/seco
make
```

##### ソースコードのコンパイル [user_settings.hを使用]

wolfSSLにリンクする単一のソースファイルをビルドするには、以下のコマンドを使用できます。前の手順で設定した環境変数がまだ有効であることを前提としています。


```
source /opt/fsl-imx-xwayland/5.4-zeus/environment-setup-aarch64-poky-linux

$CC -DWOLFSSL_USER_SETTINGS -I /path/to/user_settings.h \
-I $CRYPTODEV_DIR -I $HSM_DIR/include -I ./wolfssl server-dtls.c \
libwolfssl.a $HSM_DIR/lib/hsm_lib.a $HSM_DIR/lib/seco_nvm_manager.a \
$ZLIB_DIR/lib/libz.a -lpthread -lm
```

#### API

##### 追加されたAPI

***追加されたAPIの一覧***

- void wc_SECO_AesSetKeyID(Aes* aes, int keyId);
この関数は、SECOキーIDをAes構造体に設定するために使用されます。Aes構造体が初期化された後、構造体が暗号化/復号操作に使用される前に呼び出す必要があります。
- int wc_SECO_AesGetKeyID(Aes* aes);
Aes構造体に設定されたSECOキーIDのゲッター関数です。
- void wc_SECO_CMACSetKeyID(Cmac* cmac, int keyId);
wc_SECO_AesSetKeyIDと同様ですが、Cmac構造体用です。
- int wc_SECO_CMACGetKeyID(Cmac* cmac);
Cmac構造体に設定されたSECOキーIDのゲッター関数です。
- int wc_SECO_OpenHSM(word32 keyStoreId, word32 nonce, word16 maxUpdates, byte flag);
この関数は、キーストアを必要とする操作（ECCやAES操作など）を行う前に呼び出す必要があります。最初の引数はキーストアIDです。「nonce」は、既存のキーストアの作成およびアンロックの際に使用される特定の32ビットのシーケンスです。「maxUpdates」はキーストアを更新できる最大回数を設定します。「flag」はオプションのフラグに使用され、キーストアを作成するかどうかを受け取ります。HSMキーストアを作成するにはフラグをCAAM_KEYSTORE_CREATEにし、そうでなく既存のキーストアを開くか更新する場合はフラグをCAAM_KEYSTORE_UPDATEにする必要があります。
- int wc_SECO_CloseHSM(void);
この関数は、キーストアの使用を終えたとき、wolfCrypt_Cleanup/wolfSSL_Cleanupを呼び出す前に呼び出す必要があります。現在開いているキーストアを閉じます。
- int wc_SECO_GenerateKey(int flags, int group, byte* out, int outSz, int keyType, int keyInfo, unsigned int* keyIdOut);\
この関数は、SECO内で新しい鍵を生成するために使用できます。鍵生成の場合、フラグはCAAM_GENERATE_KEYにする必要があります。鍵を更新する場合、フラグはCAAM_UPDATE_KEYにする必要があります。keyIdOut引数は入出力引数であり、鍵の作成時に値が設定され、鍵の更新時には入力として設定する必要があります。鍵を更新する場合、それはtransient（一時的）タイプである必要があり、更新にはgroupを0に設定する必要があります。transientタイプはkeyInfo引数として設定します。keyInfoとKeyTypesの選択可能なオプションは次のとおりです。
    - CAAM_KEY_TRANSIENT (keyInfo)
    - CAAM_KEY_PERSISTENT (keyInfo)
    - CAAM_KEYTYPE_ECDSA_P256 (keyType)
    - CAAM_KEYTYPE_ECDSA_P384 (keyType)
    - CAAM_KEYTYPE_AES128 (keyType)
- int wc_SECO_DeleteKey(unsigned int keyId, int group, int keyTypeIn);
キーストアから鍵を削除するために使用されます。

##### CAAMサポートを持つネイティブwolfSSL API

これは、本ドキュメントで説明したSECOビルドでCAAMサポートを持つようになったネイティブwolfSSL APIの一覧です。

任意のAES暗号化および復号操作の鍵生成には、以下のプロセスで鍵を生成できます。wc_SECO_GenerateKey(CAAM_GENERATE_KEY, groupID, pubOut, 0, CAAM_KEYTPE_AES128, CAAM_KEY_PERSISTENT, &keyIdOut); を使用します。ここでgroupIDは指定するグループ番号、pubOutは32バイトのバッファであり、変数keyIdOutには生成された新しいキーIDが設定されます。生成されたこの新しいキーIDは、wc_SECO_AesSetKeyID(Aes, keyIdOut); を使用してAes構造体に設定できます。キーIDが構造体に設定され、Aes構造体がWOLFSSL_SECO_DEVIDタイプとして初期化されると、すべての暗号化および復号操作でそのキーIDが使用されます。

###### ***AES (ECB/CBC)***

代替手段として、AES ECB/CBC鍵を生成するには、Aes構造体がWOLFSSL_SECO_DEVIDで初期化されている場合、平文の鍵を渡してwc_AesSetKey関数を呼び出すことができます。API wc_AesSetKeyは、一意のKEKを使用して鍵の暗号化を試み、SECO HSMにインポートします。インポートに成功すると、値0が返され、キーIDがAes構造体に設定されます。


- CBC暗号化はwc_AesCbcEncryptで、復号はwc_AesCbcDecryptで行います。
- ECB暗号化はwc_AesEcbEncryptで、復号はwc_AesEcbDecryptで行います。


Aes構造体の使用を終えたら、wc_AesFree(Aes); を使用して解放する必要があります。

###### ***AES-GCM***

- GCM暗号化はwc_AesGcmEncryptで、復号はwc_AesGcmDecryptで行います。


AES-GCM暗号化関数は、Aes構造体、出力バッファ、入力バッファ、入力バッファサイズ、ノンス、ノンスサイズ（12バイトである必要があります）、MAC（タグとも呼ばれます）、タグサイズ（16バイトである必要があります）、追加データ、追加データサイズ（4バイト）を受け取ります。暗号化時には、入力バッファが暗号化され、タグバッファには作成されたMACが格納されます。

AES-GCM復号の場合、この関数は、Aes構造体、平文出力バッファ、暗号文入力バッファ、入力バッファサイズ、ノンス、ノンスサイズ（12バイト）、暗号化呼び出しで以前に作成されたタグ、タグバッファサイズ、追加データ、追加データサイズを受け取ります。復号時には、メッセージの完全性を検証するためにタグバッファが確認されます。

Aes構造体の使用を終えたら、wc_AesFree(Aes); を使用して解放する必要があります。

###### ***AES-CCM***

- CCM暗号化はwc_AesCcmEncryptで、復号はwc_AesCcmDecryptで行います。


AES-CCM暗号化関数は、Aes構造体、出力バッファ、入力バッファ、入力バッファサイズ、ノンス、ノンスサイズ（12バイトである必要があります）、MAC（タグとも呼ばれます）、タグサイズ（16バイトである必要があります）、追加データ、追加データサイズ（0バイト）を受け取ります。NXP HSMライブラリでは、追加データバッファはNULLとし、サイズは0である必要があります。暗号化時には、入力バッファが暗号化され、タグバッファには作成されたMACが格納されます。
\
AES-CCM復号の場合、この関数は、Aes構造体、平文出力バッファ、暗号文入力バッファ、入力バッファサイズ、ノンス、ノンスサイズ（12バイト）、暗号化呼び出しで以前に作成されたタグ、タグバッファサイズ、追加データ、追加データサイズを受け取ります。暗号化関数と同様に、追加データバッファはNULLである必要があります。復号時には、メッセージの完全性を検証するためにタグバッファが確認されます。
\
Aes構造体の使用を終えたら、wc_AesFree(Aes); を使用して解放する必要があります。
\

###### ***AES CMAC***

AES CMAC操作の場合、AES鍵はwc_SECO_GenerateKey(CAAM_GENERATE_KEY, groupID, pubOut, 0, CAAM_KEYTPE_AES128, CAAM_KEY_PERSISTENT, &keyIdOut); を使用して生成できます。ここでgroupIDは指定するグループ番号、pubOutは32バイトのバッファであり、変数keyIdOutには生成された新しいキーIDが設定されます。生成されたこの新しいキーIDは、wc_SECO_CMACSetKeyID(Cmac, keyIdOut); を使用してAes構造体に設定できます。キーIDが構造体に設定され、Aes構造体がWOLFSSL_SECO_DEVIDタイプとして初期化されると、すべての暗号化および復号操作でそのキーIDが使用されます。

HSMライブラリはシングルショットタイプであるため、wc_CmacUpdateの各呼び出しは入力を内部バッファに蓄積します。その後、wc_CmacFinalが呼び出されると、バッファ全体がMAC作成のためにハードウェアに渡されます。


###### ***RSA***

RSA操作はcryptodev-linuxモジュールを利用します。これには、AES-ECBで暗号化されたブラック秘密鍵のサポートが含まれます。WOLFSSL_CAAM_DEVIDで初期化した場合、これがデフォルトです。

cryptodev-linuxモジュールとともに使用されるネイティブwolfSSL APIの例は次のとおりです。


```lang-c
wc_InitRsaKey_ex(key, heap-hint (can be NULL), WOLFSSL_CAAM_DEVID);
wc_MakeRsaKey(key, 3072, WC_RSA_EXPONENT, &rng);
wc_RsaSSL_Sign or wc_RsaPublicEncrypt
wc_RsaSSL_Verify or wc_RsaPrivateDecrypt
wc_FreeRsaKey(key)
```

###### ***ECC***

ECCの署名および検証操作は、cryptodev-linuxモジュールまたはNXP HSMライブラリのいずれかを使用できます。共有秘密を作成するためのECDH操作は、cryptodev-linuxモジュールでのみ実行できます。

SECOとともに使用する（NXP HSMライブラリを使用する）場合、ecc_key構造体を初期化する際にデバイスIDフラグWOLFSSL_SECO_DEVIDを使用する必要があります。cryptodev-linuxモジュールとともに使用する場合は、デバイスIDフラグWOLFSSL_CAAM_DEVIDを使用する必要があります。関数wc_ecc_init_ec(key, heap-hint (can be NULL), dev ID); による初期化の後は、どちらのユースケースも署名および検証に同じネイティブwolfSSL関数呼び出しを使用します。

ecc_key構造体の初期化後の関数呼び出しの例は次のとおりです。


> ```lang-c
> wc_ecc_make_key(&rng, ECC_P256_KEYSIZE, key);
> wc_ecc_sign_hash(hash, hashSz, sigOut, sigOutSz, &rng, key);
> wc_ecc_verify_hash(sig, sigSz, hash, hashSz, &result, key);
> ```


また、cryptodev-linuxモジュール（WOLFSSL_CAAM_DEVID）の場合、ECDH関数を使用できます。


```lang-c
wc_ecc_shared_secret(keyA, keyB, sharedSecret, sharedSecretSz);
```

###### ***ハッシュ (Sha256, Sha384, HMAC)***

SHA256およびSHA384操作はNXP HSMライブラリを使用します。HMAC操作はcryptodev-linuxモジュールを利用します。

デフォルトでは、SHA操作はNXP HSMライブラリの利用を試みますが、デバイスIDのWOLFSSL_SECO_DEVIDへの明示的な設定も使用できます。


```lang-c
wc_InitSha256_ex(sha256, heap-hint, WOLFSSL_SECO_DEVID);
wc_InitSha384_ex(sha384, heap-hint, WOLFSSL_SECO_DEVID);
```

NXP HSMライブラリはハッシュのシングルショット操作をサポートしているため、「update」の各呼び出しは「final」関数が呼び出されるまでバッファを蓄積し、その後、ハッシュダイジェスト作成のためにバッファ全体をハードウェアに渡します。

HMACがcryptodev-linuxを利用する場合、Hmac構造体はデバイスIDのWOLFSSL_CAAM_DEVIDを使用して初期化する必要があります。


```lang-c
wc_HmacInit(hmac, heap-hint, WOLFSSL_CAAM_DEVID);
```


その後は、ネイティブwolfSSL APIで通常行うのと同様に使用できます。

```lang-c
wc_HmacSetKey(hmac, hash-type, key, keySz);
wc_HmacUpdate(hmac, input, inputSz);
wc_HmacFinal(hmac, digestOut);
```

###### ***Curve25519***

Curve25519の点乗算はcryptodev-linuxモジュールを使用して行われ、ハードウェアで使用するにはデバイスIDのWOLFSSL_CAAM_DEVIDで初期化する必要があります。

API呼び出しの例は次のとおりです。


```lang-c
wc_curve25519_init_ex(key, heap-hint, WOLFSSL_CAAM_DEVID);
wc_curve25519_make_key(&rng, CURVE25519_KEYSIZE,  key);
wc_curve25519_shared_secret(key, keyB, sharedSecretOut, sharedSecretOutSz);
```

###### ***RNG***

wolfSSL HASH-DRBGのシードに用いるTRNGは、NXP HSMライブラリを利用します。これは、wolfSSLが--enable-caam=secoでビルドされたときにwolfcrypt/src/random.cファイルにコンパイルされます。wolfSSLでのすべてのRNG初期化は、シードにTRNGを利用します。標準的なRNG API呼び出しは次のとおりです。


```lang-c
wc_InitRng(rng);
wc_RNG_GenerateBlock(rng, output, outputSz);
wc_FreeRng(rng);
```

### i.MX8 (QNX)

(ドキュメントは現在準備中です。ご入用でしたら、 [info@wolfssl.jp](mailto:info@wolfssl.jp) までお問い合わせください。)

### i.MX6 (QNX)

(ドキュメントは現在準備中です。ご入用でしたら、 [info@wolfssl.jp](mailto:info@wolfssl.jp) までお問い合わせください。)

### IMXRT1170 (FreeRTOS)

IMXRT1170で使用するIDEセットアップの例は、ディレクトリIDE/MCUEXPRESSO/RT1170にあります。


#### ビルド手順

- MCUEXPRESSOを開き、ワークスペースをwolfssl/IDE/MCUEXPRESSO/RT1170に設定します
- File -> Open Projects From File System... -> Directory : でブラウズ先をwolfssl/IDE/MCUEXPRESSO/RT1170ディレクトリに設定し、「select directory」をクリックします
- wolfssl_cm7、wolfcrypt_test_cm7、CSR_example、PKCS7_exampleを選択します
- プロジェクトを右クリック -> SDK Management -> Refresh SDK Componentsを選択し、「yes」をクリックします
- FreeRTOSConfig.hのconfigTOTAL_HEAP_SIZEのサイズを、CSRおよびPKCS7の例では60240、wolfcrypt_test_cm7では約100000に増やします
- （注: boardファイルの再作成が必要です .... これは、同じ設定を持つ新しいプロジェクトを作成し、生成されたboard/*ファイルをコピーすることで行えます）
- プロジェクトをビルドします


#### RT1170 CAAMドライバの拡張

ファイルRT1170/fsl_caam_h.patchおよびRT1170/fsl_caam_c.patchには、Blobの作成/オープン、およびECCブラックキーの生成と使用のための、既存のNXP CAAMドライバへの変更が含まれています。

パッチを適用するには、まずcaamドライバを持つプロジェクトを作成します。これにより、driversディレクトリにベースとなるfsl_caam.cとfsl_caam.hが生成されます。（すなわち PKCS7_example_cm7/drivers/fls_caam.{c,h}）
。ベースファイルが生成されたら、driversディレクトリに「cd」してパッチを適用します
（cd PKCS7_example_cm7/drivers/ && patch -p1 < ../../fsl_caam_c.patch && patch -p1 < ../../fsl_caam_h.patch）

fsl_caam.hのパッチには、ECCとBlobの両方の拡張用のマクロが定義されています（CAAM_ECC_EXPANSIONおよびCAAM_BLOB_EXPANSION）。wolfSSLのコードは、これらのマクロが定義されている（パッチが適用されている）ことを検出すると、拡張ドライバの使用をコンパイルに含めようとします。

#### WOLFSSL_HAVE_ERROR_QUEUE

`ERR_get_error()` および関連関数を介してエラー情報を保存・取得するための、OpenSSL互換のエラーキューを有効化します。

#### WOLFSSL_HAVE_CERT_SERVICE

TLSハンドシェイク中のカスタム証明書処理のための証明書サービスコールバックを有効化します。


#### WOLFSSL_HEAP_TEST

wolfSSLにおけるメモリ割り当て動作を検証するためのヒープ関連のテストユーティリティを有効化します。

#### WOLFSSL_NO_OPENSSL_RAND_CB

OpenSSL RANDコールバック互換機能を無効化します。OpenSSLスタイルの `RAND_set_rand_method()` コールバックによってRNGが上書きされることを防ぎます。


#### WOLFSSL_NO_REALLOC

`realloc()` の使用を無効化します。すべてのバッファのサイズ変更は、代わりに `malloc()` + `memcpy()` + `free()` によって行われます。


#### WOLFSSL_DEBUG_DTLS

再送、エポック管理、レコード処理を含むDTLS固有の操作のデバッグログを有効化します。

