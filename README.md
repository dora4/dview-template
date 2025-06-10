dview-template

![Release](https://jitpack.io/v/dora4/dview-template.svg)
--------------------------------

#### Gradle依赖配置

```groovy
// 添加以下代码到项目根目录下的build.gradle
allprojects {
    repositories {
        maven { url "https://jitpack.io" }
    }
}
// 添加以下代码到app模块的build.gradle
dependencies {
    implementation 'com.github.dora4:dview-template:1.0'
}
```

1. 修改settings.gradle的rootProject.name
2. 修改lib模块的build.gradle的namespace
3. 修改README.md文件的库名，并删除此步骤提示
