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
      // クラスフィールドのステージにprimarySyageを代入して初期状態を保持
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
    /** イメージビュー(画像を表示するjavafxのパーツ)の配列 */
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
        // イメージビューをマップのマス分生成してmapImageViews配列に代入
        mapImageViews = new ImageView[mapData.getHeight()*mapData.getWidth()];

        // マップのマス分ループを回してmapImageViews配列に画像データを代入
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
        /** キャラのx方向の位置 */
        int cx = c.getPosX();
        /** キャラのy方向の位置 */
        int cy = c.getPosY();
        // グリッドパネルを初期化
        mapGrid.getChildren().clear();
        // マップのマス分ループを回してグリッドパネルにキャラとマップの画像を設定
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
        // 条件分岐でH,J,K,LそれぞれのKeyCode対応したメソッドを呼ぶ
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
        // キャラmoveメソッドでキャラのy方向の位置を-1
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
        // キャラのmoveメソッドでキャラのy方向の位置を+1
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
        // キャラのmoveメソッドでキャラのx方向の位置を-1
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
        // キャラのmoveメソッドでキャラのx方向の位置を+1
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