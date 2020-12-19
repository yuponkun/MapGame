# グループ９のレポジトリ

## コードの解説

### MapGame.java

```Java
// 以下インポート
// javafx.applicationパッケージのApplicationクラス
import javafx.application.Application;
// javafx.sceneパッケージのSceneクラス
import javafx.scene.Scene;
// javafx.scene.layoutパッケージのPaneクラス
import javafx.scene.layout.Pane;
// javafx.stageパッケージのStageクラス
import javafx.stage.Stage;
// javafx.fxmlパッケージのFXMLLoaderクラス
import javafx.fxml.FXMLLoader;

/**
* fxmlファイルをよみこんでゲームを開始するクラス(MVCのV)
* @version 0.0
*/
public class MapGame extends Application {
    /** ステージ(シーンを表示するjavafxのパーツ) */
    Stage stage;

    /**
     * ゲームを起動するときの処理(Applicationクラスのメソッドをオーバライド)
     * @param primaryStage 最初に表示するステージ
     */
    @Override
    public void start(Stage primaryStage) throws Exception {
      // クラスフィールドのstageにprimarySyageを代入して初期状態を保持
      stage = primaryStage;
      // タイトルを設定
      primaryStage.setTitle("MAP GAME");
      // パネル(要素を設定するjavafxのパーツ)にfxmlファイルを設定
      Pane myPane_top = (Pane)FXMLLoader.load(getClass().getResource("MapGame.fxml"));
      // シーン(パネルを設定するjavafxのパーツ)にパネルを設定
      Scene myScene = new Scene(myPane_top);
      // ステージにシーンを設定
      primaryStage.setScene(myScene);
      // ステージを表示
      primaryStage.show();
    }

    /**
     * ゲームを起動
     * @param args[] コマンドライン変数
     */
    public static void main(String[] args) {
        // ゲームを起動
        launch(args);
    }
}
```

### MapController.java

