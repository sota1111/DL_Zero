# Wandb Sweepの使い方
## 説明のモチベーション
Wandbの使い方を説明してる記事が以下くらいしかなく、以下の記事も説明を省略してるので、作成しました。  
[Qiitaの記事](https://qiita.com/daikiclimate/items/9743976a108ea039bd88)
## インストール
以下のサイトにアクセスして、personalで登録（会社で使う場合は有償）  
https://wandb.ai/home
# WandB(Weights & Biases)とは
- WandB:モデルやデータセット、システム情報などのトラッキングを用意にするプラットフォーム  
W&Bは以下の4つのツールがある。
1. Dashboard（ダッシュボード）: 実験のトラッキングを行います
2. Artifacts（アーティファクト）: データセットのバージョン管理、モデルのバージョン管理を行います
3. Sweeps（スイープ）: ハイパーパラメータの最適化を行います
4. Reports（レポート）: 結果を再現可能な状態で保存・共有しま
先に1の機能を説明する
## 1. Dashboard（ダッシュボード）
実験経過を下図のように記録することができる。
![image](https://user-images.githubusercontent.com/97399125/210121904-0b8a9d4e-bbba-4100-a275-fba1045295d2.png)
その時のパラメータの値を確認することが可能。
![image](https://user-images.githubusercontent.com/97399125/210121953-142a2e61-ad39-4b67-b2ba-f69c390e19da.png)

数行を追加するだけで実験結果を記録することができる。  
MNISTで実行するコード  
https://github.com/sota1111/DL_Zero/blob/main/saitan/mnist_min_dis.ipynb
上記にWandB機能を埋め込んだコード  
https://github.com/sota1111/DL_Zero/blob/main/saitan/saitan8_wandb.ipynb

Dashboardは記録するためのツールであり、どのパラメータが最適化を検討するにはSWEEP機能を用いる。


## 3. Sweeps（スイープ）
### できること
自動でパラメータを振り、その結果を可視化することができる。  
以下は１層のNNでMNISTを実行した結果を可視化した。パラメータの重要性を確認することができる。  
![image](https://user-images.githubusercontent.com/97399125/210122273-82bbc6df-86f9-40e1-a3fd-7c573de83edb.png)  
パラメータによりスコアがどのように変わるのかを確認することができる。  
![image](https://user-images.githubusercontent.com/97399125/210122287-a584d075-3ec1-437e-bde7-b06fc71d784b.png)
低いスコアを取ったパラメータを確認することができる。
![image](https://user-images.githubusercontent.com/97399125/210122330-f5951502-7673-424a-9edd-1ff407ad9233.png)
上記の場合、隠れ層が小さく、learning_rateが小さく、最適化関数がSDGの場合に低いスコアになることが判る。  
この結果を基に二層のNNで実行した結果が以下。  
![image](https://user-images.githubusercontent.com/97399125/210122428-e8ec2f92-21d3-4463-9b94-05dbadbb9cdf.png)  
最適化関数をAdamのみにした。不要なので、右上のペンマークから削除する。  
![image](https://user-images.githubusercontent.com/97399125/210122462-1bea0bf3-43c1-44ed-88c1-dce518f0b6cb.png)  
一層と同様に低いスコアを取った場合を選択する。
![image](https://user-images.githubusercontent.com/97399125/210122496-34b06c88-daa5-40fc-8b97-5578fd848814.png)
一層で二層目の隠れ層の影響が判らないので、二層のみにする。左上のフィルタからNet2のみを選択する。
![image](https://user-images.githubusercontent.com/97399125/210122539-2262f70d-f5e4-47b9-ac93-2f65c6278745.png)
一層目の隠れ層の影響が大きいことが判る。  
最も高い精度となるパラメータを確認する。  
![image](https://user-images.githubusercontent.com/97399125/210122607-bf97c439-d45c-4a03-9ef4-6005b1030b44.png)
二層、batch_size大、epochs大、隠れ層大となるパラメータが高い精度を取ることを確認できる。  
隠れ層が大きくない場合でも高スコアを取っていることから、小さいNNを構築するためには左側を固定し、隠れ層を調整する必要があることが判る。  
### 使い方
詳細な使い方は以下のサイトで説明されている。
https://docs.wandb.ai/guides/sweeps/
YAMLファイルを用いる場合、以下のように記述する。  
![image](https://user-images.githubusercontent.com/97399125/210122759-5feef0ee-b588-4a55-aa8b-2bd2df2bd3e1.png)
Methodには３つの選択肢があり、randomで範囲を絞り、gridやbayesで最適なパラメータを見つけるのが良いと記述がある。
![image](https://user-images.githubusercontent.com/97399125/210122786-23cea844-8871-40b3-84a1-0e270667fcf5.png)
パラメータはリストや分散等、様々な振り方をすることできる。（分散にも多くの方法が準備されている。）  
![image](https://user-images.githubusercontent.com/97399125/210122811-6db702ee-45a6-469c-9069-f77df4742adf.png)
### コード
上記で紹介した実験結果を取得したコードが以下になります。  
関数コールバックで記述する必要があり、結構コードを変更しています。  
最初は関数化することを念頭においてなかったので、下手なコードです。。。
https://github.com/sota1111/DL_Zero/blob/main/saitan/saitan8_wandb_sweep.ipynb


