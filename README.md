# Netwrok Analysis
## ツイートの収集
`get_tweets.r`でツイートを収集する.  
**ウクライナ**という単語が含まれるツイートを30000件収集するようになっている.  
収集したツイートを`result.csv`に保存する.
## フォロワーの収集
`get_followers.r`でフォロワーを収集する.  
まず、`result.csv`に保存されているツイートの情報から、ツイートをリツイート数が多い順にソートし、アカウント名の重複を除去する.  
上の操作で作成したデータフレームから上位30アカウントを取り出す(これをネットワークのノード(頂点)とする).  
各アカウントのフォロワーを10000フォロワーずつ取り出し、データフレームを作成し、`follower_data.csv`に保存する.
## ネットワークの作成
`make_network.r`でネットワークを作成する.  
`follower_data.csv`は30列のデータフレームであり、各列は各アカウントのフォロワー情報が格納されている.  
データフレームを1行ずつ比べていき、同じフォロワーがいた場合、その数を記録する.
例えば、1列目と　5列目(1アカウント目と5アカウント目)に共通のフォロワーが100人いた場合、`1 5 100`というように記録する.  
これがネットワークの辺になる.  
これを全列の組み合わせについて行い、作成したデータフレームを辺リストと呼び、`edge_list.csv`に保存する.
## コミュニティ抽出
`mdmc.jl`でMDMCという手法を用いてコミュニティ抽出を行なった.  
手法についての説明は省くが、Juliaを使用しているのは高速化のためである.  
また、辺リストでネットワークを保存したのは`mdmc.jl`がネットワークの情報を辺リストで読み込むためである.  
コンパイルした後、JuliaのREPLで`main()`で実行可能. `main(alpha_ini=0.5)`でパラメータalphaの値を指定できる.  
alphaはデフォルトで0.5に設定されているが、より小さい値にするほどコミュニティは細かく分割される.  
結果は`f_main_belongingness.csv`に保存する.
## ネットワークの描画
`draw_network.r`でネットワークを描画する.  
抽出した30アカウントのアカウント名を`accounts.csv`に保存しておいたので、これを読み込み、描画の際のラベルにする.  
次に`edge_list.csv`を読み込み、その辺リストから隣接行列を作成する.  
辺リストからもグラフオブジェクトを作ることは可能だが、描画で彩色する際、色と頂点がずれてしまうため、一度隣接行列に変換している.  
また、`weight_cut_off`で任意の重み以下の辺を除去できるようにしている. 隣接行列において一本も辺を持たないノードは除去している.  
作成した隣接行列からグラフオブジェクトを生成する. 重みで辺の太さを調節し、頂点名にアカウント名をつける.  
MDMCの結果を読み込み、その情報からクラスターオブジェクトを生成する.  
作成したグラフオブジェクト、クラスターオブジェクトからネットワークを描画し、`plot.png`に保存する.
## データについて
データは全て`data`フォルダに入っている.  
画像データはlouvain法でコミュニティ抽出したものとMDMCでパラメータalphaを変えて抽出したものである.
