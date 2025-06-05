# learning-bedrock-book
## 書籍について
#### 書籍の情報
   - [Amazon Bedrock 生成AIアプリ開発入門](https://www.sbcr.jp/product/4815626440/)
#### サンプルコード
   - サンプルコードは[GitHub](https://github.com/minorun365/bedrock-book/tree/main)で公開されている。
## 2.11 Bedrock を試す（2025年4月22日実施）
#### 1. Bedrock で「Claude 3.5 Sonnet」を有効化
   - AWS Bedrockでは、CreateInferenceProfileで使用するベースモデルはOn Demand推論をサポートしている必要があり、Claude 3.7 Sonnetは使用できない。
#### 2. AWS SDK を用いて「Claude 3.5 Sonnet」のAPIへリクエストを行う
   - 新規作成したAWSアカウントや、これまでCloud9を利用していなかったAWSアカウントで、Cloud9コンソールにアクセスできなくなった。
     ![image](https://github.com/user-attachments/assets/9f7d6713-731d-4a25-ad00-fbd8a11d6243)
   - Cloud9の代替として**Amazon SageMaker Studio コードエディタ**を利用する。 [Amazon SageMaker Studio コードエディタ利用手順]( https://qiita.com/minorun365/items/f5289163795d5d7b21e2)
       - 2024 年 12 月 3 日、Amazon SageMaker の名前が Amazon SageMaker AI に変更された。[Amazonからの情報](https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/whatis.html)
     ![image](https://github.com/user-attachments/assets/c530958d-e9f1-4efc-9df1-c2366bb74904)
       - 「Code Editorは統合スタジオ環境の一部」として扱われており、独立したサービスではなくなっている（ https://blog.usize-tech.com/amazon-sagemaker-unified-studio/ ）
       - ```
         恒常的に無料利用枠となるインスタンスが存在しないため、ハンズオンの終了後はインスタンスを忘れず停止もしくは削除しましょう。SageMakerの「ドメイン」自体に対する課金は発生しません。
         ```
         とあるが、インスタンスを停止しても課金が続いている（ごく少額であるが）
         ![image](https://github.com/user-attachments/assets/b500af53-2406-4745-8bc6-59cc7be1a0c5)
         ![image](https://github.com/user-attachments/assets/077c5693-2fd3-46ac-a7ec-14720baac482)

   - 「Boto3」をインストール（ https://docs.aws.amazon.com/ja_jp/cloud9/latest/user-guide/sample-python.html ）
      - 依存関係のエラーが出た場合
         - エラーメッセージに従ってバージョンを合わせる
         - パッケージ同士のバージョン不整合が起きているとき、下記の手順を実行する
            1. 基本対応フロー
               ```mermaid
               graph TD
                   A[エラー発生] --> B{エラータイプ判定}
                   B -->|依存関係不足| C[手動インストール]
                   B -->|バージョン競合| D[バージョン指定]
                   B -->|ディスク不足| E[キャッシュ削除]
                   C --> F[動作確認]
                   D --> F
                   E --> F
                ```
            2. 具体的な解決手順
               <br>
               ケース1: 依存パッケージ不足
                  ```bash
                  # 不足パッケージを手動インストール
                  pip install numpy>=1.22.4 pytz>=2020.1 tzdata>=2022.7
                  
                  # conda経由でインストール（推奨）
                  conda install -c conda-forge numpy=1.22.4 pytz=2022.7 tzdata=2023.3
                  ```
               
               ケース2: パッケージバージョン競合
                  ```bash
                  # 特定バージョンを明示指定
                  pip install autogluon==0.8.0 --no-deps
                  
                  # 競合パッケージを別バージョンで再インストール
                  pip uninstall pytz
                  pip install pytz==2022.7
                  ```
                  
               ケース3: ディスク容量不足
                  ```bash
                  # condaキャッシュ全削除
                  conda clean --all -y
      
                  # pipキャッシュ削除
                  pip cache purge
                  
                  # 不要パッケージ手動削除
                  rm /home/user/.conda/pkgs/問題のパッケージ名
                  ```

   - 2_invoke-model.py で最新モデルを定義
      ```python
      # モデルを定義（Claude 3.5 Sonnet）
      modelId = "anthropic.claude-3-5-sonnet-20240620-v1:0"
      ```
      - 以降作成するファイルでも同様にテンプレートのモデル定義を変更する必要がある。

## 3.5 LangChain と Streamlit を使った生成AIアプリ開発（2025年4月28日実施）
#### 1. 開発環境の準備
   - ライブラリのインストール
      ```bash
      pip install boto3==1.34.87 langchain==0.2.0 langchain-aws==0.1.4 langchain-community==0.2.0 streamlit==1.33.0 python-dateutil==2.8.2
      ```
   - コードエディタを起動するたびに、Streamlit をインストールする必要がある。
     ```bash
     pip install streamlit
     ```
#### 2. 【ステップ1】LangChain の実装
   - LangChainは生成AIアプリの開発フレームワークとしてデファクトスタンダードの地位を確立している。
   - LangChainを利用すると、GPT-4やGeminiなどの複数の生成AIモデルを同じインターフェースで利用できる。
   -  テンプレートのモデル定義を変更する必要がある。
      ```python
      # モデルを定義（Claude 3.5 Sonnet）
      model_id = "anthropic.claude-3-5-sonnet-20240620-v1:0"
      ```
      - 以降作成するファイルでも同様にテンプレートのモデル定義を変更する必要がある。
#### 3. 【ステップ2】ストリーム出力
   - 生成AIが生成した文字列を細かい単位で出力することを**ストリーム出力**という。
   - LangChainでは、ストリーム出力を設定できる。
#### 4. 【ステップ3】Streamlit との統合
   - **Amazon SageMaker Studio コードエディタ**を利用してStreamlitアプリを起動する方法は[Streamlitアプリの起動＆プレビュー方法](https://qiita.com/minorun365/items/f5289163795d5d7b21e2)を参照。
   -ターミナルで以下のStreamlitの実行コマンドを実行。
     ```bash
     streamlit run <ファイル名>
     ```
     - アプリの実行を停止する際は、`Crtl + C`を押す。
   - 新たにターミナルを起動し、以下のPinggyの実行コマンドを実行。
     ```bash
     ssh -p 443 -R0:localhost:8501 a.pinggy.io
     ```
     - アプリの実行を停止する際は、`Crtl + C`を押す。
   - 表示されるURLのうち下側（https:// で始まる方）をコピーして、ブラウザの新しいタブで開き、「Enter site」をクリック
   - チャットアプリケーションが完成。ただ1つ以上前の会話を踏まえた回答は得られない。
     ![image](https://github.com/user-attachments/assets/2447fd23-8c3c-4b0e-968e-c64151fc2fdd)
#### 5. 【ステップ4】チャット形式の継続したやりとり
   - Streamlit の `session_state` で各ユーザーセッションの再実行間で変数を共有する。
        - 参考：[Session State - Streamlit Docs](https://docs.streamlit.io/develop/api-reference/caching-and-state/st.session_state)
#### 6. 【ステップ5】チャット履歴の永続化
   - Amazon DynamoDBにチャット履歴を保存する。
## 3.6 AWS Lambda 上で動作する生成AIアプリ開発（2025年5月30日実施）
#### 1. Lambda レイヤーを作成する
   - ターミナルで以下のコマンドを実行し、ディレクトリを作成。
     ```bash
     mkdir python
     ```
   - ターミナルで以下のコマンドを実行し、必要な Python ライブラリを取得。
     ```
     pip install -t python langchain==0.2.0 langchain-aws==0.1.4 langchain-community==0.2.0 python-dateutil==2.8.2
     ```
   - langchain-aws の依存ライブラリとして boto3 がインストールされるが、 Lambda の環境にインストール済のため、 Lambda レイヤーから削除する。
     ```bash
     rm -r python/boto*
     ```
   - zip 形式で圧縮。
     ```bash
     zip -r9 langchain-layer.zip python
     ```
     - zip コマンドを実行できないときは、以下のコマンドを実行して zip コマンドをインストールする。
       ```bash
       sudo apt-get install zip
       ```
   - Lambda レイヤーを登録。ターミナルで`python --version`を実行し、その情報を基にランタイムのpythonのバージョンを指定する。（以下ではpython3.12を指定）
     ```bash
     aws lambda publish-layer-version \
      --layer-name langchain-layer \
      --compatible-runtimes python3.12 \
      --compatible-architectures x86_64 \
      --zip-file fileb://langchain-layer.zip --no-cli-pager
     ```
#### 2. Lambda 関数を作成する
   - マネジメントコンソールで作成する。ランタイムはLambda レイヤーに登録したものに合わせる。
   - これで生成AIアプリのバックエンド部分を構築できた。
     
## 4.2 Knowledge basesでRAGを実装してみよう（2025年6月4日実施）
#### 1. S3バケットを作成する
   - [bedrock-ug.pdf](https://docs.aws.amazon.com/pdfs/bedrock/latest/userguide/bedrock-ug.pdf)をPCのローカルに保存する。
   - S3バケットに先ほど保存したPDFファイルをアップロードする。
#### 2. ナレッジベースを作成する
   - Bedrockコンソールで「ナレッジベース」を選択し、ナレッジベースを作成する。
   - コストを掛けずにRAGを試用するために、**Kendra GenAI Index を含むナレッジベース**を作成する。
   - 参考：[【未経験者大歓迎】RAG超入門：AWSが推奨するRAGを体験するハンズオン #生成AI - Qiita](https://qiita.com/moritalous/items/61f91039c13aeb9a51eb)
