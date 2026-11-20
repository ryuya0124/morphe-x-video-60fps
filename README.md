# X Video Upload 60 FPS

Android版Xの従来の動画エンコーダーで、入力のフレームレートを最大60fpsまで維持するMorphe用パッチです。入力が24fpsや30fpsなら、そのフレームレートで処理します。

[Morphe Managerに追加](https://morphe.software/add-source?github=ryuya0124/morphe-x-video-60fps&name=X%20Video%20Upload%2060%20FPS&app=com.twitter.android) · [GitHub Releasesから .mpp をダウンロード](https://github.com/ryuya0124/morphe-x-video-60fps/releases/latest)

## Morphe ManagerでPikoと併用

1. 上のリンクで追加するか、**Sources → ＋ → Remote** に `https://github.com/ryuya0124/morphe-x-video-60fps` を入力します。ダウンロード済みの `.mpp` は **Local** から追加できます。
2. **Expert mode** を有効にして、対象バージョンのオリジナルAPKまたはAPKバンドルを選びます。
3. **Piko** のタブで使いたいパッチを選び、**X Video Upload 60 FPS** のタブで **X video upload up to 60 FPS** を有効にします。
4. **Patch** を実行し、通常のAPKインストールで入れます。既存のPiko版に上書きする場合は、同じMorpheキーストアとパッケージ名を使います。

[Morpheのパッチソースの説明](https://github.com/MorpheApp/morphe-manager/blob/main/docs/patch-sources.md) · [Expert modeの説明](https://github.com/MorpheApp/morphe-manager/blob/main/docs/patching-expert-mode.md)

| X | 元APKのバージョンコード | 併用を確認したPiko | 60fps素材の再エンコード |
|---|---:|---|---|
| `12.11.0-release.0` | `312110000` | `3.9.0-dev.1` | 60fps、180フレーム |
| `12.17.0-release.0` | `312170000` | `3.9.0-dev.8` | 60fps、180フレーム |
| `12.19.1-release.0` | `312191000` | `3.11.1` | 60fps、180フレーム |

旧版Pikoは対応するリリースの `.mpp` を取得し、ローカルソースとして追加できます。 [Piko Releases](https://github.com/crimera/piko/releases)

## 実装と検証

対象は表の3組に限定しています。パッケージ名、バージョン名、バージョンコードを適用前に照合し、Morpheの `--force` を使っても対象外なら中止します。新しいバージョンは、Pikoとの同時適用と再エンコードの実測を終えてから追加します。

従来のエンコーダーが対象フレームレートの既定値30fpsを読み出す箇所を目印に、エンコーダーへ渡す上限と、間引きを開始する上限を60fpsにします。入力フレームレートのメタデータが取得できない場合は、従来の設定値を代替値として使います。対象の命令列を一意に確認できない場合もパッチ適用を中止します。

Morphe Manager 2.6.0でX `12.19.1-release.0` に本パッチとPiko 3.11.1を同時に適用できることを確認しました。表の3組はMorphe Desktop 2.2.0でもパッチ・署名し、Android 16のARM64エミュレーターに通常インストールしました。

各バージョンのエンコーダーに3秒のHEVC素材を入力し、H.264出力を `ffprobe` で検査しました。X `12.19.1-release.0` では、Pikoのみの場合に60fps入力が30fps・90フレームになり、併用時は60fps・180フレームを維持しました。3バージョンとも、24fpsは72フレーム、30fpsは90フレームを維持しました。

新UI版の `12.40.1-prod.01` と `12.41.0-prod.01` は、既存の別のエンコード経路で入力に応じて最大60fpsを選ぶことをAPKのコードで確認しました。今回のパッチの対応範囲は表の3バージョンです。Galaxyでの動作と、投稿後にXが配信する動画のfpsは未測定です。

## ビルド

JDK 21、Kotlin CLI 2.4.21、Android Build Tools 36.0.0、Morphe Desktop 2.2.0のall JARでビルドできます。

```sh
./scripts/build.sh /path/to/morphe-desktop-2.2.0-all.jar /path/to/android/build-tools/36.0.0
```

ビルド結果は `dist/` に出力されます。GitHub Actionsでも同じスクリプトを使い、配布ファイルのSHA-256は各リリースの `SHA256SUMS` で確認できます。

ライセンスは [GPL-3.0-only](LICENSE) です。
