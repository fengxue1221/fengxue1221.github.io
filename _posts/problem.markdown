1. 关于测量MeasureSpec的问题
```java
    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        realHeight = 0;
        for (int i = 0; i < getChildCount(); i++) {
            View view = getChildAt(i);
            //TODO 这两行测量不懂
            heightMeasureSpec = MeasureSpec.makeMeasureSpec(MeasureSpec.getSize(heightMeasureSpec), MeasureSpec.UNSPECIFIED);
            measureChild(view, widthMeasureSpec, heightMeasureSpec);
            realHeight += view.getMeasuredHeight();
        }
        Log.i("parent: onMeasure", "realHeight = " + realHeight);
        //TODO 这里不懂
        setMeasuredDimension(MeasureSpec.getSize(widthMeasureSpec), MeasureSpec.getSize(heightMeasureSpec));
    }



    //TODO 这里不懂为什么要进行判断
    @Override
    public void scrollTo(int x, int y) {
        Log.i("onMeasure","y: " + y + ", getScrollY: " + getScrollY() + ", height: " + getHeight() + ", realHeight: " + realHeight+ ", -- " + (realHeight -getHeight()));
        if (y < 0)
        {
            y = 0;
        }
        if (y > realHeight)
        {
            y = realHeight;
        }
        if ( y != getScrollY())
        {
            Log.e("onMeasure","scrollTo: " + y);
            super.scrollTo(x, y);
        }
    }
```