```Java
// 以下インポート
// java.netパッケージのURLクラス
import java.net.URL;
// java.utilパッケージのResourceBundleクラス
import java.util.ResourceBundle;
// javafx.fxmlパッケージのInitializableインターフェース
import javafx.fxml.Initializable;
// javafx.eventパッケージのActionEventクラス
import javafx.event.ActionEvent;
// javafx.eventパッケージのEventHandlerインターフェース
import javafx.event.EventHandler;
// javafx.scene.controlパッケージのLabelクラス
import javafx.scene.control.Label;
// javafx.scene.imageパッケージのImageクラス
import javafx.scene.image.Image;
// javafx.scene.imageパッケージのImageViewクラス
import javafx.scene.image.ImageView;
// javafx.scene.imageパッケージのGridPaneクラス
import javafx.scene.layout.GridPane;
// javafx.scene.inputパッケージのKeyEventクラス
import javafx.scene.input.KeyEvent;
// javafx.scene.inputパッケージの列挙型KeyCode
import javafx.scene.input.KeyCode;
// javafx.sceneパッケージのGroupクラス
import javafx.scene.Group;
// javafx.scene.layoutパッケージのPaneクラス
import javafx.scene.layout.Pane;

/**
 * マップの描画やキャラの操作などゲームの動きの処理をするクラス(MVCのC)
 * @version 0.0
 */
public class MapGameController implements Initializable {
    /** MapDataクラスでモデリングされているマップ */
    public MapData mapData;
    /** MoveCharaクラスでモデリングされているキャラ */
    public MoveChara chara;
    /** グリッドパネル(画像をマス目に区切って配置できるjavafxのパーツ)(fxmlファイルと対応) */
    public GridPane mapGrid;
    /** イメージビュー(画像を表示するjavafxのパーツ)型の配列 */
    public ImageView[] mapImageViews;

    /**
     * ゲームのウィンドウが生成されたとき始めに呼ばれる処理(Initializableインターフェースのメソッドをオーバライド)
     * @param url 要素の場所(パス)を利用する場合使用
     * @param rb プロパティファイルを読む場合使用
     */
    @Override
    public void initialize(URL url, ResourceBundle rb) {
        // マップを生成してmapDataに代入
        mapData = new MapData(21, 15);
        // キャラを生成してcharaに代入
        chara = new MoveChara(1, 1, mapData);
        // マップのマス分のイメージビューをmapImageViewsに代入
        mapImageViews = new ImageView[mapData.getHeight()*mapData.getWidth()];

        // mapImageViewsに画像データを代入
        for(int y=0; y<mapData.getHeight(); y++){
            for(int x=0; x<mapData.getWidth(); x++){
                int index = y*mapData.getWidth() + x;
                mapImageViews[index] = mapData.getImageView(x,y);
            }
        }
        // drawMapメソッドでゲーム画面を描画
        drawMap(chara, mapData);
    }

    /**
     * 画像をグリッドパネルに設定してゲーム画面を描画
     * @param c キャラ
     * @param m マップ
     */ 
    public void drawMap(MoveChara c, MapData m){
        /** キャラのx座標 */
        int cx = c.getPosX();
        /** キャラのy座標 */
        int cy = c.getPosY();
        // グリッドパネルを初期化
        mapGrid.getChildren().clear();
        // グリッドパネルにキャラとマップの画像を設定
        for(int y=0; y<mapData.getHeight(); y++){
            for(int x=0; x<mapData.getWidth(); x++){
                int index = y*mapData.getWidth() + x;
                if (x==cx && y==cy) {
                    mapGrid.add(c.getCharaImageView(), x, y);
                } else {
                    mapGrid.add(mapImageViews[index], x, y);
                }
            }
        }
    }

    /**
     * キーを押したときに呼ばれる処理(fxmlファイルと対応)
     * @param event キーが押されたというイベントのデータ
     */
    public void keyAction(KeyEvent event){
        /** 押したキーのKeyCode */
        KeyCode key = event.getCode(); System.out.println("keycode:"+key);
        // 条件分岐でH,J,K,LそれぞれのKeyCodeに対応したメソッドを呼ぶ
        if (key == KeyCode.H){
        	  leftButtonAction();
        }else if (key == KeyCode.J){
            downButtonAction(); 
        }else if (key == KeyCode.K){
            upButtonAction();
        }else if (key == KeyCode.L){
            rightButtonAction();
        }
    }

    /**
     * KeyCodeのKが押されたときの処理
     */
    public void upButtonAction(){
        // printActionメソッドでターミナルにUPと表示
        printAction("UP");
        // キャラのsetCharaDirectionメソッドでキャラの画像を上向きのものに変更
        chara.setCharaDirection(MoveChara.TYPE_UP);
        // キャラmoveメソッドでキャラのy座標を-1
        chara.move(0, -1);
        // drawMapメソッドでゲーム画面を再描画
        drawMap(chara, mapData);
    }

    /**
     * KeyCodeのJが押されたときの処理
     */
    public void downButtonAction(){
        // printActionメソッドでターミナルにDOWNと表示
        printAction("DOWN");
        // キャラのsetCharaDirectionメソッドでキャラの画像を下向きのものに変更
        chara.setCharaDirection(MoveChara.TYPE_DOWN);
        // キャラのmoveメソッドでキャラのy座標を+1
        chara.move(0, 1);
        // drawMapメソッドでゲーム画面を再描画
        drawMap(chara, mapData);
    }

    /**
     * KeyCodeのLが押されたときの処理
     */
    public void leftButtonAction(){
        // printActionメソッドでターミナルにLEFTと表示
        printAction("LEFT");
        // キャラのsetCharaDirectionメソッドでキャラの画像を左向きのものに変更
        chara.setCharaDirection(MoveChara.TYPE_LEFT);
        // キャラのmoveメソッドでキャラのx座標を-1
        chara.move(-1, 0);
        // drawMapメソッドでゲーム画面を再描画
        drawMap(chara, mapData);
    }

    /**
     * KeyCodeのHが押されたときの処理
     */
    public void rightButtonAction(){
        // printActionメソッドでターミナルにRIGHTと表示
        printAction("RIGHT");
        // キャラのsetCharaDirectonメソッドでキャラの画像を右向きのものに変更
        chara.setCharaDirection(MoveChara.TYPE_RIGHT);
        // キャラのmoveメソッドでキャラのx座標を+1
        chara.move(1, 0);
        // drawMapメソッドでゲーム画面を再描画
        drawMap(chara, mapData);
    }

    /**
     * func1ボタンが押されたときに呼ばれる処理(fxmlファイルと対応)
     * @param event ボタンが押されたというイベントのデータ
     */
    public void func1ButtonAction(ActionEvent event) {
        // ターミナルにfunc1: Nothing to doと表示
        System.out.println("func1: Nothing to do");
    }

    /**
     * 引数の文字列をAction:に続けてターミナルに表示
     * @param actionString 文字列
     */
    public void printAction(String actionString) {
        // ターミナルにAction:actionStringと表示
        System.out.println("Action: " + actionString);
    }

}
```

