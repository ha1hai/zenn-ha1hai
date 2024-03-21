---
title: "Astro ブログを Cloudflare CDN にデプロイする"
emoji: "😊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [cloudfalre]
published: false
---

Astro は、ブログやマーケティング、eコマースなど、コンテンツ駆動のウェブサイトを作成するためのウェブフレームワークです。
Cloudflare に Astro フレームワークを使ったブログをデプロイします。

Astro Docs の [AstroサイトをCloudflare Pagesにデプロイする](<https://docs.astro.build/ja/guides/deploy/cloudflare/>) に以下3つの方法が紹介されていたが、今回は、Cloudflare Pagesダッシュボードからデプロイする方法で行います。

1. Cloudflare Pagesダッシュボードからデプロイする方法
2. CloudflareのCLIであるWranglerを使ったデプロイ方法
3. SSRサイトを@astrojs/cloudflareを使ってデプロイする方法

## 事前準備

- Github に Astro ブログのリポジトリを準備しておく

## Cloudflere Pages に Git リポジトリを登録する

Astro Blog を登録した Git リポジトリを登録する

- Workers & Pages -> Pages -> Git に接続
- GitHub に接続 -> 認証ユーザーを選択(ログインが必要) -> 対象のリポジトリを選択 -> Install & Authorize

## Cloudflere Pages のアプリケーションを作成する

- リポジトリを選択 -> セットアップの開始
- ビルドとデプロイのセットアップの入力
  - プロジェクト名: `ha1hai-blog`
  - プロダクション ブランチ: `main`
  - ビルド設定
    - フレームワークプリセット: `astro`
    - ビルドコマンド: `rpm run build`
    - ビルド出力ディレクトリ: `dict`
- 保存してデプロイする

## サイトを確認する

- デプロイ完了後に、プロジェクトに進む
- ドメインを確認: `ha1hai-blog.pages.dev`
  - ex: {project-name}.pages.dev
  - <https://ha1hai-blog.pages.dev>
- URLにアクセスにアクセス可能なことを確認する

## 参考URL

- [AstroサイトをCloudflare Pagesにデプロイする](<https://docs.astro.build/ja/guides/deploy/cloudflare/>)
