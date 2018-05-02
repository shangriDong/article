最近做了利用SoundPool播放在asserts里面的提示音， 但在debug版本中可以顺利播放， 但是release通过jenkins重新打包签名编译之后，就无法播放。报错：**This file can not be opened as a file descriptor; it is probably compressed。**

在google查了好长时间，有一个：http://stackoverflow.com/questions/6186866/java-io-filenotfoundexception-this-file-can-not-be-opened-as-a-file-descriptor 讲述一般情况不能播放的原因。

但该方法没有解决我的问题， 最后最后找到了问题根因，当我们在jenkins重新签名利用zip打包的时候，把我要播放的文件压缩了。导致AssetFileDescriptor afd = manager.openFd("sound3.ogg"); 该步骤出错，无法读取文件。不能播放。

最后解决办法：**在利用zip打包过程中，不将媒体文件进行压缩，即针对特定文件不进行压缩即解决该问题！！！！**

AudioManager.STREAM_MUSIC  /音乐回放即媒体音量/

AudioManager.STREAM_RING /铃声/

AudioManager.STREAM_ALARM  /警报/

AudioManager.STREAM_NOTIFICATION /窗口顶部状态栏通知声/

AudioManager.STREAM_SYSTEM  /系统/

AudioManager.STREAM_VOICECALL /通话 /

AudioManager.STREAM_DTMF /双音多频,不是很明白什么东西 /

以下内容 转载自：http://www.aichengxu.com/view/2449107

利用soundPool播放简短的音效是非常有用的,优点是它比mediaPlayer占用资源少,还可以同时播放多个音效等但是缺点就是对于音效的大小和持续时间等都有比较高的要求,据传音效的大小不能超过1M,持续时间不超过5秒才能播放,但经过实际测试,其实超过1M也没什么,但是不能超太多,超太多的话,播放会有问题,一般表现为没有声音,或者有时有声音有时没有,音效持续时间也可以超过5秒,测试时发现持续时间在10秒左右都能进行播放,但是持续时间很长的话,就会发生和上面类似的问题,所以还是要保证音效尽量小,持续时间尽量短为好.

以上是播放音效没声音的一种可能情况,其实音效没声音还有很多情况,

1,不支持的音效格式(官方建议音效的格式为ogg格式,其他格式比如MP3格式,wav格式等一样可以支持,其他格式暂不清楚)

2,文件放错位置,音效文件一般都放在asset目录下或者raw目录下,随代码一起打包成apk文件,下面会有代码示例,演示如何播放这两个文件夹里的音效

3,没有实现onLoadComplete()监听,(这种情况是非常常见的),如果想要什么时候用到音效什么时候加载音效的话,一般情况下是不会播放出音效的,因为soundPool的load()方法加载音效也是需要时间的,如果没有实现onLoadComplete()监听,而直接进行播放,那么音效还没加载完,就已经执行了播放方法,这样肯定没有声音,如果打印出soundPool.play()的返回值,那么返回值肯定是0,表示播放失败,如果播放成功,则返回对应的音效id

4,还有一种情况就是你的手机或者模拟器把音量关了,所以听不到声音,这种情况只要把声音调大就行了.

下面是演示从raw目录和asset目录获取音效的实例代码;


