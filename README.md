# MediaPlayer-
最近做项目，被MediaPlayer的各种坑搞得特头疼，于是在这里总结一下


1.MediaPlayer底层并不是由Java编写的，而是由C++编写，我们许多经验性操作也许会抛出不可预知的异常（比如IllegalStateException）。
2.MediaPlayer在不运行时有两种状态，在刚new或者是reset()时，处于Idle状态，在release()之后，处于End状态。在Idle状态下，进行许多操作诸如：
getCurrentPosition(), getDuration(), getVideoHeight(), getVideoWidth(), setAudioStreamType(int), setLooping(boolean), 
setVolume(float, float), pause(), start(), stop(), seekTo(int), prepare()等操作，会抛出异常。
3.所以在此建议：一旦一个MediaPlayer对象不再被使用，应立即调用release()方法来释放在内部的播放引擎中与这个MediaPlayer对象关联的资源。
资源可能包括如硬件加速组件的单态组件，若没有调用release()方法可能会导致之后的MediaPlayer对象实例无法使用这种单态硬件资源，从而退回到软件
实现或运行失败。一旦MediaPlayer对象进入了End状态，它不能再被使用，也没有办法再迁移到其它状态。
