---
title: Android的“最近应用对话框”
author: Fan Fan
layout: post
permalink: /2011/01/09/android-recent-application-dialog/
dsq_thread_id:
  - 612782063
categories:
  - Graph
  - Mobile
---
这个对话框会列出应用的图标，当用小球把焦点移到某个图标时，这个图标会有种“光晕”的效果，如下图所示：

[<img class="alignnone size-full wp-image-878" title="device111" src="http://fkpwolf.net/WordPress/wp-content/uploads/2011/01/device111.png" alt="" width="320" height="480" />][1]

刚看到时想要如何画呢：确定图片的边界然后画上一条曲线？把放大后的图片和原来的图片叠加？模糊的边角看上去也很cool&#8230;

找一通后发现对话框的代码位置：$Droid_Src/frameworks/base/policy/src/com/android/internal/policy/impl/com/android/internal/policy/impl，图片处理的代码为IconUtilities.java。图简便就地使用ApiDemo里面的代码来做个小例子，主要代码如下。显示结果在上面的图片中。

<pre class="brush:java">int[] xy = new int[2];
Paint mBlurPaint = new Paint();
final float density = mDisplayMetrics.density;
final float blurPx = 5 * density;
mBlurPaint.setMaskFilter(new BlurMaskFilter(blurPx, BlurMaskFilter.Blur.NORMAL));
Bitmap mask = mBitmap.extractAlpha(mBlurPaint, xy);

mGlowColorFocusedPaint = new Paint();
mGlowColorFocusedPaint.setColor(0xffff8e00);
mGlowColorFocusedPaint.setMaskFilter(TableMaskFilter.CreateClipTable(0, 30));
canvas.drawBitmap(mask, 10, y, mGlowColorFocusedPaint);
canvas.drawBitmap(mBitmap, 10-xy[0], y-xy[1], new Paint());</pre>

上面TableMaskFilter在android定义为hidden，所以不能在自己的项目中用。我找个一个方法：在自己的project中重新定义一个同名同package的脚手架类，使上面代码编译通过即可。这个方法跟iPhone上面一样。

<pre class="brush:java">package android.graphics;

public class TableMaskFilter2 extends MaskFilter {

public TableMaskFilter2(byte[] table) {

}

public static TableMaskFilter2 CreateClipTable(int min, int max) {
return null;
}

public static TableMaskFilter2 CreateGammaTable(float gamma) {
return null;
}
}</pre>

代码很简单，和Photoshop的一些滤镜有点像&#8230;&#8230; 但是后面的原理现在还不清楚。

 [1]: http://fkpwolf.net/WordPress/wp-content/uploads/2011/01/device111.png