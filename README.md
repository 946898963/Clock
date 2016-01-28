# Clock
利用Canvas绘制的表盘界面，主要是学习利用Canvas的roate方法旋转坐标系，来方便我们绘制图形的方法，钟表界面如下所示 

![](http://github.com/946898963/Clock/raw/master/mdtupian/view.png)  


分析，要完成这样一个图形，可以将它分解成以下几个元素。
1 仪表盘    外面的大圆盘
2 刻度线    包括四个长的刻度线和其他短的刻度线
3 刻度值    它包括长刻度线对应的刻度值跟短刻度线对应的刻度值
4 指针        中间的指针，一粗一细两根指针

第一步  先画圆盘

     // 画外圆
     Paint paintCircle = new Paint();
     paintCircle.setStyle(Paint.Style.STROKE);
     paintCircle.setAntiAlias(true);
     paintCircle.setStrokeWidth(5);
     canvas.drawCircle(mWidth / 2,mHeight / 2, mWidth / 2, paintCircle);

第二步  画刻度线（难点）

        // 画刻度
        Paint painDegree = new Paint();
        paintCircle.setStrokeWidth(3);
        for (int i = 0; i < 24; i++) {
            // 区分整点与非整点
            if (i == 0 || i == 6 || i == 12 || i == 18) {
                painDegree.setStrokeWidth(5);
                painDegree.setTextSize(30);
                canvas.drawLine(mWidth / 2, mHeight / 2 - mWidth / 2,
                        mWidth / 2, mHeight / 2 - mWidth / 2 + 60,
                        painDegree);
                String degree = String.valueOf(i);
                canvas.drawText(degree,
                        mWidth / 2 - painDegree.measureText(degree) / 2,
                        mHeight / 2 - mWidth / 2 + 90,
                        painDegree);
            } else {
                painDegree.setStrokeWidth(3);
                painDegree.setTextSize(15);
                canvas.drawLine(mWidth / 2, mHeight / 2 - mWidth / 2,
                        mWidth / 2, mHeight / 2 - mWidth / 2 + 30,
                        painDegree);
                String degree = String.valueOf(i);
                canvas.drawText(degree,
                        mWidth / 2 - painDegree.measureText(degree) / 2,
                        mHeight / 2 - mWidth / 2 + 60,
                        painDegree);
            }
            // 通过旋转画布简化坐标运算
            canvas.rotate(15, mWidth / 2, mHeight / 2);
        }

思路，画线段，第一条线段好画，但是绘制其他的刻度线的时候，如果按照一般的方法是先计算角度，确定起始点坐标跟终点坐标，然后画线段，运算很复杂，这里我们选择使用Canvas的roate方法， 旋转坐标系。
将画布以圆心为圆点进行旋转，每当画好一条线，就旋转相应的角度。虽然下一次画线的时候，已然是第一根线的坐标，但是实际上我们把画布重新还原到旋转前的位置时，所有的刻度线就已经全部画好了。通过旋转画布，实际上是旋转了画布的坐标系，这就避免了复杂的三角函数运算。通过这样一种相对论似的变换，直接简化了绘图，这时再去绘制这些刻度线，就只需要区分整点跟非整点刻度线就可以了，代码如下所示。

第三部  画指针

        // 画圆心
        Paint paintPointer = new Paint();
        paintPointer.setStrokeWidth(30);
        canvas.drawPoint(mWidth / 2, mHeight / 2, paintPointer);
        // 画指针
        Paint paintHour = new Paint();
        paintHour.setStrokeWidth(20);
        Paint paintMinute = new Paint();
        paintMinute.setStrokeWidth(10);
        canvas.save();
        canvas.translate(mWidth / 2, mHeight / 2);
        canvas.drawLine(0, 0, 100, 100, paintHour);
        canvas.drawLine(0, 0, 100, 200, paintMinute);
        canvas.restore();

思路 通过translate方法，将坐标圆点从（0,0）位置平移到圆心，再一次为起点绘制线段
