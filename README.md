# PredictTool
需要予測ツール(exe版)の使い方

## dir構成
- input: 入力データ用dir
    - サンプルデータ: 〇〇ガス会社 九州某市内 低圧40A 電力実績[kWh], 気温[℃]
- output: 出力データ用dir
- Preprocessing: 前処理ツール用dir
- Predict: 予測ツール用dir

## 実行手順
- 手順1: inputに入力データ（input.csv）を追加する（ファイル形式はサンプルデータを参照）
- 手順2: 前処理ツールの設定をする（preprocessing_config.ini）
- 手順3: 前処理ツールを実行する（preprocessing.exeをダブルクリック）
    - 結果: inputに加工済データ（data_preprocessed.csv）が出力・保存される
- 手順4: 予測ツールの設定をする（predict_config.ini）
- 手順5: 予測ツールを実行する（predict.exeをダブルクリック）
    - 結果: outputに学習データ（tra.csv）、検証データ（val.csv）、予測データ（predict.csv）、予測トレンド図（predict.png）が出力・保存される

## 機能
- Preprocessing
    - データ加工: 入力データを予測するための変数に変換・加工する
        - Timestampの分離: Timestamp列から新たな変数を追加する
            - 年／月／日／時刻／曜日を自動的に追加
        - Label-Encoding: カテゴリ列をlabel-encodingし、ラベル（整数値）に変換する
        - Onehot-Encoding: カテゴリ列をonehot-encodingし、バイナリ（0-1）に変換する
    - フィルタリング: 空白要素があれば、その行を自動でフィルタリングする
- Predict
    - 期間・変数設定: 入力データから予測に使用する部分を抜き出す
    - 学習・予測: 抜き出したデータと指定した予測手法を用いて、学習・予測する
    - 予測トレンド描画: 予測期間の実績値と予測値のトレンド図を描画する
- Error処理
    - 実行中でエラーが発生すると、logフォルダにlogファイルをdumpする

## 前処理設定
- [DEFAULT]
    - SplitTimestamp: Trueにすると、Timestamp系変数を生成する
        - timestamp形式は「yyyy/mm/dd HH:MM」 or 「yyyy-mm-dd HH:MM」しか対応していない
    - EncodeLabel: Trueにすると、カテゴリ列をlabel-encodingする
    - EncodeOnehot: Trueにすると、カテゴリ列をonehot-encodingする
        - MLRとPLSはonehot-encoding必須、RFではonehot-encoding不要
- [ENCODELABEL]
    - EncodeLabel=Falseに設定した場合、この設定は無視される
    - XList: label-encodingする列番号を指定（リスト形式で）
        - XList=[0,2]の場合、ファイル3列目と5列目を指定している（timestampと目的変数の列はカウントしない）
        - SplitTimestampで生成した列は、ここで指定する必要が無い（自動でencodeの対象となる）
            - つまり、XList=[]に設定した場合、Timestamp系の列のみ適用される
        - sklearnのLabelEncoderを使用しているため、詳細はsklearnのマニュアルを参照
- [ENCODEONEHOT]
    - EncodeOnehot=Falseに設定した場合、この設定は無視される
    - XList: one-hot-encodingする列番号を指定（リスト形式で）
        - XList=[0,2]の場合、ファイル3列目と5列目を指定している（timestampと目的変数の列はカウントしない）
        - SplitTimestampで生成した列は、ここで指定する必要が無い（自動でencodeの対象となる）
            - つまり、XList=[]に設定した場合、Timestamp系の列のみ適用される
        - pandasのget_dummiesを使用しているため、詳細はpandasのマニュアルを参照

## 予測設定
- [DEFAULT]
    - XList: 説明変数として使用する列番号を指定
        - XList=[0,2]の場合、ファイル3列目と5列目を指定している（timestampと目的変数の列はカウントしない）
    - TraPeriod, PrePeriod: 学習期間・予測期間を指定（timestampの形式は、ファイルにある形式に揃える）
    - ModelingMode: 予測方法を指定（現在はMLR/PLS/RFのみ対応）
        - sklearnのパッケージを使用しているため、詳細はsklearnのマニュアルを参照

## 動作環境
- Windows10

## 開発履歴
- rev.0: 2022/06/17 熊谷渉 (Operational Excellence Gr., Project Design Dep., INV)

## その他
- 今後はこの機能構成をベースに、製品版エンジンのexeとして提供する予定
