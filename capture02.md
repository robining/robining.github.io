<a href="../resources/videos/SVID_20180130_142339.mp4">dasa</a>

layout\activity_pager.xml
```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="200dp">

    <android.support.v4.view.ViewPager
        android:id="@+id/v_pager"
        android:layout_width="match_parent"
        android:layout_height="200dp" />

    <com.github.robining.modules.view.MaskView
        android:id="@+id/v_mask"
        android:layout_width="match_parent"
        android:layout_height="200dp" />
</FrameLayout>

```

src\main\PagerActivity.java
```
package com.github.robining.modules;

import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.drawable.ColorDrawable;
import android.os.Bundle;
import android.support.v4.view.PagerAdapter;
import android.support.v4.view.ViewPager;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.graphics.Palette;
import android.util.SparseIntArray;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;

import com.github.robining.modules.view.MaskView;

public class PagerActivity extends AppCompatActivity {

    int[] images = {R.mipmap.a1, R.mipmap.a2, R.mipmap.a3, R.mipmap.a4, R.mipmap.a5};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_pager);
        final ViewPager viewPager = findViewById(R.id.v_pager);
        final MaskView mask = findViewById(R.id.v_mask);
        final BannerAdapter adapter = new BannerAdapter();
        viewPager.setAdapter(adapter);
        viewPager.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {
            @Override
            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
                if(positionOffset > 0.5f){
                    position += 1;
                }
                int color = adapter.getThemeColor(position);
                mask.setThemeColor(color);
                if(getSupportActionBar() != null){
                    getSupportActionBar().setElevation(0);
                    getSupportActionBar().setBackgroundDrawable(new ColorDrawable(color));
                }
            }

            @Override
            public void onPageSelected(int position) {

            }

            @Override
            public void onPageScrollStateChanged(int state) {

            }
        });
    }

    private int getColor(Palette palette) {
        Palette.Swatch mutedDark = palette.getDarkMutedSwatch();//柔和的，暗色
        if (mutedDark != null) {
            return mutedDark.getRgb();
        }

        Palette.Swatch vibrantDark = palette.getDarkVibrantSwatch();//有活力的，暗色
        if (vibrantDark != null) {
            return vibrantDark.getRgb();
        }

        Palette.Swatch muted = palette.getMutedSwatch();//柔和的
        if (muted != null) {
            return muted.getRgb();
        }

        Palette.Swatch mutedLight = palette.getLightMutedSwatch();//柔和的,亮色
        if (mutedLight != null) {
            return mutedLight.getRgb();
        }
        Palette.Swatch vibrant = palette.getVibrantSwatch();//有活力的
        if (vibrant != null) {
            return vibrant.getRgb();
        }

        Palette.Swatch vibrantLight = palette.getLightVibrantSwatch();//有活力的，亮色
        if (vibrantLight != null) {
            return vibrantLight.getRgb();
        }

        return Color.GRAY;
    }

    private class BannerAdapter extends PagerAdapter {
        private SparseIntArray themeColors = new SparseIntArray();

        @Override
        public int getCount() {
            return images.length;
        }

        @Override
        public boolean isViewFromObject(View view, Object object) {
            return view == object;
        }

        @Override
        public Object instantiateItem(ViewGroup container, int position) {
            ImageView imageView = new ImageView(container.getContext());
            imageView.setScaleType(ImageView.ScaleType.CENTER_CROP);
            Bitmap bitmap = BitmapFactory.decodeResource(getResources(), images[position]);
            Palette palette = new Palette.Builder(bitmap).generate();
            int color = getColor(palette);
            themeColors.put(position, color);
            imageView.setImageBitmap(bitmap);
            container.addView(imageView);
            ViewPager.LayoutParams layoutParams = (ViewPager.LayoutParams) imageView.getLayoutParams();
            layoutParams.width = ViewGroup.LayoutParams.MATCH_PARENT;
            layoutParams.height = ViewGroup.LayoutParams.MATCH_PARENT;
            imageView.setLayoutParams(layoutParams);
            return imageView;
        }

        @Override
        public void destroyItem(ViewGroup container, int position, Object object) {
            container.removeView((View) object);
        }

        public int getThemeColor(int position) {
            return themeColors.get(position, Color.GRAY);
        }
    }
}
```

MaskView.java
```
package com.github.robining.modules.view;

import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.LinearGradient;
import android.graphics.Paint;
import android.graphics.Shader;
import android.support.annotation.Nullable;
import android.util.AttributeSet;
import android.view.View;

/**
 * Created by LuoHaifeng on 2018/1/30 0030.
 * Email:496349136@qq.com
 */

public class MaskView extends View {
    int[] colors = {Color.TRANSPARENT,Color.TRANSPARENT,Color.TRANSPARENT,Color.TRANSPARENT};
    float[] positions = {0.0F,0.5F,0.7F,1.0F};
    Paint paint = new Paint(Paint.ANTI_ALIAS_FLAG);
    public MaskView(Context context) {
        super(context);
    }

    public MaskView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    public MaskView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        LinearGradient linearGradient = new LinearGradient(0,0,0,getHeight(),colors,positions, Shader.TileMode.CLAMP);
        paint.setShader(linearGradient);
        canvas.drawRect(0,0,getWidth(),getHeight(),paint);
    }

    public void setThemeColor(int color){
        this.colors[0] = this.colors[3] = color;
        postInvalidate();
    }
}
```
