# learning-bedrock-book
## Bedrock を試す（2025年4月22日実施）
#### 1. Bedrock で「Claude 3.7 Sonnet」を有効化
#### 2. AWS SDK を用いて「Claude 3.7 Sonnet」のAPIへリクエストを行う
   - 新規作成したAWSアカウントや、これまでCloud9を利用していなかったAWSアカウントで、Cloud9コンソールにアクセスできなくなった
     ![image](https://github.com/user-attachments/assets/9f7d6713-731d-4a25-ad00-fbd8a11d6243)
   - Cloud9の代替として「Amazon SageMaker Studio コードエディタ」を利用する （ https://qiita.com/minorun365/items/f5289163795d5d7b21e2 ）
       - 2024 年 12 月 3 日、Amazon SageMaker の名前が Amazon SageMaker AI に変更された（ https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/whatis.html ）
     ![image](https://github.com/user-attachments/assets/c530958d-e9f1-4efc-9df1-c2366bb74904)
       - 「Code Editorは統合スタジオ環境の一部」として扱われており、独立したサービスではなくなっている（ https://blog.usize-tech.com/amazon-sagemaker-unified-studio/ ）
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
                  ```

      
                  # pipキャッシュ削除
                  pip cache purge
                  
                  # 不要パッケージ手動削除
                  rm /home/user/.conda/pkgs/問題のパッケージ名
                  ```

   - 2_invoke-model.py で最新モデルを定義
      ```python
      # モデルを定義（Claude 3.7 Sonnet）
      modelId = "anthropic.claude-3-7-sonnet-20250219-v1:0"
      ```
