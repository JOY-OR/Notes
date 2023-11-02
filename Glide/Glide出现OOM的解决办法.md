### Glide出现OOM的解决办法

- 引入largeHeap属性，让系统为APP分配更多的独立内存
- 禁止Glide内存缓存，设置skipMemoryCache(true)
- 自定义GlideModule，设置MemoryCache和BitmapPool大小
- 升级到4.0，使用asDrawable代替asBitmap，drawable更省内存
- ImageView的scaleType为fitXY的时候，改为fitCenter、centerCrop、fitStart、fitEnd显示
- 不适用application作为context。当为Application的时候，ImageView的生命周期会延长到整个运行过程中，ImageView不能被回收，从而造成OOM异常
- 使用ApplicationThread作为context，但是对ImageView使用弱引用或者是软引用，尽量使用softReference，当内存不足时，将及时回收无用的ImageView
- 当列表在滑动的时候，调用Glide的pauseRequest()取消请求，滑动停止时，调用resumeRequest()恢复请求
- try-cache某些大内存分配时，考虑在cache里面尝试一次降级的内存分配操作。例如decode bitmap的时候，cache到OOM，可以尝试把采样比例在此增加一倍之后，在此尝试decode
- BitmapFactory.Options和BitmapFactory.decodeStream获取原始图片的宽、高，绕过Java层加载Bitmap，再调用Glide的override(width,height)控制显示。
- 图片局部加载。参考：SubsamplingScaleImageView，先将图片下载到本地，然后去加载，只加载当前可视区域，在手指拖动的时候再去加载另外的区域。