# グループ９のレポジトリ

## コードの解説

### MapGame.java

```Java
// javafx.applicationパッケージのApplicationクラスをインポート
import javafx.application.Application;
// javafx.sceneパッケージのSceneクラスをインポート
import javafx.scene.Scene;
// javafx.scene.layoutパッケージのPaneクラスをインポート
import javafx.scene.layout.Pane;
// javafx.stageパッケージのStageクラスをインポート
import javafx.stage.Stage;
// javafx.fxmlパッケージのFXMLLoaderクラスをインポート
import javafx.fxml.FXMLLoader;

/**
* fxmlファイルをよみこんでゲームを開始するクラス
* @version 0.0
*/
public class MapGame extends Application {
    /** ステージ */
    Stage stage;

    /**
     * ゲームを起動するときの処理
     * @param primaryStage Sceneを設定するStage
     */
    @Override
    public void start(Stage primaryStage) throws Exception {
      // stageにprimarySyageを代入
      stage = primaryStage;
      // titleを設定
      primaryStage.setTitle("MAP GAME");
      // Paneにfxmlファイルを設定
      Pane myPane_top = (Pane)FXMLLoader.load(getClass().getResource("MapGame.fxml"));
      // SceneにPaneを設定
      Scene myScene = new Scene(myPane_top);
      // StageにSceneを設定
      primaryStage.setScene(myScene);
      // Stageを表示
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