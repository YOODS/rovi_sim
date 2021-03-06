# VT simulator  
rovi_simはYCAM3D無しで、VT機能を机上でシミュレーションします。

## インストール  
先にRoVIおよびVTをインストします(https://github.com/YOODS/rovi_visual_teach)。その後このReposをcatkinワークスペースにCloneします。

## 起動  
~~~
roslaunch rovi_sim start.launch
~~~
または *起動* アイコンをクリック。

## nodeグループ
### subscriber

|トピック名|機能|結果|
|:----|:---|:----|
|X1|キャプチャをシミュレートします。カメラ位置と視野範囲(ローカルパラメータ)から撮影される点群をパブリッシュします|Y1:撮影結果<br>ps_floats:点群|
|place|パラメータで指定した積み方でワークを配置します。|error:ワーク残数＞０で呼ばれた場合|
|place0|ワーク１個をワールド原点に配置します||
|pick1|両端以外のワークをピックします。ピックするワークは指定フレーム(デフォルトcamera/capture0/solve)を参照します。ピック可能なワークは、最上段の両端以外のワークです。それ以外のワークをピックしようとしたときはエラーとなります。|error:所定のワーク以外を取ろうとした。|
|pick2|両端のワークをピックします。このトピックでピック可能なワークは、最上段の両端ワークです。それ以外のワークをピックしようとしたときはエラーとなります。|error:所定のワーク以外を取ろうとした。|
|pick3|異形ワークをピックします。このトピックでピック可能なワークは、最上段の異形ワークです。それ以外のワークをピックしようとしたときはエラーとなります。|error:所定のワーク以外を取ろうとした。|
|/request/redraw|点群を再出力する||

### publisher

|トピック名|内容|形式|
|:----|:---|:----|
|Y1|キャプチャの成否を出力します|Bool|
|wp_floats|配置された点群をワールド座標で出力します|Numpy|
|ps_floats|配置された点群をカメラ座標で出力します。カメラの視野に応じたクロップ処理を行います。|Numpy|
|error|pickエラー出力します。エラーはロガー(/report)へも出力します|Int|

### 構成パラメータ/config/vstacker

|パラメータ名|内容|デフォ値|
|:----|:---|:----|
|model|ワークのPLYファイル|test/shaft.ply|
|dif|異形ワークデータファイルのパス|test/cylinder.ply|
|env|環境データ(バケット等)ファイルのパス|test/env.ply|
|range_x|X方向(シャフト軸方向)の配置範囲|50(mm)|
|range_y|Y方向(シャフト径方向)の配置範囲|1000(mm)|
|dmin|最小軸間距離|100(mm)|
|layer|段積み層数|3|
|precision|ピック座標誤差。ピック座標指示はこの値以内でなければエラーになります。|5(mm)|
|master_frame_id|マスターワークのTF名|camera/master0|
|pick_frame_id|ピック座標のTF名|camera/capture0/solve0|

### 構成パラメータ/config/vcam
|trim_x|X方向カメラ視野幅|1000(mm)|
|trim_y|Y方向カメラ視野幅|300(mm)|
|source_frame_id|入力点群ののTF名|world|
|target_frame_id|出力点群ののTF名|camera|

### エラーコード
|コード|内容|
|:----|:---|
|9000|座標に該当するワークが存在しない|
|9001|pick1にて不正なワークが指示された|
|9002|pick2にて不正なワークが指示された|
|9003|pick3にて不正なワークが指示された|
|9004|ワークが残っている状態で再配置(place)された|
