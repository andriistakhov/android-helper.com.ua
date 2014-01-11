---
author: andrew
comments: true
date: 2012-08-02 08:02:27+00:00
layout: post
slug: static_code
title: Статические анализаторы кода для Android-приложения.
wordpress_id: 402
categories:
- Памятка разработчику
- Программирование
---

Добрый день друзья.





Сегодня нашел очень увлекательную [статью](http://derevyanko.blogspot.com/2012/01/android.html) которая помогает проанализировать коды вашей программы.





Надеюсь, что вам понравится!


<!-- more -->


А вот и описание:

























Одним из путей улучшения качества кода приложения является [регулярное](http://habrahabr.ru/blogs/code_review/135234/#habracut)применение статических анализаторов кода. Статический анализ кода позволяет находить ошибки в коде, отслеживать несоблюдение стандарта кодирования, находить "плохой" код, который может привести к проблемам в будущем, а так же неэффективный код, негативно сказывающийся на производительности в настоящем, строить метрики кода, находить полностью или частично одинаковые фрагменты кода и так далее. 





Для разработчиков, ведущих кодирование в одиночку, статические анализаторы вещь вообще неоценимая. Ведь им некому показать код своего приложения. Так что "электронный помощник", способный грамотно выполнить "code review", может им здорово пригодится.





Вопрос - какие статические анализаторы кода доступны разработчику Android приложений? Для Java статических анализаторов существует множество (см. список [lint ](http://en.wikipedia.org/wiki/Lint_(software))приложений для Java в википедии), но не все они умеют работать с Android-приложениями и, тем более, учитывать их специфику.





Мне удалось отыскать следующие статические анализаторы кода, которые умеют работать с Android-приложениями:








  * [Lint ](http://tools.android.com/tips/lint). "Родной" анализатор кода для Android, входит в состав Android SDK начиная с r16.



  * [FindBugs ](http://findbugs.sourceforge.net/)(бесплатный).



  * [Checkstyle ](http://checkstyle.sourceforge.net/)(бесплатный).



  * [CodePro Analytix ](http://code.google.com/intl/ru-RU/javadevtools/codepro/doc/index.html)(бесплатный).



  * [PMD ](http://pmd.sourceforge.net/)(бесплатный)



  * Motodev App Validator (бесплатный). Входит в состав среды разработки[MOTODEV Studio for Android ](http://developer.motorola.com/docstools/motodevstudio/). Есть [online ](http://developer.motorola.com/testing/app-validator/)вариант.



  * [Klocwork Solo ](http://www.klocwork.com/products/solo/)(платный). Доступна триальная версия



  * [JTest ](http://www.parasoft.com/jsp/products/jtest.jsp)от Parasoft (платный).



  * [Julia ](http://juliasoft.com/)(платный). Доступен бесплатный [online ](http://julia.scienze.univr.it/)вариант.






Далее представлены результаты сравнения всех этих анализаторов - какой анализатор что умеет, для чего предназначен, насколько удобно пользоваться, какие ошибки находит, насколько он полезен на практике. 





Чтобы было интереснее сравнивать, я "натравил" каждый из этих анализаторов на одно и то же тестовое приложение. В качестве такового я взял код одной из ранних версий моего приложения [Animated Widget Contact Launcher ](https://market.android.com/details?id=com.mobilityflow.awidget&hl=ru). Версию я выбрал годичной давности, багов и проблем в ней было вагон (с FindBugs на тот момент я был не знаком). Так что "что поискать" в ней точно было.





### Lint





[Lint ](http://tools.android.com/tips/lint)- анализатор кода, который поставляется вместе с Android SDK. Он появился совсем недавно, в версии r16. 





Lint нацелен на поиск проблем, связанных с ресурсами. Неиспользуемые ресурсы, ненужные или наоборот, отсутствующие, аттрибуты, неоптимальные конструкции в layout и т.д. Список проблем, которые ищет Lint, приведен [здесь ](http://tools.android.com/tips/lint-checks). 





Работать с Lint очень удобно. Вызывать и настраивать его можно прямо из Eclipse. Для найденных ошибок показывается объяснение - в чем состоит ошибка и как ее исправить. Ошибки делятся на 3 уровня (error, warning, information). 





При проверке тестового приложения был выдан 101 warning. Все по существу - ложных срабатываний не было, за исключением пары сообщений об неиспользуемости ресурсов (и это при отсутствии библиотек; если же библиотеки используются, то разработчики прямо предупреждают о возможности ложных сообщений, так что будьте осторожны при чистке кода).









![](http://3.bp.blogspot.com/-UJ2JPXSVb9s/TwVtraTmfMI/AAAAAAAAAFQ/R32F2qPO1uk/s320/lint2.png)









###  





### FindBugs





FindBugs я пользуюсь уже почти год и очень доволен. Находит реальные ошибки в коде. Вот полный [список проблем ](http://findbugs.sourceforge.net/bugDescriptions.html), которые он умеет находить.





В декабре 2011, после длительного перерыва, FindBugs обновился с версии 1.3.9 до 2.0. Субъективно новая версия стала точнее, удобнее и быстрее предыдущей. Настраивается она теперь прямо из Eclipse (важно: в свойствах проекта, а не в свойствах workspace). Плагин для eclipse устанавливается с помощью location URL: `http://findbugs.cs.umd.edu/eclipse/` (в Eclipse выбрать `Help\Install new software`, ввести location URL в поле Work with).









![](http://2.bp.blogspot.com/-dKUxauuPo5s/TwV0S48r2zI/AAAAAAAAAFc/0cjP_W2wTuM/s320/findbugs_settings.jpg)









FindBugs нашел в тестовом приложении 12 ошибок при дефолтном уровне чувствительности. При максимальной чувствительности ошибок нашлось 137. При этом ложных срабатываний было всего 10 (анализатору не понравился способ именования классов в автоматические генерируемом файле ресурсов; этот файл нужно просто исключить из обработки в настройках FindBugs). Подавляющее большинство остальных ошибок я исправил в более поздних версиях.









![](http://4.bp.blogspot.com/-hybSpfAErHs/TwV0q3gJQhI/AAAAAAAAAFo/Q_9jti2d1oI/s320/findbugs_sample.jpg)









Приведу примеры ошибок, которые нашел FindBugs:








  * `Should be a static inner class` (вложенный класс не сделан статичным, хотя доступ к внешнему классу ему не нужен).



  * `Should this field be static?` (константа не объявлена как static)



  * `Switch statement found where one case falls through to the next case`(пропущен break)



  * `Comparison of String parameter using == or != in` (строки в Java небходимо сравнивать через equal)






Специфичных для Android правил в FindBugs пока нет. Но, возможно, со временем они появятся - разработчики [готовы идти навстречу пожеланиям пользователей.](http://groups.google.com/group/openintents/browse_thread/thread/b3a520a90254ce7f?pli=1)





###  





### Checkstyle





[Checkstyle ](http://checkstyle.sourceforge.net/)нацелен на проверку соблюдения стандарта кодирования. Проверка соблюдения правил именования, правил расстановки скобочек, правил оформления кода и т.д. - вот цель этого анализатора. Полный список правил можно посмотреть в [документации ](http://checkstyle.sourceforge.net/config_coding.html). Плагин для eclipse устанавливается с помощью location URL: `http://eclipse-cs.sf.net/update/`





После анализа тестового приложения Checkstyle выдал 8741(!) ворнинг. Я внимательно просмотрел все типы ворнингов.. и не нашел для себя ничего полезного. На мой взгляд - это совершенно не тот инструмент, который необходим индивидуальным разработчикам и небольшим командам. С другой стороны, его активно используют и [дорабатывают ](http://sevntu-checkstyle.github.com/sevntu.checkstyle/), так что видимо область применения у него все же есть.









![](http://3.bp.blogspot.com/-2S9FFxInHYo/TwV4wSDKRhI/AAAAAAAAAF0/xlOO_lzrCSc/s320/checkstyle_settings.png)









###  





### CodePro Analytix





[CodePro Analytix ](http://code.google.com/intl/ru-RU/javadevtools/codepro/doc/index.html)- разработка компании Instantiations. Google купил эту компанию и [сделал эти продукт бесплатным ](http://googlewebtoolkit.blogspot.com/2010/09/google-relaunches-instantiations.html), за что ему большое спасибо.





Заявленные [возможности CodePro Analytix ](http://code.google.com/intl/ru-RU/javadevtools/codepro/doc/index.html)впечатляют. [Аудит кода ](http://code.google.com/intl/ru-RU/javadevtools/codepro/doc/features/audit/audit.html)на основе нескольких различных наборов правил ("The Elements of Java Style", "Effective Java", "Potential Errors and Refactoring", "Security" и т.д.). Измерение кода с помощью различных метрик. Поиск "дублей" в коде - результатов копипаста. И т.д.





Провел аудит кода тестового приложения используя все возможные наборы правил. Получил: 152 предупреждения с уровнем серьезности High, 1246 - medium, 1062 - low. Для сравнения - анализ с помощью единственного набора правил "Code Pro Core" выдал 64 medium и 4 low предупреждения.









![](http://4.bp.blogspot.com/-RD18joLpJgo/TwWG7-5R8EI/AAAAAAAAAGM/l3WFRuLTkG8/s320/codepro_audit.png)









Что-же нашел Code Pro? Частично, результаты поиска пересеклись с результатами FindBugs и Lint. Но только частично. Большую часть ошибок составили минорные проблемы в коде, которые не были обнаружены другими анализаторами. Например:








  * `String literal can be replaced by a character literal`. В StringBuilder вместо append("\n") использовать append('\n').



  * `Badly located array declarators`. Массив объявлен как "String abc[]" вместо "String[] abc".



  * `Constant on right side of comparison`. Код "if (a == 5) { .. }" следует переписать так "if (5 == a") { .. }"



  * `Switch statements should include all possible enumeration constants`. В перечислимом типе 3 возможных значения, а в switch задействовано только 2 из них.



  * `Do not divide by powers of 2: use ">> 1" rather than "/ 2"`



  * `Invalid string literal: "Yes"`. Строки следует хранить в ресурсах, а не кодировать их жестко в код.



  * `Use charAt() rather than startsWith() when the constant is a single character string`. Код skinName.startsWith("/") можно переписать skinName.charAt(0) == '/'



  * `Define the initial capacity of StringBuilder instances`






Если не полениться и настроить Code Pro под себя, то инструмент оказывается весьма полезным. Возможно, некоторые рекомендации похожи на "ловлю блох".. но почему бы не привыкнуть писать более оптимальный код, если это возможно?





Кстати, юзабилити у Code Pro на высоте. Находясь в списке ошибок можно:








  * автоматически исправить ошибку (естестенно, автоматический рефакторинг кода поддерживается не для всех типов правил);



  * проигнорировать ошибку - она перестанет появляться в списке ошибок;



  * отключить правило, по которому была найдена ошибка;



  * настроить (!) параметры правила,






и так далее.





Кроме аудита, очень понравилось, как Code Pro ищет дубли. Незаменимая функция.









![](http://4.bp.blogspot.com/-yKmH44qnn_o/TwWG1iYOocI/AAAAAAAAAGA/6Feo9xpPQ9Y/s320/codepro_compare.png)









### PMD





[PMD ](http://pmd.sourceforge.net/)- еще один анализатор кода, типа Code Pro. Основное назначение - поиск неоптимального кода, проблем с производительностью, нарушений стиля кодирования, дублей в коде и т.д. Плагин PMD устанавливается с помощью location URL: `http://pmd.sourceforge.net/rules/android.html`





Примечательно, что в PMD реализовано [несколько правил, специфичных для Android ](http://pmd.sourceforge.net/rules/android.html)





Для тестового приложения PMD выдал около 2000 предупреждений. Примеры:








  * `Substitute calls to size() == 0 (or size() != 0) with calls to isEmpty()`



  * `Private field ''{0}'' could be made final; it is only initialized in the declaration or constructor.`



  * `UnnecessaryCaseChange: Using equalsIgnoreCase() is cleaner than using toUpperCase/toLowerCase().equals()`



  * `UnusedFormalParameter: Avoid unused {0} parameters such as ''{1}''. Avoid passing parameters to methods or constructors and then not using those parameters.`



  * `InefficientEmptyStringCheck: String.trim().length()==0 is an inefficient way to validate an empty String.`



  * `UseIndexOfChar: String.indexOf(char) is faster than String.indexOf(String).`



  * `BooleanInversion: Use bitwise inversion to invert boolean values`






Несмотря на то, что PMD генерирует множество лишних предупреждений, следует отметить, что и полезных подсказок он выдает множество. Так же как и Code Pro, PMD следует тщательно настраивать.





В PMD, так же как в Code Pro, реализована функция поиска повторяющихся участков кода. Визуализация найденных фрагментов, правда, похуже. Зато результаты поиска - другие, так что программы дополняют друг друга.









![](http://1.bp.blogspot.com/-07qFCT9CevQ/TwWOUNRVzfI/AAAAAAAAAGY/EBMZ6YGA2-U/s320/pmd.jpg)









###  





### Motodev App Validator





Motodev App Validator входит в состав среды разработки [MOTODEV Studio for Android ](http://developer.motorola.com/docstools/motodevstudio/). Прочитать про него можно в презентации [Static Analysis For Improved Application Performance And Quality Presentation (PDF) ](http://assets.en.oreilly.com/1/event/68/Static%20Analysis%20For%20Improved%20Application%20Performance%20And%20Quality%20Presentation.pdf)





Те, кто не использует MOTODEV Studio, могут попробовать [online вариант Motodev App Validator ](http://developer.motorola.com/testing/app-validator/)(требуется зарегистрироваться на сайте). 





Online вариант Motodev App Validator принимает на вход apk-файл приложения. К сожалению, 400 килобайтную apk-шку тестового приложения online вариант не заглотил - выдал ошибку. Мелкие приложение в 30-40 кб анализирует, а с "крупным" не работает. Пришлось [качать Motodev Studio ](http://developer.motorola.com/docstools/motodevstudio/download/)





Скачал, запустил. Для тестового приложения Application Validator выдал 101 предупреждение. Из них:








  * 75 - `"Drawable "XXX.png" does not exist in the "drawable-ldpi" folder. Add the appropriate "XXX.png" drawable to the "drawable-ldpi" folder."` - ничего нового.



  * 13 - `"Default layout file YYY.xml does not have an xlarge-specific version. This app may not have the look and feel expected on devices with extra large screens. Define an xlarge layout for YYY.xml or add <supports-screens android:xlargeScreens="false"> to the manifest file."` - так же ничего нового.



  * 4 - `"The import android.util.DisplayMetrics is never used ZZZ.java"` - ошибка, на которую умеет указывать сам Eclipse.



  * 1 - `"The value of the local variable AAA is not used"` - эту ошибку умеют находить многие анализаторы.



  * 3 - `"The static field A.B should be accessed in a static way ClassA.B"` - и эту тоже.






Интересным оказалось лишь одно сообщение - `"The application declares the permission android.permission.CALL_PHONE which implies the unsupported feature android.hardware.telephony..."`. Честно говоря, результаты работы не впечатлили.









![](http://1.bp.blogspot.com/-NHo6NN7u7ZY/TwZeDM5UqkI/AAAAAAAAAGw/EfpMA2g-Ong/s320/motodev_settings.png)









Впрочем, судя по списку правил, Application Validator умеет находить и более интересные вещи: незакрытые курсоры, недочеты Android Manifest, отсутствие перевода строки в локализованных ресурсах, ненужные или наоборот, пропущенные permissions и т.д.





###  





### Klocwork Solo for Java





[Klocwork Solo ](http://www.klocwork.com/products/solo/)- платный статический анализатор кода. По заверениям разработчиков, способен находить более 200 типов проблем, связанных с уязвимостью и надежностью кода. Анализатор умеет находить проблемы, специфичные для Android-приложений. Интегрируется с EClipse. Триальная 30-дневная версия доступна после регистрации на сайте. У триальной версии ограничение - не более 300 файлов в проекте.





Полный список правил, которые проверяет Kockwork Solo, можно посмотреть в[документации ](http://download.klocwork.com/docs/issuehelp/index.html). На настоящий момент правил, специфичных для Android, всего девять.









![](http://2.bp.blogspot.com/-EX-wZ8D7340/TwWqBFoC-LI/AAAAAAAAAGk/aaRqJPj4WPM/s320/klocwork_settings.png)









Для тестового приложения Kockwork Solo выдал 240 предупреждений. Неожиданно, очень интересных. Примеры:








  * `NPE.RET : Null pointer dereference of 'xy' where null is returned from a method`. В приложении имеется такой код: `Point xy = m_SA.getShortcutPosition(); Rect r = new Rect(0, 0, xy.x, xy.y);`. Функция getShortcutPosition() в отдельных случаях может возвращать null. Но в вызывающей функции проверки на null нет. Это типичная ошибка.



  * `NPE.COND : Null pointer dereference of 'xxx' where null comes from condition`. Объект xxx используется без проверки на null, а между тем, он запросто может быть равен null.



  * `RTC.CALL : Type cast from 'android.view.View' to 'android.widget.ImageButton' is redundant because method 'setTag' is defined in 'android.view.View'`. В приложении код такой:`((ImageButton)findViewById(R.id.hs_image)).setTag(combination)`. Т.е. анализатор обнаружил ненужное приведение типа.



  * `REDUN.FINAL : Redundant 'final' modifier` - приватный метод объявлен как final. Приватные методы всегда final, так что объявление излишне.



  * `JD.SYNC.IN : Field 'VarName' synchronized inconsistently.` - доступ к переменной VarName синхронизирован частично. Это ошибка - доступ должен либо всегда синхронизироваться, либо никогда.



  * `ANDROID.NPE : Null pointer dereference of 'window' in an Android application`. Код в приложении следующий: `Window window = activity.getWindow(); window.getDecorView().getWindowVisibleDisplayFrame(rect)` Т.е. не выполняется проверка window на null






Так что Kockwork Solo находит реальные ошибки, причем иные, чем FindBugs (списки дефектов, которые они обнаруживают, пересекаются незначительно). Лично меня очень заинтересовали ошибки типа NPE.RET и Android.NPE. Такие ошибки, судя по всему, не находит ни один другой анализатор.





Юзабилити у продукта не понравилось. В списке ошибок есть фильтрация, есть возможность указать статус ошибки. Но если указать статус "Ignored", то ошибка пропадает и не ясно, как ее вернуть. Если отфильтровать список, то часть ошибок из списка пропадает и опять же не ясно, как сбросить фильтр. Не интуитивно все как-то. В CodePro сделано удобнее.









![](http://1.bp.blogspot.com/-Lmwoy5r95EE/TwZ1c_3J7AI/AAAAAAAAAG8/YJ0REyWSlCQ/s320/klocwok_screen.jpg)









###  





### JTest от Parasoft





Триальную версию [Jtest ](http://www.parasoft.com/jsp/products/jtest.jsp)просто так скачать не дают. Так что этот анализатор я в работе проверить не смог. Судя по описанию - функциональность очень интересная. Например, JTest, так же как и Code Pro, умеет самостоятельно рефакторить код.





###  





### Julia





Julia - коммерческий анализатор кода Java / Android приложений. "Семантический инструмент, основанный на [математической теории ](http://www.juliasoft.com/public/Biblioteca/cade11.pdf)". 





Как указано на сайте разработчика, протестировать работу анализатора можно[online ](http://julia.scienze.univr.it/), загрузив в него jar-файл. У android приложения нет jar файла, есть apk (бинарный формат - другой). APK-файл анализатор принял.. но ни один из доступных способов анализа (Nullness, Termination, Check) ничего не выдал. Пишет - "there are no warnings". 





Загрузил первый попавшийся JAR - работает. Судя по всему, там неплохой анализ на "отсутствие проверки на null". Жаль, что apk-шки не грузятся.





**Update**: Написал разработчикам. Они мне подсказали, что можно экспортировать Android приложение в JAR-файл прямо из Eclipse. Экспортировал, загрузил в Julia.





Nullness analysis и Termination analysis результатов не выдали. Дело в том, что в демонстрационной online-версии для данных типов анализа существует ограничение: общее количество методов не должно превышать 10000 и 8500 соответственно. В тестовом приложении количество методов оказалось большим, так что процедура анализа принудительно останавливалась до завершения работы.





Третий тип анализа - "Checks", - выдал 286 предупреждений. По большей части, вот таких:








  * `[Classcast] A you sure that this cast from classA into classB is always legal?`



  * `[BadNames] Method XXX has a bad name`



  * `[Deadcode] Methos XXX is not reachable`



  * `[BadEq] Inefficient comparison with the empty string. Use isEmpty() instead`



  * `[Approximation] Unsafe comparison beween non-integral numbers.`



  * `[StaticFieldAccess] Modification of static field from a non-static context`(это что-то новенькое; другие анализаторы на такие ошибки не ругались).



  * `[Unused class] Class YYY is not used.`



  * `[Field access] Field XXX is never reach in reachable code.`



  * `[Useless call] Useless call toString()`



  * `[BadEq] Suspicious use of == rather then equals() to compare two java.lang.String`






В принципе, кое что интересно есть. Но: пользоваться очень неудобно (результаты выдаются в виде странички на флеш), поскольку сервис online, приходится "отдавать" исходники приложения в виде jar, что не всегда приемлемо.





**Update2**. Попросил разработчиков Julia провести анализ тестового приложения и прислать мне результаты. Они любезно согласились.





Termination analysis - это анализ кода на наличие бесконечных циклов и рекурсий в коде. Он выдал одно предупреждение. Анализатору не понравился код   
 
	private void expungeStaleEntries() {  
	Reference sv;  
	while ((sv = queue.poll()) != null) {  
	hash.remove(reverseLookup.remove(sv));  
	}  
	}  
  
из реализации SoftHashMap, взятой [отсюда ](http://www.javaspecialists.eu/archive/Issue098.html). Опасения анализатора понятны. Тем не менее, код правильный.





Nullness analysis проверяет в коде отсутствие необходимых и наличие излишних проверок на null. Таких предупреждений было выдано 187. Примеры ошибок:








  * `X1.java:57: is the return value of getY1 non-null?`



  * `X2.java:57: is the 0th actual parameter of parseInt non-null?`



  * `X3.java:69,70,72,78,105,109,178: is the return value of getY2 non-null?`



  * `X4.java:227,261,309: is the value of field mX1 non-null?`



  * `X5.java:84: is this nullness check useless?`



  * `X6.java:318,361: is the receiver of the call to iterator non-null?`



  * `X7.java:34: is the formal parameter srcRect non-null?`






Отмечу, что Kockwork Solo выдал всего 43 предупреждения, связанных с проверкой на null. Подробнее результаты, полученные Julia и Kockwork Solo, сравниваются ниже.





###  





### Напоследок. Сравнительные тесты





Ну как же без тестов... Многие анализаторы обнаруживают одни и те же типы проблемы в коде. Сравним, кто что находит.





#### Неверное сравнение строк





В Java строки надо сравнивать через equal. Между тем, по старой сишной привычке, можно сравнить строки через != и == и получить ошибку. Этот баг настолько распространен, что его ищут чуть ли не все анализаторы.





В тестовом приложении оказалось семь таких багов. Вот они:  
 
	1. widget_name == ""  
	2. title != adapter.getItem(i).Title  
	3. srcStr == "S"  
	4. m_K != ""  
	5. contact_name != m_B.getB().GetR(this)  
	6. contact_name == ""  
	7. name == "" ? "" : name + ": "  







#### Проблемы со switch





Для switch характерны следующие проблемы:








  * 1. Отсутствие default



  * 2. Пропущен break



  * 3. Switch выполняется по значению перечислимого типа. В "case" используются не все значения, входящие в перечислимый тип.



  * 4. Switch слишком маленький - например, в нем одно или два значения. Можно обойтись if/else.



  * 5. В двух case используется одинаковый код.






Далеко не всегда эти проблемы являются реальными ошибками. Тем не менее, анализаторы кода как правило считают своим долгом о них сообщить.





Разные анализаторы работают с разными типами проблем. Итак, вот как отработали анализаторы на тестовом приложении.







#### NP-анализ





Отсутствие проверки на null и, как результат, "NUll pointer exception" - одна из наиболее частых ошибок при разработке под Android. Другая сторона медали - излишние проверки. 





К слову сказать, FindBugs успешно отлавливает такую ошибку: вначале у объекта вызывается функция, а затем объект проверяется на null. 





Klocwork и Julia "заточены" под Android. И эта "заточенность" проявляется, прежде всего, в возможности NP-анализа. Как я писал вышел, Klocwork для тестового приложения выдал 43 NP-предупреждения, Julia - 187. Сравним, кто что нашел.





##### NP анализ. Файл 1





Проблема 1.1. `String action = intent.getAction(); if (action.equals("abc")) {..};` Переменная action может быть null. Проблему обнаружили обе программы.  
Проблема 1.2. `intent.getExtras().getInt(...);` Результат getExtras() не проверяется на null. Проблему обнаружили обе программы.   
Проблема 1.3. `Uri uri = intent.getData(); Integer.parseInt(uri.getQueryParameter("widget_id"))`. Оба анализатора сообщили об использовании uri без проверки uri на null. Julia дополнительно сообщила о том, что результаты работы uri.getQueryParameter на null не проверяются и в parseInt может быть передан null.  
Проблема 1.4. `WidgetContent wc = WidgetFabric.getWidgetContent(); wc.getFlagValue();`. Оба анализатора сообщили, что wc может быть null. В реализации getWidgetContent() действительно есть ветка кода, которая возвращает null.  
Проблема 1.5. `((Singleton)context.getApplicationContext()).getABC().Register();`Об этой проблеме сообщила только Julia. Функция getABC() может вернуть null и вызывать Register() нельзя. На самом деле, объект ABC создается в [синглетоне, реализованном поверх класса Application. ](http://derevyanko.blogspot.com/2010/12/android_26.html)Инициализируется он в момент запуска приложения и null быть не может. Однако, инициализация ABC проводится не в конструкторе синглетона, а в функции onCreate. Анализатор не знает про тонкости создания синглетона и предупреждает об ошибке, которая вряд ли возможна.





##### NP анализ. Файл 2





Проблема 2.1. `CheckBoxPreference pref = (CheckBoxPreference)getPreferenceScreen().findPreference("pref_name");`. findPreference может возвращать null.  
Проблема 2.2. `List list_skins = ((Singleton)this.getApplicationContext()).getSkinManager().getListSkins("skin_name");`Проблема аналогична 1.5. Функция getSkinManager может возвращать null, но только теоретически.  
Проблема 2.3. `int len_skins = list_skins.size();` Переменная list_skins действительно может быть null.  
Проблема 2.4. `for (int j = 0; j < len_skins; ++j) { array_skins[j] = list_skins.get(j).SkinTag; }` Результаты get не проверяются на null. Такая проверка необходима, если list_skins может содержать null. В приложении такого быть не может, так что проверка не нужна.  
Проблема 2.5. `String skin_name = (String) p.getEntries()[p.findIndexOfValue(skin_tag)];` Результат работы getEntries() не проверяется на null.  
Проблема 2.6. `CharSequence[] titles = res.getTextArray(idTitles); if (values[0].equals(svalue)) {...};` Значение values[0] не проверяется на null.  
Julia сообщила обо всех этих проблемах. Klocwork - только о 2.3.





##### NP анализ. Файл 3





Проблема 3.1. `m_AsyncTask = new AppInfoReceiverTask(mUtils.getList());` Эту ошибка нашла только Julia - переменная mUtils может быть null. На самом деле, mUtils инициализируется в onCreate (речь идет о наследнике Activity) и null быть не может.  
Проблема 3.2. `protected void onActivityResult(int requestCode, int resultCode, Intent data) { data.getExtras().getString(...);` Эту проблему нашли оба анализатора: data.getExtra может вернуть null.  
Проблема 3.3. `(LauncherContent)WidgetFabric.getWidgetContent()` Эту проблему нашел Klocwork. Julia ее пропустила.  
Проблема 3.4. `SpinnerItem selected_item = (SpinnerItem)spinner.getSelectedItem(); String s = selected_item.Title;`Функция getSelectedItem может вернуть null согласно документации. Проблему нашла только Julia  
Проблема 3.5. `ListView list_view = (ListView) dialog.findViewById(R.id.listview); list_view.setAdapter(new SelectImageAdapter(list_images) );` Теоретически, findViewById может вернуть null. На проблему указала только Julia.  
Проблема 3.6. `private final void remove_shortcut(AppInfo appInfo) { m_List.remove(appInfo); }` В функцию может быть передан null, appInfo на null не проверяется. Проблему нашла только Julia.  
Проблема 3.7. `for (SkinManager.SkinInfo skin : skins) { ... }` Переменная skins может содержать null. Проблему нашли оба анализатора.





Резюме. Julia находит почти все NP-ошибки, которые обнаруживает Klocwork, плюс еще ряд дополнительных. Одновременно, она генерирует довольно много ложных сообщений, которые на практике придется просто игнорировать. Самая большая проблема на мой взгляд - она не учитывает, что в Activity переменные инициализируются в onCreate, а не в конструкторе.





### Итоги





В итоге получается следующая картина. В настоящее время существует восемь доступных статических анализаторов кода для Android приложений: Lint, FindBugs, Checkstyle, CodePro, PMD, Motodev App Validator, Klocwork Solo, Julia. Основная специализация у них следующая:








  * Lint и Motodev App Validator - поиск проблем в ресурсах Android приложения;



  * FindBugs, Klocwork Solo и Julia - поиск ошибок в коде приложения;



  * Checkstyle - проверка соблюдения правил оформления кода и стандарта кодирования;



  * CodePro и PMD - поиск неоптимального кода, проблемного кода, поиск дублей.






FindBugs и Lint прекрасно работают "с нуля" - настраивать их практически не требуется. CodePro и PMD требуют тонкой настройки. Но если отключить в них ненужные проверки, они приносят реальную пользу. Motodev App Validator и Checkstyle мне показались не слишком полезными - на любителя. Klocwork Solo - находит ряд ошибок, которые не находят другие анализаторы. Он платный, но и триальную версию можно вполне успешно использовать для небольших приложений. Julia находит ряд NP-ошибок, которые не находит Klocwork, но при этом генерирует еще и множество ложных предупреждений (ошибка возможна, но только теоретически). К сожалению, воспользоваться бесплатной версией практически не возможно.

























 





 





 









 




