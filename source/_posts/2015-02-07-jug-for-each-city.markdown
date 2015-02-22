---
layout: post
title: "JUG в каждый город"
date: 2015-02-07 00:58:39 +0300
comments: true
categories:
- java
- jug
---

{% img left http://habrastorage.org/getpro/habr/post_images/484/260/2a6/4842602a616898e5dc6d2718a1df1473.gif" %}
[Java User Groups](http://en.wikipedia.org/wiki/Java_User_Group")(JUGs) – это волонтерские организации Java-разработчиков призванные объединить Java-программистов, пользователей Java и IT-компании. Обычно объединенные по географическому признаку, например [Московское сообщество Java-разработчиков](http://jug.msk.ru) или [Белорусская Java User Group](http://www.belarusjug.org)</a>. Они предоставляют место встречи для пользователей Java, чтобы обмениваться информацией, обсуждать проблемы и решения из мира Java и, прежде всего, **_пить пиво, есть пиццу и получать удовольствие._**    
Как создать свой JUG и зачем это надо, читаем ниже.
<!-- more -->
{% img http://habrastorage.org/getpro/habr/post_images/c52/bb6/002/c52bb6002abc58c8819b37e0225bdd4f.jpg" %}  
*Типичный JUG event*

{% img http://habrastorage.org/getpro/habr/post_images/36d/c81/d2e/36dc81d2ee90a54a7dfbcc3f74ce371f.jpg" %}
*JUG Пакистан*

#####JUG в России
Давайте посмотрим на карту [Java User Groups International Map](https://www.java.net/jug-profile-map)
{% img http://habrastorage.org/getpro/habr/post_images/305/415/3e3/3054153e36146cbccf95c05a26480975.png" %}

Лично мне становится обидно, получается, на всей огромной территории нашей страны существует только Московский JUG, Питерский JUG и Омский JUG *(возможно есть и другие просто их нет на карте, но никакой информации о них я не нашел)*. Даже в Уганде и Лагосе есть свой JUG - регионы, ау!

#####Зачем это надо?
Всем известна ситуация на рынке труда, спрос на java – разработчиков очень высок, если в Москве еще реально найти «квалифицированные» кадры то в регионах это почти нереально. 
Периодически ко мне обращаются с такими вопросами:   
-- Привет, у тебя тут остались знакомые которые знают Java? А то проект намечается.  
*(«тут» в городе откуда я родом, сейчас я живу в Москве)*  
--  хм.. да, но они скорее всего заняты  
--  ну вдруг там подрос кто?  
….  

Люди уходят в фриланс, начинают стартапы и одному потянуть проект нереально. Привлечь в свои ряды гуру с 20-летним опытом вам врятли удастся, а вот начинающие, делающие первые шаги джуниоры, то что надо и площадка JUG отличное место для общения и заведения нужных знакомств.

#####Как создать свой JUG

* Для начала регистрируемся на [java.net](java.net)
* Далее переходим на [страницу создания](https://www.java.net//request_project.csp), знакомимся с требованиями к проекту и жмем *Create Project*
* Заполняем поле Project Name, это имя будет использоваться в URL имя_проекта.java.net и Description, выбираем Source License, жмем create.

После этого вам на почту придет поздравление с тем, что ваш проект успешно создан.

Итак, наш проект создан, давайте посмотрим что нам предоставляет java.net

   - багтрекер – можно выбрать JIRA или Bugzilla
   - репозиторий – subversion, mercurial или git
   - список рассылок
   - файловое хранилище
   - форум
   - чат
   - wiki
   - и еще какие-то мелочи

JUG создан, но на данный момент он является приватным, его нет в[Jugs List](https://www.java.net/jugs-list-alphabetical) и нет на [карте](https://www.java.net/jug-profile-map). Чтобы сделать проект публичным, нужно в свободной форме написать менеджеру *(Java.net Community Manager)* с просьбой открыть проект. Если все ок, через некоторое время (несколько часов) вам ответят и откроют проект.
Чтобы добавить ваш JUG на карту - [Java User Groups International Map](https://www.java.net/jug-profile-map) нужно на почту комъюнити менеджера отправить xml определенного вида, например:

``` xml
<Placemark>
        <name>Baikal Web Developer JUG</name>
        <description>
        <![CDATA[ 
            Up to a maximum of 250 characters of general group description text can precede
            the JUG Leader and JUG Site information.<br/>
            <b>Leaders:</b> <a href="http://ilinchik.ru">Anton Ilinchik</a> <br/>
            <b>Site:</b> https://baikal.java.net/
        ]]>
        </description>
        <Point>
            <coordinates>107.600000,51.833333,0</coordinates>
        </Point>
        <styleUrl>#jugStyle</styleUrl>
</Placemark>
```
И новый JUG появится на карте
{% img http://habrastorage.org/getpro/habr/post_images/f72/4a1/e82/f724a1e82bb9bf56aaf4f44abcb0a615.png" %}

Теперь ваш JUG официально зарегистрирован, он есть в списке и на карте, теперь дело за малым – готовим доклады, ищем спонсоров, закупаем пиво и пиццу и возможно вас ждет успех [BeJUG](www.bejug.org) (Belgium Java User Group) который перерос в огромную конференцию [Devoxx](http://www.devoxx.com/).  

Первоначально опубликовал этот пост на хабре, почитать оригинал можно [тут](http://habrahabr.ru/post/198910/) 