### MapData.java

```Java
// 以下インポート
// javafx.scene.imageパッケージのImageクラス
import javafx.scene.image.Image;
// javafx.scene.imageパッケージのImageViewクラス
import javafx.scene.image.ImageView;

/**
 * マップをモデリングするクラス(MVCのM)
 * @version 0.0
 */
public class MapData {
    /** 空白の画像を示す番号 */
    public static final int TYPE_SPACE = 0;
    /** 壁の画像を示す番号 */
    public static final int TYPE_WALL = 1;
    /** その他の画像を示す番号 */
    public static final int TYPE_OTHERS = 2;
    /** 空白と壁の画像が入った配列 */
    private static final String mapImageFiles[] = {
        "png/SPACE.png",
        "png/WALL.png"
    };

    /** イメージ(画像を読み込むjavafxのパーツ)の配列 */
    private Image[] mapImages;
    /** イメージビューの2次元配列 */
    private ImageView[][] mapImageViews;
    /** マップの画像の番号を入れる2次元配列 */
    private int[][] maps;
    /** 横幅 */
    private int width;
    /** 縦幅 */
    private int height;

    /**
     * マップを生成するときに呼ばれる処理(コンストラクタ)
     * @param x xの最大座標(横幅)
     * @param y yの最大座標(縦幅)
     */
    MapData(int x, int y){
        // 2つ分のイメージをmapImages配列に代入
        mapImages = new Image[2];
        // マップのマス分のイメージビュー配列をmapImageViewsに代入
        mapImageViews = new ImageView[y][x];
        // 空白と壁の画像をmapImagesに代入
        for (int i=0; i<2; i++) {
            mapImages[i] = new Image(mapImageFiles[i]);
        }

        // 横幅をwidthに代入
        width = x;
        // 縦幅をheightに代入
        height = y;
        // y*xの2次元配列をmapsに代入
        maps = new int[y][x];

        // fillMapメソッドでmaps全てに壁の画像の番号を設定
        fillMap(MapData.TYPE_WALL);
        // digMapメソッドでmapsにランダムで空白の画像の番号を設定
        digMap(1, 3);
        // setImageViewsメソッドでmapImageViewsにmapsをもとに画像を設定
        setImageViews();
    }

    /**
     * heigthのゲッター
     * @return 縦幅
     */
    public int getHeight(){
        return height;
    }

    /**
     * widthのゲッター
     * @return 横幅
     */
    public int getWidth(){
        return width;
    }

    /**
     * mapsのゲッター
     * @param x x座標
     * @param y y座標
     * @return maps[y][x] or -1
     */
    public int getMap(int x, int y) {
        // xかyが0より小さいかmapsの大きさを超える場合-1を返す
        if (x < 0 || width <= x || y < 0 || height <= y) {
            return -1;
        }
        return maps[y][x];
    }

    /**
     * mapImageViewのゲッター
     * @param x x座標
     * @param y y座標
     * @return mapImageViews[y][x]
     */
    public ImageView getImageView(int x, int y) {
        return mapImageViews[y][x];
    }

    /**
     * mapsのセッター
     * @param x x座標
     * @param y y座標
     * @param type 画像の番号
     */
    public void setMap(int x, int y, int type){
        // xかyが1より小さいかmapsの大きさ以上の場合処理を終了
        if (x < 1 || width <= x-1 || y < 1 || height <= y-1) {
            return;
        }
        // mapsに画像の番号を設定
        maps[y][x] = type;
    }

    /**
     * mapImageViewsのセッター
     */
    public void setImageViews() {
        // mapsの番号に対応するmapImagesの画像をmapImageViewsに代入
        for (int y=0; y<height; y++) {
            for (int x=0; x<width; x++) {
                mapImageViews[y][x] = new ImageView(mapImages[maps[y][x]]);
            }
        }
    }

    /**
     * mapsに引数で指定した画像の番号を設定
     * @param type 画像の番号
     */
    public void fillMap(int type){
        // maps全てに指定した画像の番号を代入
        for (int y=0; y<height; y++){
            for (int x=0; x<width; x++){
                maps[y][x] = type;
            }
        }
    }
    
    /**
     * 空白の画像の番号を設定し迷路を生成
     * @param x x座標
     * @param y y座標
     */
    public void digMap(int x, int y){
        // setMapメソッドで現在の座標に空白の画像の番号を設定
        setMap(x, y, MapData.TYPE_SPACE);
        /** 現在の座標の上下左右いずれかの座標を参照するための2次元配列*/
        int[][] dl = {{0,1},{0,-1},{-1,0},{1,0}};
        /** dlの一時的な退避に用いる配列 */
        int[] tmp;

        // dlの要素を乱数でシャッフル
        for (int i=0; i<dl.length; i++) {
            int r = (int)(Math.random()*dl.length);
            tmp = dl[i];
            dl[i] = dl[r];
            dl[r] = tmp;
        }

        // 現在の座標の2マス先に壁がある間空白を設定
        for (int i=0; i<dl.length; i++){
            int dx = dl[i][0];
            int dy = dl[i][1];
            if (getMap(x+dx*2, y+dy*2) == MapData.TYPE_WALL){
                setMap(x+dx, y+dy, MapData.TYPE_SPACE);
                digMap(x+dx*2, y+dy*2);
            }
        }
    }
}
```

