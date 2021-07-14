# Azure DevOps を使用したハンズオン

## Azure DevOps とは

[Azure DevOps Services | Microsoft Azure](https://azure.microsoft.com/ja-jp/services/devops/)

## Azure DevOps Starter リソースの作成

**まずはサンプルのプロジェクトを作ってみましょう。**

[Microsoft Azure ポータル](https://portal.azure.com/)にサインインする。
このときハンズオンで使用するメールアドレスおよびパスワードでサインインすること。

下記のドキュメントを参考に Azure DevOps Starter リソースを作成する。
入力項目はユニークである必要があるためチェックマークがつく内容を入力する必要がある。  
**※「Azure DevOps と Azure サブスクリプションを構成する」まで進めたら、本ハンズオンドキュメントに戻る。**

- [クイック スタート:Azure DevOps Starter を使用して .NET 用 CI/CD パイプラインを作成する | Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/devops-project/azure-devops-project-aspnet-core)

※DevOps Starter を設定するための既定のオプションが GitHub となっているため、 Azure DevOps に変更する。  
「Setting up DevOps starter with Github. change setting here」というの文言のhere部分を押す
![starter-settings-01.png](./images/starter-settings-01.png)

![starter-settings-02.png](./images/starter-settings-02.png)

**事前にOrganizationが作成されている場合**
- 「Azure Devops Organization」は事前に作成した [Azure Pipelineが無料付与されている](https://docs.microsoft.com/ja-jp/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops&tabs=ms-hosted) Organizationを選ぶようにすること
-  選択時に「You don't have permissions...」と警告が出た場合、管理者にユーザーアクセス管理者のロールをサブスクリプションに追加するようにお問い合わせください。

※Additional settingsについて
- Pricing Tier は「F1」を選ぶようにする

![01.png](./images/01.png)

作成された Azure DevOps Starter リソースでは、サンプルのソースコードを用いた各種パイプラインや Web サーバーが構築されるため、 Web ページにアクセスし表示を確認する。
![02.png](./images/02.png)

## 独自のプロジェクトの作成

新たに下記の構成で Azure DevOps Starter リソースを作成する。  
※[先述の手順](#Azure-DevOps-Starter-リソースの作成)と同様に GitHub から Azure DevOps に変更したうえで Bring your own code を選択

![03-01.png](./images/03-01.png)

- Bring your own code

![03-02.png](./images/03-02.png)

- Code repository : Other Git
  - Repository URL : <https://github.com/alterbooth/hol-aspnetcore-sample.git>
    - Branch : master
    - Private repository : No

![03-03.png](./images/03-03.png)

- Is app Dockerized : No
- Select application framework : ASP.NET Core

![03-04.png](./images/03-04.png)

- Windows Web App

![01.png](./images/01.png)

- 上記以外の項目はチェックマークがつく内容を入力すること
- **事前にOrganizationが作成されている場合**  
  - 「Azure Devops Organization」は事前に作成した [Azure Pipelineが無料付与されている](https://docs.microsoft.com/ja-jp/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops&tabs=ms-hosted) Organizationを選ぶようにすること
- ※Additional settingsについて
  - ハンズオン後半にApp Service PlanのPricing TierがS1以上でなければ動作しない[手順](#task-6--blue-green-デプロイメント)があるため、Pricing Tier は「S1」を選ぶようにする

### 設定の確認

作成した Azure DevOps Starter リソースに移り [Project homepage] から Azure DevOps にアクセスする。  
プロジェクトの設定にて下記の機能が有効になっていることを確認する。

- Boards
- Repos
- Pipelines
- Artifacts
- Test plans

![03-05.png](./images/03-05.png)

## Work Items の作成

### Feature の作成

Boards > Work items > New Work Item > Feature を作成する。

![New Work Item](images/new-work-item.png)

![Add Feature](images/add-feature.png)

- Title : `Azure DevOps ハンズオン`　
- Assign : 自分
- Iteration : Iteration 1

### User Story の作成

Boards > Boards から先ほど作成した Feature を開く。

Storiesが表示されている場合、右上の Stories を Feature に変更する。

![Add User Story](images/add-user-story.png)

下記の User Story を追加する。

- `Azure Web App にコードをデプロイする`

先ほど作成した User Story が Boards に表示されていることを確認する。

![Boards Stories](images/boards-stories.png)

### Task の作成

先ほど作成した User Story に Task を追加する。

![Add Task](images/add-task.png)

- `Gitリポジトリをインポートする`
- `ビルドパイプラインを作成する`
- `単体テストを行う`
- `ソースコードを変更する`
- `自動リリースを構成する`
- `Blue-Greenデプロイメント`

先ほど作成した Tasks が Sprints に表示されていることを確認する。

![Sprints](images/sprints.png)

## User Story : Azure Web App にコードをデプロイする

先ほど作成した User Story を Active に動かす。

![Active User Story](images/active-user-story.png)

### Task 1 : Gitリポジトリをインポートする

Task を Active に動かす。

![Active Task](images/active-task.png)

Repos にアクセスし、下記の Github リポジトリをインポートする。  

<https://github.com/alterbooth/hol-aspnetcore-sample.git>

![04.png](./images/04.png)

インポートに成功したら Task を Closed に動かすこと。

![Closed Task](images/closed-task.png)

### Task 2 : ビルドパイプラインを作成する

Task を Active に動かす。

Pipelines > Pipelines にアクセスし、 ASP.NET Core のビルドパイプラインを作成する。

1. [New pipeline] をクリックする
2. [Use the classic editor] をクリックする
3. インポートしたリポジトリをソースに指定する
4. テンプレートは選ばず 「Select a template」の下にある [Empty job] をクリックする
5. 下記のタスクを追加する
    - dotnet restore（.NET Coreを追加し、Commandをrestoreへ切替え）
        - Path to project : `RazorPagesMovie/*.csproj`
    - dotnet build
        - Path to project : `RazorPagesMovie/*.csproj`
        - Arguments : `--configuration Release`
    - dotnet publish
        - Publish web projects : false
        - Path to project : `RazorPagesMovie/*.csproj`
        - Arguments : `--configuration Release --output $(build.artifactstagingdirectory) -r win-x86 --self-contained true`
    - Publish build artifacts
        - 設定変更不要
6. master ブランチへのプッシュをトリガーとする
    - [Triggers] > [Enable continuous integration]
7. パイプラインを保存する
    - [Save & queue] > [save]
8. [Queue] をクリックしパイプラインを手動実行する
9. ビルドパイプラインが成功したことを確認する

![05.png](./images/05.png)

**成果物の確認**

成功したビルドパイプラインの結果にアクセスし [published] から Zip ファイルをダウンロードする。

![Pipelines Build Summary](images/pipelines-build-summary.png)
![Pipelines Build Artifacts](images/pipelines-build-artifacts.png)

- ダウンロードしたZipファイルを展開する。
- 成果物の確認を行ったら Task を Closed に動かすこと。

### Task 3 : 単体テストを行う

Task を Active に動かす。

Task 2 にて作成したパイプラインを変更して、単体テストのパイプラインを追加する。Task 2 で作成したパイプライン定義を選択して \[Edit\] から変更します。

1. 下記のタスクを `dotnet build` と `dotnet publish` の間に追加する
    - dotnet test
        - Path to project : `RazorPagesMovie.Test/*.csproj`
        - Arguments : `--configuration Release`
2. パイプラインを保存する
    - [Save & queue] > [save]
3. [Queue] をクリックしパイプラインを手動実行する
4. ビルドパイプラインが成功したことを確認する

![Add dotnet test](images/add-dotnet-test.png)

![dotnet test job](images/pipeline-jobs-run-dotnet-test.png)

確認を行ったら、Task を Closed に動かす。

### Task 4 : ソースコードを変更する

**開発作業を行う**

Task を Active に動かす。

Task を開き、画面右のDevelopmentの下にある [Create a branch] からブランチを作成する。

作成したブランチにてファイルを修正し、変更をコミットする。  
このときコミットメッセージに `#<Task ID>` を含め [Work items to link] にて Task 4 を指定すること。

再び Task 4 にアクセスし、 Task とブランチが紐付けされていることを確認する。

**プルリクエストを作成する**

Task 4 を開き [Create a pull request] から master ブランチへのプルリクエストを作成する。  
このとき自分をレビューアーに指定すること。

プルリクエストにて差分の確認を行い、承認およびマージを実行する。

**ビルドパイプラインを確認する**

- Task 2 にて作成したパイプラインにアクセスし、プルリクエストのマージをトリガーとしたビルドパイプラインが自動実行されたことを確認する。  
- ビルドパイプラインにてエラーが発生した場合は必要な修正を行うこと。

**成果物の確認**

- Task 2 と同様に、ビルドパイプラインから Zip ファイルをダウンロードし、展開する。  
- 成果物の確認を行ったら Task を Closed に動かすこと。

### Task 5 : 自動リリースを構成する

Task を Active に動かす。

**パイプラインの設定**

Pipelines > Release > New release pipeline

![New Release Pipeline](images/new-release-pipeline.png)

テンプレートに [Azure App Service deployment] を指定する。  

![New Release Pipeline Template](images/new-release-pipeline-template.png)

Artifacts には先ほど作成したビルドパイプラインを指定する。  

![New Release Pipeline Artifacts](images/new-release-pipeline-artifacts.png)

ビルドパイプラインの完了をリリースパイプラインのトリガーとする。

![New Release Pipeline Trigger](images/new-release-pipeline-trigger.png)

Stage のタスクにてデプロイ先となる Azure サブスクリプションと App Service 名を指定する。

![New Release Pipeline Tasks](images/new-release-pipeline-tasks.png)

\[Deploy Azure App Service\] タスクの詳細にて、下記の設定をする。

- Additional Deployment Options
  - Selected deployment method : true
  - Deployment method : Web Deploy

![Task Deploy Azure App Service](images/new-release-pipeline-tasks-app.png)

最後に \[Save\] で保存する。

**デプロイを確認する**

- リリースパイプラインが用意できたらソースコードの変更を行い、 master ブランチにコミットする。  
- 自動ビルドおよび自動デプロイが終了し、アプリケーションに変更が正しく反映されていることを確認する。
- 確認を行ったら、Task を Closed に動かす。

### Task 6 : Blue-Green デプロイメント

Task を Active に動かす。

**スロットを追加する**

Azure ポータルに戻り、Azure DevOps Starter リソースの \[App Service\] から Web App リソースへ移動します。

![02.png](./images/02.png)

![Web App Overview](images/webapp-overview.png)

\[Deployment Slot\] からスロットを追加します。

![Add Slot](images/webapp-add-slot.png)

追加したスロットを確認します。

![Added Slot](images/webapp-added-slot.png)

追加したスロットの Web ページにアクセスし表示を確認する。

![Staging Web Site](images/webapp-stg-slot-web-empty.png)

**スロットにデプロイする**

Task 5 の自動リリースの設定を変更して、スロットを使う構成にリリース定義を変更します。

Task 5 で作成したリリース定義を選択して \[Edit\] から変更します。
![Edit Release Pipeline](images/edit-release-pipeline.png)

\[Delete\] で Stage を削除します。
![Edit Stage](images/edit-release-pipeline-edit-stage.png)

\[New stage\] で新たに Stage を作成します。
![Add Stage](images/edit-release-pipeline-add-stage.png)

\[Search\] から "slot" で検索し、 テンプレートに \[Azure App Service deployment with slot\] を指定します。
![New Release Pipeline Template Web App Slot Deploy](images/new-release-pipeline-template-webapp-slot.png)

Stage のタスクにてデプロイ先となる Azure サブスクリプションと App Service 名、リソースグループ名、スロット名を指定する。

![New Release Pipeline Web App Slot Tasks](images/new-release-pipeline-template-webapp-slot-task.png)

\[Deploy Azure App Service to Slot\] タスクの詳細にて、下記の設定をする。

- Additional Deployment Options
  - Selected deployment method : true
  - Deployment method : Web Deploy

![Slot Task Deploy Azure App Service](images/new-release-pipeline-template-webapp-slot-task-app.png)

最後に \[Save\] で保存する。

**デプロイを確認する**

- リリースパイプラインが用意できたらソースコードの変更を行い、 master ブランチにコミットする。
- 自動ビルドおよび自動デプロイが終了し、アプリケーションに変更が正しく反映されていることを確認する。
- 追加したスロットの Web ページにアクセスし、**ソースコードを変更する前の表示**を確認する。
- 確認を行ったら、Task を Closed に動かす。

**参考ドキュメント**

- [デプロイのベスト プラクティス - Azure App Service | Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/app-service/deploy-best-practices#use-deployment-slots)
- [ステージング環境を設定する - Azure App Service | Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/app-service/deploy-staging-slots)
- [App Service のデプロイ スロットを使ってテストとロールバック用に Web アプリのデプロイをステージングする - Learn | Microsoft Docs](https://docs.microsoft.com/ja-jp/learn/modules/stage-deploy-app-service-deployment-slots/)

## チーム開発

複数人での DevOps を体験するために複数人のチームを組み、一人の Organization に他のメンバーを招待し、本ハンズオンを再度実施してみる。

## Microsoft Learn の Azure DevOps ラーニングパス

- [Azure DevOps ラーニング パスでアプリケーションをビルドする - Learn | Microsoft Docs](https://docs.microsoft.com/ja-jp/learn/paths/build-applications-with-azure-devops/)
- [DevOps プラクティスのラーニング パスを発展させる - Learn | Microsoft Docs](https://docs.microsoft.com/ja-jp/learn/paths/evolve-your-devops-practices/)

## より本格的に Azure DevOps を使ってみる

[Azure DevOps Demo Generator](https://azuredevopsdemogenerator.azurewebsites.net/) を使用して、自身の Organization にデモデータを作成してみる。  
Boards, Repos, Pipelines にどのようなデータや定義が用意されているかを確認する。

## 後片付け

ハンズオンで作成した全てのリソースグループを削除する。
