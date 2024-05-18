# Chapter-4
第四讲 复杂应用组件 Handler机制、多线程、 自定义View

包含了以下内容

1. ppt - 本地课程的讲义
2. assignment - 本次课程的作业：绘制一个桌面时钟，在Clock.java类中完成todo部分

---

### 输出结果

![QQ录屏20240518212259](https://github.com/longliveassa/Chapter-4/assets/117608033/0f82dd57-db9c-4c91-af02-9b3f2933f6db)


### 关键代码


``` java
 

    private Handler handler = new Handler();
    private Runnable runnable = new Runnable() {
        @Override
        public void run() {
            invalidate();
            handler.postDelayed(this, 1000); // 每秒刷新一次
        }
    };
```

```java
    private void init(Context context, AttributeSet attrs) {

        this.degreesColor = DEFAULT_PRIMARY_COLOR;

        mNeedlePaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        mNeedlePaint.setStyle(Paint.Style.FILL_AND_STROKE);
        mNeedlePaint.setStrokeCap(Paint.Cap.ROUND);

         
        handler.post(runnable); // 启动定时任务

    }

        private void drawHoursValues(Canvas canvas) {
        // Default Color:
        // - hoursValuesColor
        //有60个刻度，各5个刻一个
        Paint paint = new Paint(Paint.ANTI_ALIAS_FLAG);
        paint.setColor(Color.WHITE);
        paint.setTextSize(mWidth * 0.05f); // 设置文本大小
        paint.setTextAlign(Paint.Align.CENTER); // 设置文本对齐方式

        // 半径设置为比刻度短一些，以便将数字绘制在表盘内侧
        int radius = mCenterX - (int) (mWidth * 0.1f);

        // 遍历每个小时并绘制
        for (int i = 0; i < 12; i++) {
            // 计算每个小时的位置，0小时位于正上方
            float angle = (float) (i * 30); // 每小时间隔30度
            float angleInRadians = (float) Math.toRadians(angle);

            // 计算文本的位置
            float x = (float) (mCenterX + radius * Math.sin(angleInRadians));
            float y = (float) (mCenterY - radius * Math.cos(angleInRadians));

            // 绘制小时数字（1-12）
            canvas.drawText(String.valueOf(i == 0 ? 12 : i), x, y + paint.getTextSize() / 3, paint);
        }

    }


        private void drawNeedles(final Canvas canvas) {
        Calendar calendar = Calendar.getInstance();
        Date now = calendar.getTime();
        int nowHours = now.getHours();
        int nowMinutes = now.getMinutes();
        int nowSeconds = now.getSeconds();
        // 画秒针
        drawPointer(canvas, 2, nowSeconds);
        // 画分针
        // todo 画分针
        drawPointer(canvas, 1, nowMinutes);
        // 画时针
        int part = nowMinutes / 12;
        drawPointer(canvas, 0, 5 * (nowHours+8) + part);
        System.out.println("part:"+part+" nowHours:"+nowHours);
 
    }


        private void drawPointer(Canvas canvas, int pointerType, int value) {

        float degree;
        float[] pointerHeadXY = new float[2];

        mNeedlePaint.setStrokeWidth(mWidth * DEFAULT_DEGREE_STROKE_WIDTH);
        switch (pointerType) {
            case 0:
                degree = value * UNIT_DEGREE;
                mNeedlePaint.setColor(Color.WHITE);
                pointerHeadXY = getPointerHeadXY(HOUR_POINTER_LENGTH, degree);
                break;
            case 1:
                // todo 画分针，设置分针的颜色
                degree = value * UNIT_DEGREE;
                mNeedlePaint.setColor(Color.BLACK);
                pointerHeadXY = getPointerHeadXY(MINUTE_POINTER_LENGTH, degree);
                break;
            case 2:
                degree = value * UNIT_DEGREE;
                mNeedlePaint.setColor(Color.GREEN);
                pointerHeadXY = getPointerHeadXY(SECOND_POINTER_LENGTH, degree);
                break;
        }


        canvas.drawLine(mCenterX, mCenterY, pointerHeadXY[0], pointerHeadXY[1], mNeedlePaint);
    }
```
