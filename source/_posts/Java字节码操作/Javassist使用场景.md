---
title: Javassist使用场景
date: 2024-04-21 11:08:15
tags: Javassist
categories: Java
---

## Javassist使用场景

### 与Java Agent结合实现代码增强

#### premain方式

定义一个类，在里面添加premain方法，之后将该类打包为jar包

```java
public class JarAgent {
    public static void agentmain(String args, Instrumentation instrumentation) throws Exception {
        premain(args, instrumentation);
    }
    public static void premain(String args, Instrumentation instrumentation) throws Exception {
        ClassPool classPool = ClassPool.getDefault();
        CtClass ctClass = classPool.get("org.example.App");
        ctClass.defrost();
        CtMethod doOk = ctClass.getMethod("doOk", "()V");
        doOk.insertBefore("{System.out.println(\"change class success\");}");
        Class<?> redefineClass = null;
        // 如果类还没加载，则需要先加载
        Class<?> aClass = JarAgent.class.getClassLoader().loadClass("org.example.App");
        Class<?>[] allLoadedClasses = instrumentation.getAllLoadedClasses();
        for (Class<?> clz : allLoadedClasses) {
            if ("org.example.App".equals(clz.getName())) {
                redefineClass = clz;
            }
        }
        byte[] bytecode = ctClass.toBytecode();
        ClassDefinition classDefinition = new ClassDefinition(redefineClass, bytecode);
        instrumentation.redefineClasses(classDefinition);
        // 或者使用transformer
        ClassFileTransformer transformer = new CursomTransformer();
        instrumentation.addTransformer(transformer);
    }
    static class CustomTransformer implements ClassFileTransformer {
    @Override
    public byte[] transform(ClassLoader loader, String className, Class<?> classBeingRedefined, ProtectionDomain protectionDomain, byte[] classfileBuffer) throws IllegalClassFormatException {
        return classfileBuffer;
    }
}
```

同时在MANIFEST中需配置Premain-Class、Can-Redefine-Classes和Can-Retransform-Classes等信息

```
Manifest-Version: 1.0
Premain-Class: org.example.JarAgent
Can-Redefine-Classes: true
Can-Retransform-Classes: true
```

之后在对应程序启动时，添加启动参数

```shell
-javaagent:xxxx.jar
```

#### agentmain方式

和premain方式类似，这里需要在类中添加agentmain方法，之后将类打包为jar包

MANIFEST中需特殊配置Agent-Class属性

之后不需要添加特殊的启动参数，直接启动目标程序

最后通过VirtualMachine的attach方法，修改目标程序中的类

```java
public class Main {
    public static void main(String[] args) throws Exception {
        VirtualMachine vm;
        while (true) {
            List<VirtualMachineDescriptor> list = VirtualMachine.list();
            for (VirtualMachineDescriptor vmd : list) {
                System.out.println(vmd.displayName());
                // 通过类名查找对应的虚拟机
                if (vmd.displayName().equals("xxx")) {
                    VirtualMachine.attach(vmd);
                }
            }
            break;
        }
        // 直接通过进程id找到对应的虚拟机
        vm = VirtualMachine.attach("17348");
        vm.loadAgent("jaragent-1.0-SNAPSHOT.jar");
        vm.detach();
    }
```

如果运行该类时报错，可尝试加上启动参数-Xbootclasspath/a:，来设置运行时类的搜索路径

```shell
java -Xbootclasspath/a:xxx\tools.jar Main
```

