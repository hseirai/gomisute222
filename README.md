# gomisute222
gomisute222ソースコード

package com.example.gomisute222;
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.content.Context;
import android.content.res.Resources;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;
import android.graphics.Paint;
import android.view.MotionEvent;
import android.view.View;
import android.widget.TextView;

public class MainActivity extends Activity {
    /** Called when the activity is first created. */

    DroidAnimeView myView;

    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
       myView = new DroidAnimeView( getApplication() );
        setContentView(myView);
    }

}

/**
 * 描画用のクラス
 */
class DroidAnimeView extends View {
    private Paint myPaint = new Paint();
    private Bitmap myBitmap1, myBitmap2, beijing,gomi;
    private int displayWidth, displayHeight;
    private int imageWidth, imageHeight;
    private int x, y;
    private int dx, dy, dxx;
    private float gomiX=-50, gomiY=-50;
    private int gomiImageWidth, gomiImageHeight;
    private boolean isAttached;
    //private TextView textView;


    private static final long DELAY_MILLIS = 60;

    /**
     * コンストラクタ
     */
    public DroidAnimeView(Context c) {
        super(c);

        // イベントが取得できるようにFocusを有効にする

        Resources res = this.getContext().getResources();

        // 画像の読み込み
        myBitmap1 = BitmapFactory.decodeResource(res,
                                     R.drawable.gomibako);
        myBitmap2 = BitmapFactory.decodeResource(res,
                                     R.drawable.gomibako);
        beijing = BitmapFactory.decodeResource(res,
                R.drawable.beijing);
        gomi = BitmapFactory.decodeResource(res, R.drawable.gomi);


        // ゴミ箱の画像サイズの取得
        imageWidth = myBitmap1.getWidth();
        imageHeight = myBitmap1.getHeight();

        //ゴミ玉の画像サイズの取得
        gomiImageWidth = gomi.getWidth();
        gomiImageHeight = gomi.getHeight();


        // 座標、差分の初期化
        x = 0;
        y = 1300;
        dx = 30;
        dy = 1300;
        dxx = 30;

    }

    /**
     * 描画処理
     */
    protected void onDraw(Canvas canvas) {
        Bitmap myBitmap;


        // 画面を白色にする
        //canvas.drawColor(Color.BLUE);

        //バックグラウンド処理
        canvas.drawBitmap(beijing, 0, 0, myPaint);

        // 移動方向のチェック
        if ( dx < 0 ) {
            // 左向きの画像
            myBitmap = myBitmap1;
        } else {
            // 右向きの画像
            myBitmap = myBitmap2;
        }

        // 画像の描画
        canvas.drawBitmap(myBitmap, x, y, myPaint);

        //ゴミ玉の描画
        canvas.drawBitmap(gomi, gomiX, gomiY, myPaint);

    }

    //タッチイベント
    public boolean onTouchEvent(MotionEvent e){

    	switch (e.getAction()) {
		case MotionEvent.ACTION_DOWN:
			gomiX = e.getX()-gomiImageWidth/2;
			gomiY = e.getY()-gomiImageHeight/2;

			this.invalidate();
			break;

		default:
			break;
		}

    	return true;
    }

    /**
     * サイズが変更された時(縦から横モードになった時)に
     * 呼び出される
     */
    protected void onSizeChanged(int w, int h,
                                     int oldw, int oldh) {
        // 画面の縦と横のサイズを得る
        displayWidth = w;
        displayHeight = h;
    }

    /**
     * 移動処理
     */
    private void move() {
        // x方向の画面の端まで移動したかのチェック
        if (x < 0 || x + imageWidth > displayWidth) {
            // x方向の移動を反転
            dx = -dx;

            // y座標の移動
            y = dy;
        }

        // y方向の画面の端まで移動したかのチェック
        if (y < 0 || y + imageHeight > displayHeight) {
            // y方向の移動を反転
            dy = -dy;

            // y座標の移動
            y = dy;
        }

        //ゴミ玉の移動
        if(gomiY+gomiImageHeight > displayHeight){
        	gomiX = -50;

        }

        // x座標の移動
        x += dx;
        gomiY += dxx*2;

        //当たりの判断
        if (gomiY+gomiImageHeight/2 >= y) {
			if (gomiX+gomiImageWidth/2 >= x-imageWidth/2 || gomiX+gomiImageWidth/2 <= x+imageWidth/2) {

			} else {

			}
        }
    }

    /**
     * タイマーハンドラー
     */
    private Handler myHandler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            if (isAttached) {
                // 移動処理
                move();

                // 再描画
                invalidate();
                sendEmptyMessageDelayed(90, DELAY_MILLIS);
            }
        }
    };

    /**
     * WindowにAttachされた時の処理
     */
    protected void onAttachedToWindow() {
        isAttached = true;
        myHandler.sendEmptyMessageDelayed(0, DELAY_MILLIS);
        super.onAttachedToWindow();
    }

    /**
     * WindowからDetachされた時の処理
     */
    protected void onDetachedFromWindow() {
        isAttached = false;
        super.onDetachedFromWindow();
    }

}
