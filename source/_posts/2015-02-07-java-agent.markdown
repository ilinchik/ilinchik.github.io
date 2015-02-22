---
layout: post
title: "Java Agent на службе JVM"
date: 2015-02-07 01:19:35 +0300
comments: true
categories: 
- java
- java agent
---

{% img left http://habrastorage.org/getpro/habr/post_images/0d6/cea/505/0d6cea5059f39fac1c6522285bd73794.png" %}
Наверное многие слышали или сталкивались с таким параметром JVM как -javaagent, увидеть этот параметр вы могли используя [Jrebel](http://zeroturnaround.com/software/jrebel) или [Plumbr](https://plumbr.eu) это могло выглядеть например так <code>JAVA_OPTS=-javaagent:[path/to/]jrebel.jar</code> или так <code>-javaagent:/path-to/plumbr.jar</code>
Хотя **javaagent** появился еще в версии java 1.5, многие разработчики так никогда и не использовали возможности агентов и имеют смутное представление что это такое.  
Что же это за агент? Зачем он может нам понадобиться и как написать свой?
<!-- more -->
##Что такое javaagent
Как я написал выше javaagent это один из параметров JVM, который позволяет указать агент который будет запущен с вашим приложением, а точнее он будет запущен еще перед запуском вашего приложения. Сам агент это отдельное приложение которое предоставляет доступ к механизму манипуляции байт-кодом (<code>java.lang.instrument</code>) в runtime. Это если вкратце. Официальную документацию можно почитать [тут](http://docs.oracle.com/javase/8/docs/api/java/lang/instrument/package-summary.html), но она довольно скудная. Ничего непонятно? Итак, давайте разбираться. Лучше всего разбираться на примерах.

##Напишем элементарный агент 
{% codeblock lang:java %}
package ru.ilinchik.agent;

public class Agent007 {
    public static void premain(String args) {
        System.out.println("Hello! I`m java agent");        
    }
}
{% endcodeblock %}
Обратите внимание, агент обязательно должен реализовывать метод **premain** со следующей сигнатурой  
<code>public static void premain(String args); </code>  
или  
<code>public static void premain(String args, Instrumentation inst);</code>  

Класс агента должен быть упакован в jar и содержать <code>MANIFEST.MF</code>, с обязательным атрибутом
**PreMain-Class** - указывает на класс агента с premain методом. Есть и другие атрибуты агента, но они необязательные и сейчас  нам не понадобятся.

Вот так будет выглядеть наш manifest.mf.
``` java
Manifest-Version: 1.0
PreMain-Class: ru.ilinchik.agent.Agent007

``` *не забудьте добавить перевод строки в конец файла*

Теперь упакуем все это в jar
```
jar -cvfm Agent007.jar manifest.mf  ru/ilinchik/agent/Agent007.class
```
И наконец класс испытатель
``` java
package ru.ilinchik.agent;

public class AgentTester {
    public static void main(String[] args) {
        System.out.println("Hello! I`m agent tester");
    }
}
```
Запускаем AgentTester из командной строки
``` 
java -javaagent:Agent007.jar ru.ilinchik.agent.AgentTester
Hello! I`m java agent
Hello! I`m agent tester
```
Из этого примера видно что:
<ul>
<li>метод <b>premain</b> исполняется еще до вызова метода <b>main</b> основного приложения.</li>
<li>агент указывается с помощью параметра <code>-javaagent:jarpath[=options]</code></li>
</ul>
##Давайте попробуем извлечь из агента какую-нибудь пользу
Вообще **_механизм агентов предназначен для манипуляции байт-кодом_**, но скажу сразу модифицировать байт-код в этой статье мы не будем иначе можно уйти далеко-далеко за пределы этого поста. Кому интересно можно посмотреть на <a href="http://www.csg.ci.i.u-tokyo.ac.jp/~chiba/javassist/">javassist</a> так как стандартных средств для работы с байт-кодом нет.

Напишем AgentCounter который будет выводить имя загружаемого класс и подсчитывать кол-во загруженных классов. Так мы сможем понаблюдать за работой classloader`a.

``` java
package ru.ilinchik.agent;

import java.lang.instrument.Instrumentation;

public class AgentCounter {
    public static void premain(String agentArgument, Instrumentation instrumentation) {
        System.out.println("Agent Counter");
        instrumentation.addTransformer(new ClassTransformer());
    }
}
```
Обратите внимание, теперь я использую другую сигнатуру метода premain. В объект instrumentation я передаю ClassTransformer который и выполняет всю работу. ClassTransformer будет срабатывать каждый раз при загрузке класса. Если вы хотите использовать свой ClassTransformer, вы должны реализовать интерфейс <code>java.lang.instrument.ClassFileTransformer</code> и добавить свой объект через метод <code>Instrumentation.addTransformer</code>

``` java
package ru.ilinchik.agent;

import java.lang.instrument.ClassFileTransformer;
import java.security.ProtectionDomain;

public class ClassTransformer implements ClassFileTransformer {
    
    private static int count = 0;
    
    @Override
    public byte[] transform(ClassLoader loader, 
                            String className,
                            Class<?> classBeingRedefined, 
                            ProtectionDomain protectionDomain,
                            byte[] classfileBuffer) {
        System.out.println("load class: " + className.replaceAll("/", "."));
        System.out.println(String.format("loaded %s classes", ++count));
        return classfileBuffer;
    }
}
```
classfileBuffer - это и есть байт-код текущего класса представленный в виде массива байт, для его переопределения трансформер должен вернуть новый массив байт, в данном примере мы не меняем содержимое класса поэтому просто возвращаем тот же массив.

Пакуем агент и трансформер в новый jar
``` 
jar -cvfm agentCounter.jar manifest.mf  ru/ilinchik/agent/AgentCounter.class ru/ilinchik/agent/ClassTransformer.class
```
Немного модифицируем класс тестер
``` java
package ru.ilinchik.agent;

public class AgentTester {
    public static void main(String[] args) {
        A a = new A();
        B b = new B();          
        C c = null;
    }
}

class A {};
class B {};
class C {};
```
Запускаем AgentTester c новым агентом
```
java -javaagent:agentCounter.jar ru.ilinchik.agent.AgentTester
Agent Counter
load class: sun.launcher.LauncherHelper
loaded 1 classes
load class: ru.ilinchik.agent.AgentTester
loaded 2 classes
load class: ru.ilinchik.agent.A
loaded 3 classes
load class: ru.ilinchik.agent.B
loaded 4 classes
```
*для разных версий java результаты могут отличаться*

Если запустить какое-нибудь enterprise приложение с таким агентом, можно получить довольно интересные результаты, например один из проектов после старта выдал мне следующее:
```
sun.reflect.GeneratedMethodAccessor230
loaded 33597 classes
java.rmi.server.Unreferenced
loaded 33598 classes
```
##Измеряем размер java объектов
Рассмотрим еще один пример использования агентов. Напишем класс который будет возвращать размер java объектов и javaagent будет играть ключевую роль. <b>Кто как ни JVM может знать реальный размер созданного объекта</b>, в интерфейсе <code>Instrumentation</code>  есть замечательный метод <code>long getObjectSize(Object objectToSize)</code> который возвращает размер объекта. Но как из нашего приложения получить доступ к агенту? А делать ничего особенного и не придется,<b> javaagent автоматически добавляется в classpath</b> и нам остается только добавить в агент поле типа Instrumentation instrumentation и инициализировать его в методе premain. 

``` java
package ru.ilinchik.agent;

import java.lang.instrument.Instrumentation;

public class AgentMemoryCounter {
    
    private static Instrumentation instrumentation;
    
    public static void premain(String args, Instrumentation instrumentation) {
        AgentMemoryCounter.instrumentation = instrumentation;
    }
    
    public static long getSize(Object obj) {
        if (instrumentation == null) {
            throw new IllegalStateException("Agent not initialised");
        }
        return instrumentation.getObjectSize(obj);
    }
}
```
Мы получаем доступ к методу <code>AgentMemoryCounter.getSize(obj)</code> из класса приложения. 
``` java
package ru.ilinchik.agent;

import java.math.BigDecimal;
import java.util.ArrayList;
import java.util.Calendar;

public class AgentTester {
    public static void main(String[] args) {
        printObjectSize(new Object());
        printObjectSize(new A());
        printObjectSize(1);
        printObjectSize("string");
        printObjectSize(Calendar.getInstance());
        printObjectSize(new BigDecimal("999999999999999.999"));
        printObjectSize(new ArrayList<String>());
        printObjectSize(new Integer[100]);
    }

    public static void printObjectSize(Object obj) {
        System.out.println(String.format("%s, size=%s", obj.getClass()
                .getSimpleName(), AgentMemoryCounter.getSize(obj)));
    }
}

class A {
    Integer id;
    String name;
}
```
Результаты работы приложения могу выглядеть следующим образом
```
java -javaagent:agentMemoryCounter.jar ru.ilinchik.agent.AgentTester
Agent Counter
Object, size=8
A, size=16
Integer, size=16
String, size=24
GregorianCalendar, size=112
BigDecimal, size=32
ArrayList, size=24
Integer[], size=416
```
Обратите внимание что метод <code>getObjectSize()</code> не учитывает размер вложенных объектов т.е учитывается только память затраченная на ссылку на объект.

##Заключение
Надеюсь этот пост помог понять предназначение javaagent-ов для тех кто никогда с ними не работал, также я пытался продемонстрировать альтернативное использование javaagent-а (не для трансформации байт-кода). А для чего вы используете в своих проектах агенты? Напишите в комментариях, было бы очень интересно.

Первоначально опубликовал этот пост на хабре, почитать оригинал можно [тут](http://habrahabr.ru/post/230239/) 

