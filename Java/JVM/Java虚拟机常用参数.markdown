# Java虚拟机常用参数

标签（空格分隔）： JVM

---

##类加载/卸载

* -verbose:class：跟踪类的加载与卸载
* -XX:+TraceClassLoading：跟踪类的加载
* --X:+TraceClassUnloading：跟踪类的卸载


**备注：**动态类的加载是非常隐蔽的，它们由代码逻辑控制，不出现在文件系统中，跟踪这些类，就需要使用-XX:TraceClassLoading等参数来观察系统实际使用的情况。

```Java
//UnloadClass 示例代码 
package com.wds.jvm.basic;

import jdk.internal.org.objectweb.asm.ClassWriter;
import jdk.internal.org.objectweb.asm.MethodVisitor;
import jdk.internal.org.objectweb.asm.Opcodes;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

/**
 * Created by wds on 2015/11/15.
 */
public class UnloadClass implements Opcodes {
    //private static final Logger LOGGER = LogManager.getLogger(UnloadClass.class);

    public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
        ClassWriter cw = new ClassWriter(jdk.internal.org.objectweb.asm.ClassWriter.COMPUTE_MAXS | ClassWriter.COMPUTE_FRAMES);
        cw.visit(V1_7, ACC_PUBLIC, "Example", null, "java/lang/Object", null);
        MethodVisitor mw = cw.visitMethod(ACC_PUBLIC, "<init>", "()V", null, null);
        mw.visitVarInsn(ALOAD, 0);
        mw.visitMethodInsn(INVOKESPECIAL, "java/lang/Object", "<init>", "()V");
        mw.visitInsn(RETURN);
        mw.visitMaxs(0, 0);
        mw.visitEnd();

        mw = cw.visitMethod(ACC_PUBLIC + ACC_STATIC, "main", "([LJava/lang/String;)V", null, null);
        mw.visitFieldInsn(GETSTATIC, "java/lang/System", "out", "Ljava/io/PrintStream;");
        mw.visitLdcInsn("Hello World");
        mw.visitMethodInsn(INVOKEVIRTUAL, "java/io/PrintStream", "println", "(Ljava/lang/String;)V");

        mw.visitInsn(RETURN);
        mw.visitMaxs(0, 0);
        mw.visitEnd();

        byte[] code = cw.toByteArray();

        for (int i = 0; i < 2; i++) {
            UnloadClassLoader unloadClass = new UnloadClassLoader();
            Method m = ClassLoader.class.getDeclaredMethod("defineClass", String.class, byte[].class, int.class, int.class);

            m.setAccessible(true);
            m.invoke(unloadClass, "Example", code, 0, code.length);
            m.setAccessible(false);

            unloadClass = null;
            //System.gc();
        }
    }
}

//UnloadClassLoader
package com.wds.jvm.basic;

public class UnloadClassLoader extends ClassLoader {

}

```
* -XX:+PrintClassHistogram：可在运行时打印、查看系统中类的分布情况，在系统启动时配置此参数，然后在Java控制中按下Ctrl+Break键，控制台上就会显示类信息的柱状图。

###系统参数查看
虚拟机支持众多参数，不同参数对执行的性能有较大影响，有必要明确当前系统的实际运行参数，JVM提供了如下参数，通过这些参数可以查看JVM运行时接受的参数。

* -XX:+PrintVMOptions：在程序运行时，打印虚拟机接受到的命令显示参数。

* -XX:PrintCommandLineFlags：打印传递给虚拟机的显示和隐式参数，隐式参数未必是通过命令行直接给出的，它可能是由虚拟机启动时自行设置的，

* -XX:PrintFlagsFinal：打印所有的系统参数的值。

###堆参数

* -Xms：指定初始堆大小  
* -Xmx：指定最大值大小  

**备注**：在实际工作中，为避免动态调整堆大小及减少垃圾回收次数，一般将-Xmx和-Xms的值设置为相同。

####新生代配置  

* -Xmm：设置新生代大小，此参数对系统性能影响较大，新生代的大小一般设置为整个堆空间的1/3到1/4。
* -XX:SurvivorRatio：设置新生代中eden空间和from/to空间的比例关系，含义如下：
```
-XX:SurvivorRatio = eden /from = eden /to
```
使用如下示例，练习堆的配置
```
/**
 * 分别使用4种参数执行本程序
 * 1、-Xmx20m -Xmx20m -Xmn1m -XX:SurvivorRatio=2 -XX:+PrintGCDetails
 * 1、-Xmx20m -Xmx20m -Xmn7m -XX:SurvivorRatio=2 -XX:+PrintGCDetails
 * 3、-Xmx20m -Xmx20m -Xmn15m -XX:SurvivorRatio=8 -XX:+PrintGCDetails
 * 4、-Xmx20m -Xmx20m -XX:NewRatio=2 -XX:+PrintGCDetails
 * Created by wds on 2015/11/16.
 */
public class EdenNewSize {

    public static void main(String[] args) {
        byte[] b = null;
        for (int i = 0; i < 10; i++) {
            b = new byte[1 * 1024 * 1024];
        }
    }

}

```
> **备注：**不同的堆分配情况，对系统执行产生一定影响。在实际工作中，应该根据系统的特点做合理的设置，基本策略是：尽可能将对象预留在新生代，减少老年代GC的次数。

* -XX:NewRatio=老年代/新生代

**注意：** -XX:SurvivorRatio设置的是eden区与survivor区的比例。-XX:NewRatio设置老年代与新生代的比例。

###堆溢出处理

* -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=path：可以设置在OOM时导出当前堆到path指定的路径。
* -XX:OnOutOfMemoryError=path：指定在OOM时执行脚本，可以是批文件或Shell脚本，用于程序自救、报警或通知。

###其它参数

-XX:PermSize：永久区初始化大小（已在1.8中去掉）
-XX:MaxPermSize:记久区最大Size（已在1.8中去掉）

**备注：**元数据区替换了永久区

-XX:MaxMetaspaceSize：指定最大元数据区（永久区）。

-Xss：指定线程的栈大小。

-XX:MaxDirectMemorySize：直接内存大小，默认值为最大堆空间，当直接使用内存量达到其指定的大小之后，会触发垃圾回收，如果不能回收，会引OOM。