```
package com.rqq.soundpooldemo;

import android.content.res.AssetFileDescriptor;
import android.content.res.AssetManager;
import android.media.AudioManager;
import android.media.SoundPool;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;

import java.io.IOException;

public class MainActivity extends AppCompatActivity {

    private float Volume;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        AudioManager manager = (AudioManager) this.getSystemService(AUDIO_SERVICE);
        int maxVolume = manager.getStreamMaxVolume(AudioManager.STREAM_MUSIC);
        int currentVolume = manager.getStreamVolume(AudioManager.STREAM_MUSIC);
        Volume = currentVolume / (float) maxVolume;

    }

    /**
     * 从raw文件夹播放音效
     */
    public void click(View view) {
        //第一个参数为允许soundPool同时播放几个音效,这里只播放1个音效
        //第二个参数为播放的流的类型
        //第三个参数为播放的音效的质量,目前该值设为0即可
        SoundPool pool = new SoundPool(1, AudioManager.STREAM_MUSIC, 0);
        pool.load(this, R.raw.sound3, 1);
        pool.setOnLoadCompleteListener(new SoundPool.OnLoadCompleteListener() {
            @Override
            public void onLoadComplete(SoundPool soundPool, int sampleId, int status) {
                soundPool.play(sampleId, Volume, Volume, 1, 0, 1);
            }
        });
    }

    /**
     * 从asset文件夹播放音效
     */
    public void click1(View view) {
        try {
            AssetManager manager = getAssets();
            AssetFileDescriptor afd = manager.openFd("sound3.ogg");
            SoundPool pool = new SoundPool(1, AudioManager.STREAM_MUSIC, 0);
            pool.load(afd, 0);
            pool.setOnLoadCompleteListener(new SoundPool.OnLoadCompleteListener() {
                @Override
                public void onLoadComplete(SoundPool soundPool, int sampleId, int status) {
                    soundPool.play(sampleId, Volume, Volume, 1, 0, 1);
                }
            });
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

播放音效没声音的问题解决了,下面来探讨一下怎么提高音效的声音,

如果照着我上面的代码写的话,音效肯定能播放出来,但是有时可能会遇到音量较小的情况,为什么说是有时会遇到呢?因为这和用户的手机设置有关系.上面的代码中,在初始化soundPool的时候,第二个参数填写的是AudioManager.STREAM_MUSIC常量,这个常量监听的是手机的媒体音量,而用户可能有这个习惯,就是把媒体音量关了或者调低,以免播放音乐或电影时影响到别人,而这也就造成了播放音效时音效声音低的听不到,误认为是没有播放成功,所以要提高音效音量可以把手机的媒体音量调高即可,这种方式虽然能解决问题,但这并不是个好方法,因为你没法控制用户的喜好,那么怎么才能有效的提高音效的音量呢?其实也很简单,只要把AudioManager.STREAM_MUSIC常量换成AudioManager.STREAM_SYSTEM常量即可,AudioManager.STREAM_SYSTEM常量监听的是手机的系统音量,系统音量对于用户来说,很少设置成0,如果设置成0了,说明用户就是不想让手机发出任何声音,那么我们只要遵从就可以了,而只要不为0,那么我们的音效音量就会和用户的手机系统音量一样,这样手机的系统音量有多大我们的音效就有多大,不用担心手机系统音量小怎么办,因为现在的大部分手机,即使开了最小的系统音量,那声音也是很清楚的,OK,如何提高音效声音也说完了,如果还是感觉声音小的话,不如去更换一下常量,把AudioManager.STREAM_SYSTEM换成AudioManager.STREAM_VOICE_CALL(手机来电时的音量),AudioManager.STREAM_ALARM(闹钟音量)等.下面上修改后的代码:

```
package com.rqq.soundpooldemo;

import android.content.res.AssetFileDescriptor;
import android.content.res.AssetManager;
import android.media.AudioManager;
import android.media.SoundPool;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;

import java.io.IOException;

public class MainActivity extends AppCompatActivity {

    private float Volume;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        AudioManager manager = (AudioManager) this.getSystemService(AUDIO_SERVICE);
        int maxVolume = manager.getStreamMaxVolume(AudioManager.STREAM_MUSIC);
        int currentVolume = manager.getStreamVolume(AudioManager.STREAM_MUSIC);
        Volume = currentVolume / (float) maxVolume;

    }

    /**
     * 从raw文件夹播放音效
     */
    public void click(View view) {
        //第一个参数为允许soundPool同时播放几个音效,这里只播放1个音效
        //第二个参数为播放的流的类型,设置为系统音量类型
        //第三个参数为播放的音效的质量,目前该值设为0即可
        SoundPool pool = new SoundPool(1, AudioManager.STREAM_SYSTEM, 0);
        pool.load(this, R.raw.sound3, 1);
        pool.setOnLoadCompleteListener(new SoundPool.OnLoadCompleteListener() {
            @Override
            public void onLoadComplete(SoundPool soundPool, int sampleId, int status) {
                soundPool.play(sampleId, Volume, Volume, 1, 0, 1);
            }
        });
    }

    /**
     * 从asset文件夹播放音效
     */
    public void click1(View view) {
        try {
            AssetManager manager = getAssets();
            AssetFileDescriptor afd = manager.openFd("sound3.ogg");
            SoundPool pool = new SoundPool(1, AudioManager.STREAM_SYSTEM, 0);
            pool.load(afd, 0);
            pool.setOnLoadCompleteListener(new SoundPool.OnLoadCompleteListener() {
                @Override
                public void onLoadComplete(SoundPool soundPool, int sampleId, int status) {
                    soundPool.play(sampleId, Volume, Volume, 1, 0, 1);
                }
            });
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```