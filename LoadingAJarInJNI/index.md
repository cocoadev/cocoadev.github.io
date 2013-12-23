---
layout: page
title: LoadingAJarInJNI
---

I realize this is cocadev and not jnidev, so if you have relevant links, fire away.  However, I'm doing this in Xcode and am ultimately using JNI to get in and out of objc (not that that matters)

I've never used JNI before and I'm trying to load classes from a jar.

Here's what I've got.

    
	JNIEnv *env;
	JavaVM *jvm;
	jint res;
	jclass cls;
	jmethodID mid;
	jstring jstr;
	jclass stringClass;
	jobjectArray args;
	
	JavaVMInitArgs vm_args;
	JavaVMOption options[1];
	options[0].optionString = "-Djava.class.path=/Users/me/Desktop/myJarFile.jar";
	vm_args.version = JNI_VERSION_1_4;
	vm_args.options = options;
	vm_args.nOptions = 1;
	vm_args.ignoreUnrecognized = JNI_TRUE;
	res = JNI_CreateJavaVM(&jvm, (void**)&env, &vm_args);
	if (res < 0) {
		fprintf(stderr, "Can't create Java VM\n");
		exit(1);
	
cls = (*env)->FindClass(env, "MyJavaClass");


What I get is the following:	*Exception in thread "main" java.lang.NoClassDefFoundError: MyJavaClass*.  What am I doing wrong?
(PS: Had a similar problem when trying to use the JavaBridge -- I couldn't find the classes in my Jar file.)

----

You should take a look at the source for Apple's simpleJavaLauncher example.

<http://developer.apple.com/samplecode/simpleJavaLauncher/listing2.html>

