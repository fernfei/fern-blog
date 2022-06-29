---
title: Java通过JNI调用C++
date: 2022-06-30 01:46:45
tags: JNI
---

# JAVA通过JNI调用C++

### 0.前置条件与JNI预备知识

需要gcc编译器

| 字符              | java          | c++/c     |
| ----------------- | ------------- | --------- |
| V                 | void          | void      |
| Z                 | jboolean      | boolean   |
| I                 | jint          | int       |
| J                 | jlong         | long      |
| D                 | jdouble       | double    |
| F                 | jfloat        | float     |
| B                 | jbyte         | byte      |
| C                 | jchar         | char      |
| S                 | jshort        | short     |
| [I                | jintArray[]   | int[]     |
| [F                | jfloatArray[] | float[]   |
| [B                | jbyteArray    | byte[]    |
| [C                | jcharArray    | char[]    |
| [S                | jshortArray   | short[]   |
| [D                | jdoubleArray  | double[]  |
| [J                | jloneArray    | long[]    |
| [Z                | jbooleanArray | boolean[] |
| Ljava/lang/String | jstring       |           |
| 其他对象类推      |               |           |

```java
(Ljava/lang/String;I)Ljava/lang/String;  <===>  String helloJNI(String a,int b)
(II)V <===> void helloJNI(int a,int b)
```

### 1.创建Java项目 代码如下

```java
public class JNITest {

    public native String helloJNI(String name,int age);

    public static void main(String[] args) {
        JNITest jniTest = new JNITest();
        String z3 = jniTest.helloJNI("z3", 18);
        System.out.println(z3);
    }
}
```

关于上述代码解释如下：native表示该函数的实现不是用java实现的，可能是C/C++等语言实现，

### 2.编写C++实现

#### 2.1 创建JNI.cpp文件

```c++
// “jni.h” 由先从当前目录去找jni.h
#include "jni.h"

jstring cppJNI(JNIEnv* env, jobject clazz, jstring name, jint age) {
	const char* chars = "hello java";
	const jstring result = env->NewStringUTF(chars);
	const char* nameChar = env->GetStringUTFChars(name, NULL);
	return result;
}

/*
	JVM启动时 会优先查看动态链接库中是否有JNI_OnLoad()函数
*/
JNIEXPORT jint JNI_OnLoad(JavaVM* vm, void* reserved) {
	JNIEnv* env;
	if (JNI_OK != vm->GetEnv(reinterpret_cast<void**>(&env), JNI_VERSION_1_8)) {
		printf("JNI_OnLoad cloud not get jni env");
		return JNI_ERR;
	}
	jclass clazz = env->FindClass("com/huf/JNITest");

	//通过 JNINativeMethod映射 c++函数与java函数直接的关系，从而不用在C++ 提供的JNI函数时的函数名不需要按照一定规则才能访问
	JNINativeMethod methods[] = {
		{"helloJNI","(Ljava/lang/String;I)Ljava/lang/String;",(void*)cppJNI},
	};

	if (env->RegisterNatives(clazz, methods, sizeof(methods) / sizeof((methods)[0])) < 0) {
		return JNI_ERR;
	}
	return JNI_VERSION_1_8;
}
```

#### 2.2 将jdk环境下的 jni.h与jni_md.h复制到出来

`<你的目录>\jdk8u322-b06\include\jni.h`

`<你的目录>\jdk8u322-b06\include\win32\jni_md.h`

![在这里插入图片描述](http://image.hi-hufei.com/typora/92becc5e7dcb4c84a0cdd8249be39dde.png)


#### 2.3 生成动态链接库

此步骤需要gcc编译器没有请自行安装

| windows | linux | mac    |
| ------- | ----- | ------ |
| dll     | so    | jnilib |

```linux
gcc -shared -o JNITest.dll .\JNI.cpp
```

执行成功会看见JNITest.dll文件

#### 2.4 修改java代码

```java
public class JNITest {

    static {
        System.loadLibrary("JNITest");
    }

    public native String helloJNI(String name,int age);

    public static void main(String[] args) {
        JNITest jniTest = new JNITest();
        String z3 = jniTest.helloJNI("z3", 18);
        System.out.println(z3);
    }
}

```

#### 2.5 执行java main函数测试

不出意外会出现如下错误：

```linux
no JNITest in java.library.path
```

因为它默认会去默认的路径找这个JNITest.dll库，所以我们需要手动指定

```linux
-Djava.library.path=E:\idea\jni\src\com\huf\
```
![在这里插入图片描述](http://image.hi-hufei.com/typora/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6byT5o2j54yr6IW7,size_20,color_FFFFFF,t_70,g_se,x_16.png)

#### 2.6 再次执行

```linux
hello java
```

#### 2.7 C++回调java

- c++代码如下

```c++
// pch.cpp: 与预编译标头对应的源文件
#include "jni.h"

JNIEnv* m_ENV = NULL;
JavaVM* m_JVM = NULL;
jobject object = NULL;

void callJava(const char* chars, int age) {
	bool flag = false;
	if (m_JVM->AttachCurrentThread((void**)&m_ENV, NULL) != JNI_OK) {
		fprintf(stderr, "AttachCurrentThread error\n");
		return;
	}
	flag = true;
	jclass clazz = m_ENV->GetObjectClass(object);
	//jclass clazz = m_env->FindClass("com/huf/JNITest");
	if (clazz == NULL) {
		fprintf(stderr, "GetObjectClass error\n");
		if (flag) {
			m_JVM->DetachCurrentThread();
		}
		return;
	}
	jmethodID method = m_ENV->GetMethodID(clazz, "callBack", "(Ljava/lang/String;I)V");
	if (NULL == method) {
		if (flag) {
			m_JVM->DetachCurrentThread();
		}
		fprintf(stderr, "GetMethodID error\n");
		return;
	}
	jobject object = m_ENV->AllocObject(clazz);
	jstring re = m_ENV->NewStringUTF(chars);
	m_ENV->CallVoidMethod(object, method, re, age);
	if (flag) {
		m_JVM->DetachCurrentThread();
	}
}

jstring cppJNI(JNIEnv* env, jobject clazz, jstring name, jint age) {
	const char* chars = "hello java";
	const jstring result = env->NewStringUTF(chars);
	const char* nameChar = env->GetStringUTFChars(name, NULL);
	object = clazz;
	callJava(nameChar, age);
	return result;
}

/*
	JVM启动时 会优先查看动态链接库中是否有JNI_OnLoad()函数
*/
JNIEXPORT jint JNI_OnLoad(JavaVM* vm, void* reserved) {
	JNIEnv* env;
	if (JNI_OK != vm->GetEnv(reinterpret_cast<void**>(&env), JNI_VERSION_1_8)) {
		printf("JNI_OnLoad cloud not get jni env");
		return JNI_ERR;
	}
	m_ENV = env;
	m_JVM = vm;
	jclass clazz = env->FindClass("com/huf/JNITest");

	//通过 JNINativeMethod映射 c++函数与java函数直接的关系，从而不用在C++ 提供的JNI函数时的函数名不需要按照一定规则才能访问
	JNINativeMethod methods[] = {
		{"helloJNI","(Ljava/lang/String;I)Ljava/lang/String;",(void*)cppJNI},
	};

	if (env->RegisterNatives(clazz, methods, sizeof(methods) / sizeof((methods)[0])) < 0) {
		return JNI_ERR;
	}
	return JNI_VERSION_1_8;
}
```

- java代码如下：

```java
public class JNITest {

    static {
        System.loadLibrary("JNITest");
    }

    public native String helloJNI(String name,int age);

    public void callBack(String name, int age) {
        System.out.println("c++ ======> java");
        System.out.printf("name : %s \n age : %d \n", name, age);
    }

    public static void main(String[] args) {
        JNITest jniTest = new JNITest();
        String z3 = jniTest.helloJNI("z3", 18);
        System.out.println(z3);
    }
}

```

#### 最后
可以将无用文件删除只留下如图
![在这里插入图片描述](http://image.hi-hufei.com/typora/5fab8c2fb1154021bceadefd0220478a.png)