### MoveChara.java

```Java
// 以下インポート
// javafx.scene.imageパッケージのImageクラス
import javafx.scene.image.Image;
// javafx.scene.imageパッケージのImageViewクラス
import javafx.scene.image.ImageView;
// javafx.scene.shapeパッケージのRectangleクラス
import javafx.scene.shape.Rectangle;
// javafx.animationパッケージのAnimationTimerクラス
import javafx.animation.AnimationTimer;

/**
 * キャラをモデリングするクラス(MVCのM)
 * @version 0.0
 */
public class MoveChara {
    /** 下向きのキャラの画像を指定する番号 */
    public static final int TYPE_DOWN  = 0;
    /** 左向きのキャラの画像を指定する番号 */
    public static final int TYPE_LEFT  = 1;
    /** 右向きのキャラの画像を指定する番号 */
    public static final int TYPE_RIGHT = 2;
    /** 上向きのキャラの画像を指定する番号 */
    public static final int TYPE_UP    = 3;

    /** 上下左右それぞれの文字列が入った配列 */
    private final String[] directions  = { "Down", "Left", "Right", "Up" };
    /** 足踏みアニメーションの際のキャラの画像を切り替えるための番号(文字列)が入った配列 */
    private final String[] animationNumbers = { "1", "2", "3" };
    /** キャラの画像の場所(パス)の名前で全てに共通するところを抜き出した文字列 */
    private final String pngPathBefore = "png/cat";
    /** キャラの画像の拡張子を表す文字列 */
    private final String pngPathAfter  = ".png";

    /** x座標 */
    private int posX;
    /** y座標 */
    private int posY;

    /** マップ */
    private MapData mapData;

    /** イメージの2次元配列 */
    private Image[][] charaImages;
    /** イメージビューの配列 */
    private ImageView[] charaImageViews;
    /** イメージアニメーション(キャラを足踏みさせるクラス)の配列 *//
    private ImageAnimation[] charaImageAnimations;

    /** キャラの向きの番号 *//
	  private int charaDirection;

    /**
     * キャラを生成するときに呼ばれる処理(コンストラクタ)
     * @param startX 初期x座標
     * @param startY 初期y座標
     * @param mapData マップ
     */
    MoveChara(int startX, int startY, MapData mapData){
        // マップをmaoDataに代入
        this.mapData = mapData;

        // 4*3のイメージ2次元配列をcharaImagesに代入
        charaImages = new Image[4][3];
        // 4つ分のイメージビュー配列をcharaImageViewsに代入
        charaImageViews = new ImageView[4];
        // 4つ分のイメージアニメーション配列をcharaImageAnimationsに代入
        charaImageAnimations = new ImageAnimation[4];

        // キャラの画像を配列に設定
        for (int i=0; i<4; i++) {
            charaImages[i] = new Image[3];
            for (int j=0; j<3; j++) {
                charaImages[i][j] = new Image(pngPathBefore + directions[i] + animationNumbers[j] + pngPathAfter);
            }
            charaImageViews[i] = new ImageView(charaImages[i][0]);
            charaImageAnimations[i] = new ImageAnimation( charaImageViews[i], charaImages[i] );
        }
        
        // 初期x座標をposXに代入
        posX = startX;
        // 初期y座標をposYに代入
        posY = startY;

        // setCharaDirectonでキャラの向きを右に設定
        setCharaDirection(TYPE_RIGHT);
    }

    /**
     * charaDirectionのセッター
     * @param cd キャラの向きを示す番号
     */
    public void setCharaDirection(int cd){
        // キャラの向きを示す番号をcharaDirectionに代入
        charaDirection = cd;
        // 足踏みアニメーションを開始する
        for (int i=0; i<4; i++) {
            if (i == charaDirection) {
                charaImageAnimations[i].start();
            } else {
                charaImageAnimations[i].stop();
            }
        }
    }

    /**
     * 1マス先に進めるかの確認
     * @param dx x座標の増加分
     * @param dy y座標の増加分
     * @return 1マス先が空白の場合true そのほかの場合false
     */
    public boolean isMovable(int dx, int dy){
        // 1マス先の画像番号をみて空白の場合のみtrue
        if (mapData.getMap(posX+dx, posY+dy) == MapData.TYPE_WALL){
            return false;
        } else if (mapData.getMap(posX+dx, posY+dy) == MapData.TYPE_SPACE){
            return true;
        }
        return false;
    }

    /**
     * 1マス先にすすめる場合座標をその分変更
     * @param dx x座標の増加分
     * @param dy y座標の増加分
     * @return 進める場合true そのほかの場合false
     */
    public boolean move(int dx, int dy){
        // isMovableメソッドで進めるか確認して進める場合座標をその分変更
        if (isMovable(dx,dy)){
            posX += dx;
            posY += dy;
            return true;
        }else {
            return false;
        }
    }

    /**
     * charaImageViewsのゲッター
     * @return charaImageViews
     */
    public ImageView getCharaImageView(){
        return charaImageViews[charaDirection];
    }

    /**
     * posXのゲッター
     * @return posX
     */
    public int getPosX(){
        return posX;
    }

    /**
     * posYのゲッター
     * @return posY
     */
    public int getPosY(){
        return posY;
    }

    /**
     * 足踏みアニメーションをするクラス
     * @version 0.0
     */
    private class ImageAnimation extends AnimationTimer {

        /** イメージビュー */
        private ImageView charaView = null;
        /** イメージ配列 */
        private Image[] charaImages = null;
        /** キャラの向きの画像を切り替える番号 */
        private int index = 0;

        /** 切り替える間隔(0.5秒) */
        private long duration = 500 * 1000000L;
        /** 初期時間設定 */
        private long startTime = 0;

        /** 時間のカウンタ */
        private long count = 0L;
        /** ひとつ前のカウンタの値を保持　*/
        private long preCount;
        /** indexを増やすか減らすかの判定 */
        private boolean isPlus = true;

        /**
         * アニメーションが生成されたとき呼ばれる処理(コンストラクタ)
         * @param charaView キャラのイメージビュー
         * @param images キャラのイメージ配列
         */
        public ImageAnimation( ImageView charaView , Image[] images ) {
            // キャラのイメージビューをcharaViewに代入
            this.charaView = charaView;
            // キャラのイメージ配列をcharaImagesに代入
            this.charaImages = images;
            // 0をindexに代入
            this.index = 0;
        }

        /**
         * start()メソッドで呼ばれる・stop()メソッドで終わる処理
         * @param now 現在の時間
         */
        @Override
        public void handle( long now ) {
            // 最初のみstartTimeにnowを設定
            if( startTime == 0 ){ startTime = now; }
            
            // preCountに現在のcountの値を保持
            preCount = count;
            // nowとstartTimeとの差分を0.5で割ったものをcountに代入
            count  = ( now - startTime ) / duration;
            // 時間が変化した場合
            if (preCount != count) {
                // isPlusがtrueの場合indexを1増やす,そのほかの場合は1減らす
                if (isPlus) {
                    index++;
                } else {
                    index--;
                }
                // indexが0より小さいか2より大きい場合indexを１に設定し,isPlusを反転する
                if ( index < 0 || 2 < index ) {
                    index = 1;
                    isPlus = !isPlus;
                }
                // イメージビューのsetImageメソッドでindexに対応したキャラの向きの画像に切り替える
                charaView.setImage(charaImages[index]);
            }
        }
    }
}
```