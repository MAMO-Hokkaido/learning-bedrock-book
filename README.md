# learning-bedrock-book
## Bedrock を試す
1. Bedrock で「Claude 3.7 Sonnet」を有効化
2. AWS SDK を用いて「Claude 3.7 Sonnet」のAPIへリクエストを行う
   - 新規作成したAWSアカウントや、これまでCloud9を利用していなかったAWSアカウントで、Cloud9コンソールにアクセスできなくなった
     ![image](https://github.com/user-attachments/assets/9f7d6713-731d-4a25-ad00-fbd8a11d6243)
   - Cloud9の代替として「Amazon SageMaker Studio コードエディタ」を利用する （ https://qiita.com/minorun365/items/f5289163795d5d7b21e2 ）
   - 2024 年 12 月 3 日、Amazon SageMaker の名前が Amazon SageMaker AI に変更された（ https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/whatis.html ）
   - 「Code Editorは統合スタジオ環境の一部」として扱われており、独立したサービスではなくなっている（ https://blog.usize-tech.com/amazon-sagemaker-unified-studio/ ）
   - 「Boto3」をインストール（ https://docs.aws.amazon.com/ja_jp/cloud9/latest/user-guide/sample-python.html ）
