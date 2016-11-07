# MediaPlayer-
最近做项目，被MediaPlayer的各种坑搞得特头疼，于是在这里总结一下


1.MediaPlayer底层并不是由Java编写的，而是由C++编写，我们许多经验性操作也许会抛出不可预知的异常（比如IllegalStateException）。
2.MediaPlayer在不运行时有两种状态，在刚new或者是reset()时，处于Idle状态，在release()之后，处于End状态。在Idle状态下，进行许多操作诸如：
getCurrentPosition(), getDuration(), getVideoHeight(), getVideoWidth(), setAudioStreamType(int), setLooping(boolean), 
setVolume(float, float), pause(), start(), stop(), seekTo(int), prepare()等操作，会抛出异常。
3.所以在此建议：一旦一个MediaPlayer对象不再被使用，应立即调用release()方法来释放在内部的播放引擎中与这个MediaPlayer对象关联的资源。
资源可能包括如硬件加速组件的单态组件，若没有调用release()方法可能会导致之后的MediaPlayer对象实例无法使用这种单态硬件资源，从而退回到软件
实现或运行失败。一旦MediaPlayer对象进入了End状态，它不能再被使用，也没有办法再迁移到其它状态。
4.关于Error状态：一旦发生错误，MediaPlayer对象会进入到Error状态，但是可以通过调用reset()将一个Error状态的MediaPlayer重置为Idle状态。
5.关于Initialized状态：调用setDataSource(FileDescriptor)方法，或setDataSource(String)方法，或setDataSource(Context，Uri)方法，或setDataSource(FileDescriptor，long，long)方法会使处于Idle状态的对象迁移到Initialized状态。
6.关于Prepare状态：当MediaPlayer对象处于Prepared状态的时候，可以调整音频/视频的属性，如音量，播放时是否一直亮屏，循环播放等。 
7.关于Started状态：当调用start()方法成功后，MediaPlayer会处于Started状态。可以使用isPlaying()方法检测一个MediaPlayer是否处于Started状
态，但是若是非法调用，则会引起IllegalStateException异常。 对一个已经处于Started 状态的MediaPlayer对象调用start()方法没有影响。
8.关于Paused状态：当调用pause()方法并返回时，会使MediaPlayer对象进入Paused状态。注意Started与Paused状态的相互转换在内部的播放引擎中是异
步的。所以可能需要一点时间在isPlaying()方法中更新状态，若在播放流内容，这段时间可能会有几秒钟。对一个已经处于Paused状态的MediaPlayer对象pause()方法没有影响。
