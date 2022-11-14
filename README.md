# sample-gitlab-npm-package

## 概要

GitLab Package Registryにnpmパッケージを公開するサンプルコード。

## GitLab Package Registryにnpmパッケージを公開する手順

1. npmパッケージを開発する
   * 本リポジトリでは`index.js`をnpmパッケージとして作成している。
1. npmパッケージ公開用のアクセストークンを発行する
   * 状況に合わせて以下のいずれかの方法でアクセストークンを発行する。
     * Personal Access Token: GitLabユーザーに対して発行するアクセストークン
     * Deploy Token: グループやプロジェクトに対して発行するアクセストークン
     * CI Job Token: GitLab CI/CDから利用するアクセストークンで、GitLabが自動で発行するアクセストークン
   * 詳細な作成方法は以下のサイト等を参考にする。
     * [Gitlab Package Registryを利用してプライベートNPMパッケージを運用する - トークンの作成](https://zenn.dev/hytkgami/articles/private-npm-package-with-gitlab-package-registry#%E3%83%88%E3%83%BC%E3%82%AF%E3%83%B3%E3%81%AE%E4%BD%9C%E6%88%90)
1. `package.json`の修正
   * 任意のnpmのスコープをnameフィールドに追記する。
     * 通常はnpmパッケージを管理しているプロジェクトが属しているルートグループの名前をスコープとして設定する。
   * publishConfig.registryフィールドにnpmパッケージの公開先となるGitLabプロジェクトのnpmレジストリのURLを指定する。
     * 具体的には以下の形式の文字列を設定する。
       ```
       http://gitlab.example.com/api/v4/projects/${GITLAB_PROJECT_ID}/packages/npm/
       ```
     * 上記文字列に対して、以下の修正をする。
       * `gitlab.example.com`の部分をGitLabのホスト名に置き換える。
       * `${GITLAB_PROJECT_ID}`の部分をnpmパッケージを管理しているGitLabのプロジェクトのIDに置き換える。
1. npmレジストリの認証時にアクセストークンを利用するよう設定
   * 以下のコマンドを実行して、事前に発行しておいたアクセストークンをnpmレジストリの認証時に利用するよう設定する。
     ```bash
     npm config set -- //gitlab.example.com/api/v4/projects/${GITLAB_PROJECT_ID}/packages/npm/:_authToken ${GITLAB_ACCESS_TOKEN}
     ```
   * 上記文字列に対して、以下の修正をする。
     * `gitlab.example.com`の部分をGitLabのホスト名に置き換える。
     * `${GITLAB_PROJECT_ID}`の部分をnpmパッケージを管理しているGitLabのプロジェクトのIDに置き換える。
     * `${GITLAB_ACCESS_TOKEN}`の部分は事前に発行したアクセストークンに置き換える。
1. npmパッケージの公開
   * 以下のコマンドを実行することで、npmパッケージをGitLab上に公開する。
     ```bash
     npm publish
     ```

## GitLab Package Registry上のnpmパッケージをインストールする手順

1. npmパッケージインストール用のアクセストークンを発行する
   * 発行手順はnpmパッケージ公開用アクセストークンのときと同様。
   * ただし、npmパッケージインストール用の場合はnpmレジストリの参照権限が付与されていればOK。
1. npmのスコープに対してnpmレジストリのURLを割り当てる
   * `.npmrc`ファイルに以下の形式の文字列を追記する。
     ```
     @scope:registry=http://gitlab.example.com/api/v4/packages/npm/
     ```
   * 上記文字列に対して、以下の修正をする。
     * `@scope`の部分はインストールするnpmパッケージのスコープに置き換える。
     * `gitlab.example.com`の部分をGitLabのホスト名に置き換える。
1. npmレジストリの認証時にアクセストークンを利用するよう設定
   * 以下のコマンドを実行して、事前に発行しておいたアクセストークンをnpmレジストリの認証時に利用するよう設定する。
     ```bash
     npm config set -- //gitlab.example.com/api/v4/packages/npm/:_authToken ${GITLAB_ACCESS_TOKEN}
     ```
   * 上記文字列に対して、以下の修正をする。
     * `gitlab.example.com`の部分をGitLabのホスト名に置き換える。
     * `${GITLAB_ACCESS_TOKEN}`の部分は事前に発行したアクセストークンに置き換える。
1. npmパッケージをインストールする
   * 以下のコマンドを実行することで、GitLab上からnpmパッケージをインストールする。
     ```bash
     npm install @scope/package-name
     ```
   * 上記コマンドについて、以下の修正をする。
     * `@scope`の部分はインストールするnpmパッケージのスコープに置き換える。
     * `package-name`の部分はインストールするnpmパッケージの名前に置き換える。

## 参考サイト

* [Gitlab Package Registryを利用してプライベートNPMパッケージを運用する](https://zenn.dev/hytkgami/articles/private-npm-package-with-gitlab-package-registry)
* [GitLabプライベートプロジェクトをNPM公開する](https://qiita.com/keyhole0/items/d9debb8f5bca3ef8adea)
