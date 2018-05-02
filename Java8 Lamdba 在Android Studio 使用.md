一 首先配置，使Android Studio支持Java8 Lamdba
由于这个插件是在 maven 仓库中，所以在 Application 的 build.gradle 文件中

```
buildscript {
    repositories {
        jcenter()

        // step 1
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.3.0'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
        
        // step 2
        classpath 'me.tatarka:gradle-retrolambda:3.4.0'
    }
}

allprojects {
    repositories {
        jcenter()
        
        // step 3
        mavenCentral()
    }
}
```

然后再到 Application 的 Module 模块中应用这个插件:

```
apply plugin: 'com.android.application'

//step 1
apply plugin: 'me.tatarka.retrolambda'

android {
    compileSdkVersion 25
    buildToolsVersion "25.0.2"
    defaultConfig {
        applicationId "com.dong.shangri.reflectiondemo"
        minSdkVersion 15
        targetSdkVersion 25
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }

    // step 2
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7:25.1.1'
    compile 'com.android.support.constraint:constraint-layout:1.0.2'
    testCompile 'junit:junit:4.12'
    
    //step3
    compile 'me.tatarka:gradle-retrolambda:3.4.0'
}
```

使用例子：

```
findViewById(R.id.get_constructor).setOnClickListener(v -> classForName());
```