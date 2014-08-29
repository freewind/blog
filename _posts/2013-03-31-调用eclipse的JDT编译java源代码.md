---
layout: post
title: 调用eclipse的JDT编译java源代码
tags: Java
date: 2013-03-31 20:06:36
---


Play的源代码是一个宝库，从里面学到了很多。

Play为了能够对java源代码的编译过程进行控制，以实现一些魔术效果，使用了eclipse的JDT来手动进行编译。今天我把相关的代码抽出来，写成了一个独立运行的例子，并且成功地将指定目录下的java源文件编译成了.class文件。

当我们安装了eclipse后，它里面就自带了jdt的库，并且以jar的形式提供了字节码文件和源文件。以我下载的eclipse 4.2为例，可以在其plugins目录下找到：

1.  org.eclipse.jdt.core_3.8.3.v20130121-145325.jar2.  org.eclipse.jdt.core_3.8.3_source.v20130121-145325.jar

前者是字节码，后者是源文件。最好把两个都拷贝到项目中，因为在调用ecilpse相关的类时，肯定需要查看源代码和注释。

org.eclipse.jdt包下还有其它几个jar，不过对于编译这个基本功能来说，都用不上。

## 这里先简单的描述一下jdt编译的过程

我们需要传入一些参数生成一个org.eclipse.jdt.internal.compiler.Compiler的实例，然后调用其compile方法，就可以编译指定的java源文件。“如何编译”这些复杂的事情，都由JDT内部解决了，由我们需要需要帮助它寻找到正确的信息（如类、包等）。

首先看一下Compiler庞大的构造函数：

