# キーマップ同期（同期先）

`config/roBa.keymap` は **自動生成ファイル**。直接編集しないこと。導入日 2026-10-28。

マスターは **[dgarai/zmk-config-moNa2-v2](https://github.com/dgarai/zmk-config-moNa2-v2)** の
`config/mona2.keymap`。あちらが更新されると、マスター側の
`.github/workflows/sync-keymap.yml` がこのリポジトリへ push し、その push で
このリポジトリの `build.yml`（ファームウェア）と draw ワークフロー（キーマップ図）が走る。

## キーマップを変えたいとき

**マスターを編集する。** このリポジトリを keymap-editor で開いて SAVE しても、
次の同期で上書きされて消える。keymap-editor にはマスターだけを登録しておくこと。

## 変換の内容

roBa は 43 キーで、マスターの moNa2（42キー）とは 2段目の左内側キー 1 個分だけ違う。
同期時に以下の変換がかかる。

- 各レイヤーの 2段目に 1 個キーを補う。default レイヤーは `&kp LS(LG(S))`
  （macOS スクリーンショット）、NUM レイヤーは `&kp LC(LA(KP_NUMBER_0))`、
  残りは `&trans`。変更したいときはマスターの `tools/sync-keymap.py` の
  `ROBA_EXTRA_PER_LAYER` を編集する
- コンボの `key-positions` のうち 15 以降の値を +1 ずらす
- `#include` や `&trackball { scroll-layers }` などのデバイス固有ヘッダは
  このリポジトリの既存のものを温存する（マスターのものは使わない）
- マスターにある GESTURE レイヤー（layer 8）が追加される。マスターの default
  レイヤーが `&lt 8 MINUS` / `&lt 8 TAB` で layer 8 を参照しているため必要

`config/roBa.json`（keymap-editor が読むレイアウト定義）と `config/west.yml`、
`boards/` は同期対象外。

なお `.github/workflows/draw.yml` の push トリガーを同期導入時に有効化した
（元はコメントアウトされていた）。同期後にキーマップ図が自動更新されるようにするため。

## 元に戻す

### 一時的に同期を止める

このリポジトリ側では止められない。マスターの Actions タブ →
"Sync keymap to other boards" → `…` → Disable workflow。

このリポジトリ側だけで断ち切るなら、Settings → Deploy keys から
`keymap-sync roBa` のデプロイキーを削除する。以降マスターからの push は
権限エラーで失敗する（マスター側の同期ジョブが赤くなるので気付ける）。

### 同期導入前のキーマップに戻す

導入直前のコミットは `3f5865996afc1c2b912daad2ad277f5471bf6fc3`。

```sh
git fetch origin
git checkout main
git checkout 3f5865996afc1c2b912daad2ad277f5471bf6fc3 -- config/roBa.keymap
git rm KEYMAP-SYNC.md
git commit -m "Revert keymap sync" && git push
```

マスター側の同期を止めずにこれをやると、次の同期で再び上書きされる。
先に上の「一時的に同期を止める」を行うこと。

### 完全に撤去する

上記に加えて、マスター側の `KEYMAP-SYNC.md` の「同期の仕組みを完全に撤去する」を実施する。