<pre>public Compiler(
	INameEnvironment environment,
	IErrorHandlingPolicy policy,
	CompilerOptions options,
	final ICompilerRequestor requestor,
	IProblemFactory problemFactory) {
}```

每个参数都有自己的作用，这里一一道来。

**INameEnvironment**

我们需要自己写个类实现该接口，它用于根据JDT传过来的包名类名，找到正确的字节码或者源文件给它。比如它会传过来类似于java.lang.String这样的数据，我们就需要调用classloader.getResourceAsString(“java/lang/String.class”)找到正确的字节码文件对应的二进制数据，回传过去。如果传来的是我们自己的文件路径，比如"aaa.BBB"，那我们就要去寻找自己指定的目录下的"aaa/BBB.java"文件，把该文件传过去，让它继续编译。

**IErrorHandlingPolicy **

遇到错误时怎么办。一般采用DefaultErrorHandlingPolicies.exitOnFirstError()，即遇到第一个错误就退出。

**CompilerOptions **

控制编译的参数，比如OPTION_LineNumberAttribute，OPTION_SourceFileAttribute，OPTION_LocalVariableAttribute等等，可让JDT在编译时，是否保留某些信息。

对于上面的三个参数，Play都指定为GENERATE，让JDT在字节码中尽可能多的保留源文件中的信息，以方便后面使用javassist进行字节码增强。

**ICompilerRequestor **

取回编译结果。如果编译成功，则可以拿到编译后的字节码二进制数据，否则可以拿到错误信息。

**IProblemFactory **

控制错误信息的locale、格式等。这里可直接使用new DefaultProblemFactory(Locale.ENGLISH)，即返回英文的错误信息。

**ICompilationUnit**

另外，我们还要自己实现一个ICompilationUnit。当我们把一个java源文件传给JDT时，不是简单地传一个File过去，而是用ICompilationUnit包装起来，以方便JDT取得文件名、类名和源代码等数据。由于ICompilationUnit并不要求一定得是一个文件，所以我们还可以直接在程序中返回源代码。

## 代码

下面是具体的代码，需要注释说明的地方都已经注明，另外强烈建议查看JDT相关类的注释，写得很详细：

<pre>import org.apache.commons.io.FileUtils;
import org.apache.commons.io.IOUtils;
import org.eclipse.jdt.core.compiler.IProblem;
import org.eclipse.jdt.internal.compiler.ClassFile;
import org.eclipse.jdt.internal.compiler.CompilationResult;
import org.eclipse.jdt.internal.compiler.Compiler;
import org.eclipse.jdt.internal.compiler.DefaultErrorHandlingPolicies;
import org.eclipse.jdt.internal.compiler.ICompilerRequestor;
import org.eclipse.jdt.internal.compiler.IErrorHandlingPolicy;
import org.eclipse.jdt.internal.compiler.IProblemFactory;
import org.eclipse.jdt.internal.compiler.ast.CompilationUnitDeclaration;
import org.eclipse.jdt.internal.compiler.classfmt.ClassFileReader;
import org.eclipse.jdt.internal.compiler.classfmt.ClassFormatException;
import org.eclipse.jdt.internal.compiler.env.ICompilationUnit;
import org.eclipse.jdt.internal.compiler.env.INameEnvironment;
import org.eclipse.jdt.internal.compiler.env.NameEnvironmentAnswer;
import org.eclipse.jdt.internal.compiler.impl.CompilerOptions;
import org.eclipse.jdt.internal.compiler.problem.DefaultProblemFactory;

import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.util.HashMap;
import java.util.Locale;
import java.util.Map;

/**
 * User: Freewind
 * Date: 13-3-31
 * Time: 下午5:22
 * Blog: http://freewind.me
 */
public class CompileWithJDT {

    private static final File CURRENT = new File("X:\\WORKSPACE\\dive_in_play\\EclipseJDT\\data");
    private static final File SOURCES_DIR = new File(CURRENT, "sources");
    private static final File BYTECODES_DIR = new File(CURRENT, "bytecodes");

    public static void main(String[] args) {

        /**
         * To find types ...
         */
        INameEnvironment nameEnvironment = new INameEnvironment() {

            /**
             * @param compoundTypeName {{'j','a','v','a'}, {'l','a','n','g'}}
             */
            public NameEnvironmentAnswer findType(final char[][] compoundTypeName) {
                return findType(join(compoundTypeName));
            }

            public NameEnvironmentAnswer findType(final char[] typeName, final char[][] packageName) {
                return findType(join(packageName) + "." + new String(typeName));
            }

            /**
             * @param name like `aaa`,`aaa.BBB`,`java.lang`,`java.lang.String`
             */
            private NameEnvironmentAnswer findType(final String name) {
                System.out.println("### to find the type: " + name);

                // check data dir first
                File file = new File(SOURCES_DIR, name.replace('.', '/') + ".java");
                if (file.isFile()) {
                    return new NameEnvironmentAnswer(new CompilationUnit(file), null);
                }

                // find by system
                try {
                    InputStream input = this.getClass().getClassLoader().getResourceAsStream(name.replace(".", "/") + ".class");
                    if (input != null) {
                        byte[] bytes = IOUtils.toByteArray(input);
                        if (bytes != null) {
                            ClassFileReader classFileReader = new ClassFileReader(bytes, name.toCharArray(), true);
                            return new NameEnvironmentAnswer(classFileReader, null);
                        }
                    }
                } catch (ClassFormatException e) {
                    // Something very very bad
                    throw new RuntimeException(e);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }

                System.out.println("### type not found: " + name);
                return null;
            }

            public boolean isPackage(char[][] parentPackageName, char[] packageName) {
                String name = new String(packageName);
                if (parentPackageName != null) {
                    name = join(parentPackageName) + "." + name;
                }

                File target = new File(SOURCES_DIR, name.replace('.', '/'));

                // only return false if it's a file
                // return true even if it doesn't exist
                return !target.isFile();
            }

            public void cleanup() {
            }
        };

        /**
         * Compilation result
         */
        ICompilerRequestor compilerRequestor = new ICompilerRequestor() {

            public void acceptResult(CompilationResult result) {
                // If error
                if (result.hasErrors()) {
                    for (IProblem problem : result.getErrors()) {
                        String className = new String(problem.getOriginatingFileName()).replace("/", ".");
                        className = className.substring(0, className.length() - 5);
                        String message = problem.getMessage();
                        if (problem.getID() == IProblem.CannotImportPackage) {
                            // Non sense !
                            message = problem.getArguments()[0] + " cannot be resolved";
                        }
                        throw new RuntimeException(className + ":" + message);
                    }
                }

                // Something has been compiled
                ClassFile[] clazzFiles = result.getClassFiles();
                for (int i = 0; i < clazzFiles.length; i++) {
                    String clazzName = join(clazzFiles[i].getCompoundName());

                    // save to disk as .class file
                    File target = new File(BYTECODES_DIR, clazzName.replace(".", "/") + ".class");
                    try {
                        FileUtils.writeByteArrayToFile(target, clazzFiles[i].getBytes());
                    } catch (IOException e) {
                        throw new RuntimeException(e);
                    }
                }
            }
        };

        IProblemFactory problemFactory = new DefaultProblemFactory(Locale.ENGLISH);
        IErrorHandlingPolicy policy = DefaultErrorHandlingPolicies.exitOnFirstError();

        /**
         * The JDT compiler
         */
        org.eclipse.jdt.internal.compiler.Compiler jdtCompiler = new Compiler(
                nameEnvironment, policy, getCompilerOptions(), compilerRequestor, problemFactory);

        // Go !
        jdtCompiler.compile(new ICompilationUnit[]{new CompilationUnit(new File(SOURCES_DIR, "aaa/BBB.java"))});
    }

    public static CompilerOptions getCompilerOptions() {
        Map<string  , string> settings = new HashMap();
        settings.put(CompilerOptions.OPTION_ReportMissingSerialVersion, CompilerOptions.IGNORE);
        settings.put(CompilerOptions.OPTION_LineNumberAttribute, CompilerOptions.GENERATE);
        settings.put(CompilerOptions.OPTION_SourceFileAttribute, CompilerOptions.GENERATE);
        settings.put(CompilerOptions.OPTION_ReportDeprecation, CompilerOptions.IGNORE);
        settings.put(CompilerOptions.OPTION_ReportUnusedImport, CompilerOptions.IGNORE);
        settings.put(CompilerOptions.OPTION_Encoding, "UTF-8");
        settings.put(CompilerOptions.OPTION_LocalVariableAttribute, CompilerOptions.GENERATE);
        String javaVersion = CompilerOptions.VERSION_1_5;
        if (System.getProperty("java.version").startsWith("1.6")) {
            javaVersion = CompilerOptions.VERSION_1_6;
        } else if (System.getProperty("java.version").startsWith("1.7")) {
            javaVersion = CompilerOptions.VERSION_1_7;
        }
        settings.put(CompilerOptions.OPTION_Source, javaVersion);
        settings.put(CompilerOptions.OPTION_TargetPlatform, javaVersion);
        settings.put(CompilerOptions.OPTION_PreserveUnusedLocal, CompilerOptions.PRESERVE);
        settings.put(CompilerOptions.OPTION_Compliance, javaVersion);
        return new CompilerOptions(settings);
    }

    private static class CompilationUnit implements ICompilationUnit {

        private File file;

        public CompilationUnit(File file) {
            this.file = file;
        }

        @Override
        public char[] getContents() {
            try {
                return FileUtils.readFileToString(file).toCharArray();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }

        @Override
        public char[] getMainTypeName() {
            return file.getName().replace(".java", "").toCharArray();
        }

        @Override
        public char[][] getPackageName() {
            String fullPkgName = this.file.getParentFile().getAbsolutePath().replace(SOURCES_DIR.getAbsolutePath(), "");
            fullPkgName = fullPkgName.replace("/", ".").replace("\\", ".");

            if (fullPkgName.startsWith(".")) fullPkgName = fullPkgName.substring(1);

            String[] items = fullPkgName.split("[.]");
            char[][] pkgName = new char[items.length][];
            for (int i = 0; i < items.length; i++) {
                pkgName[i] = items[i].toCharArray();
            }
            return pkgName;
        }

        @Override
        public boolean ignoreOptionalProblems() {
            return false;
        }

        @Override
        public char[] getFileName() {
            return this.file.getName().toCharArray();
        }
    }

    private static String join(char[][] chars) {
        StringBuilder sb = new StringBuilder();
        for (char[] item : chars) {
            if (sb.length() > 0) {
                sb.append(".");
            }
            sb.append(item);
        }
        return sb.toString();
    }

}```

我在代码中，要求它编译sources目录下的aaa包下的BBB.java文件。它们的源代码如下：

**aaa/BBB.java**

<pre>package aaa;

public class BBB {
    public static void main(String[] args) {
        new CCC().hello("JDT");
    }
}```

**aaa/CCC.java**

<pre>package aaa;

public class CCC {

    public void hello(String name) {
        System.out.println("Hello, " + name);
    }

}```

运行成功后，将会成功的在bytecodes目录下，生成BBB.class和CCC.class文件。

