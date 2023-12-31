# Упаковка WEB-приложения для ОС АВРОРА (руководство)
<br>
<strong>Руководство по упаковке веб-приложения на ОС Аврора при помощи Аврора IDE (Qt Creator), не требующего для своей работы веб-сервер</strong><br><br>

Упаковываем приложение, работающее без веб-сервера, построенное на базе html+js. <br>Руководство для версии Аврора IDE 4.0.2.249 (актуальность - декабрь 2023 года).<br><br>

<strong>Шаги:</strong><br><br>
1. Создаем новое приложение, заполняем все необходимые поля диалога создания нового Qt QML-приложения<br><br><img src="https://github.com/mastercond-comp/AuroraQTWebApp/blob/main/step1.png?raw=true"><br><br>
2. Весь веб-контент (html,js,контент) кладем в папку www в директории приложения (например, для Linux это будет /home/имя_пользователя/AuroraApps/имя_приложения)<br><br><img src="https://github.com/mastercond-comp/AuroraQTWebApp/blob/main/step2.png?raw=true"><br><br>
3. Правой кнопкой мыши по имени проекта, в меню выбираем "Добавить новый", в окне выбираем Qt -> Файл ресурсов Qt.<br><br><img src="https://github.com/mastercond-comp/AuroraQTWebApp/blob/main/step3.png?raw=true"><br><br>
Нажимаем "Выбрать", в следующем диалоге пишем в имени файла resources и в следующем диалоге "Завершить".<br><br><img src="https://github.com/mastercond-comp/AuroraQTWebApp/blob/main/step3-1.png?raw=true"><br><br> В проекте будет создан файл ресурсов resources.qrc, который будет прописан в .pro файле нашего приложения (будет внизу .pro файла строка RESOURCES += resources.qrc)<br><br><img src="https://github.com/mastercond-comp/AuroraQTWebApp/blob/main/step3-2.png?raw=true"><br>
4. Добавлем в проект приложения нашу папку www: правой кнопкой по названию проекта -> "Добавить существующий каталог", далее выбираем наш каталог www.<br><br><img src="https://github.com/mastercond-comp/AuroraQTWebApp/blob/main/step4.png?raw=true"><br><br>
5. Всё содержимое каталога www будет включено в .pro файл нашего проекта. Открываем .pro файл и видим список файлов в разделе DISTFILES += . Выбираем и копируем все оттуда, начинающееся с www (Ctrl-C).<br><br><img src="https://github.com/mastercond-comp/AuroraQTWebApp/blob/main/step5.png?raw=true"><br><br>
6. Правой кнопкой мыши по нашему файлу ресурсов resources.qrc -> Открыть в редакторе -> Где префикс - там ставим /, добавляем префикс. Теперь правой кнопкой мыши по resources.qrc -> Открыть с помощью -> Текстовый редактор.<br><br>
   
   &lt;RCC&gt;<br>
   &lt;qresource prefix="/"&gt;<br>
    //Сюда вставлем то, что скопировали в пункте 5. Добавляем &lt;file&gt; перед названием файла и &lt;/file&gt; после названия файла, при большом количестве файлов пользуемся функцией "Найти и заменить" (Ctrl-F)//<br>
   &lt;/qresource&gt;<br>
&lt;/RCC&gt;<br>
<br>

Должно получиться в итоге примерно следующее:<br><br>
<pre><code>
&lt;RCC&gt;
   &lt;qresource prefix="/"&gt;
    ...
       &lt;file&gt;www/blockly/accessible/app.component.js&lt;/file&gt;
       &lt;file&gt;www/blockly/accessible/clipboard.service.js&lt;/file&gt;
       &lt;file&gt;www/blockly/accessible/field.component.js&lt;/file&gt;
       &lt;file&gt;www/blockly/accessible/libs/Rx.umd.min.js&lt;/file&gt;
       &lt;file&gt;www/blockly/accessible/libs/angular2-all.umd.min.js&lt;/file&gt;
       &lt;file&gt;www/blockly/accessible/libs/angular2-polyfills.min.js&lt;/file&gt;
       &lt;file&gt;www/blockly/accessible/libs/es6-shim.min.js&lt;/file&gt;
       &lt;file&gt;www/blockly/accessible/messages.js&lt;/file&gt;
    ...
 &lt;/qresource&gt;
&lt;/RCC&gt;
</code></pre><br><br><img src="https://github.com/mastercond-comp/AuroraQTWebApp/blob/main/step6.png?raw=true"><br><br>

Не забываем сохранить изменения в файлах.<br><br>

7. Идем в .pro файл нашего проекта и добавляем  в #CONFIG += resources_big. Должно получиться следующее:<br><br>
   
<code>
    CONFIG += \ <br>
    auroraapp \ <br>
    resources_big <br>
</code><br><br>

<strong>ВНИМАНИЕ! 
Без этой директивы в случае большого веб-проекта на несколько десятков мегабайт сборка выдаст ошибку "virtul memory exhauted", "qrc_resources.o Error 1"
</strong><br><br><img src="https://github.com/mastercond-comp/AuroraQTWebApp/blob/main/step7.png?raw=true"><br><br>

<pre><code>
#ЗА ПОМОЩЬ С РЕШЕНИЕМ ПРОБЛЕМ С НЕХВАТКОЙ ПАМЯТИ ПРИ СБОРКЕ ОГРОМНОЕ СПАСИБО ДАНИИЛУ МИХАЙЛОВУ-ОВСЯННИКОВУ <br>
#CONFIG += resources_big<br>
#ПРИ ПОМОЩИ ЭТОЙ ДИРЕКТИВЫ ПРОЕКТ СОБИРАЕТСЯ БЕЗ ПРОБЛЕМ ДАЖЕ ПРИ РАЗМЕРЕ ПАПКИ WWW в 150 Мб<br>
</code></pre><br><br>

8. Добавляем в конец нашего .pro файла следующий код:<br><br>
<pre><code>
DEPLOYMENT_PATH = /usr/share/$${TARGET}/qml/pages <br>
www.files =www <br>
www.path = $${DEPLOYMENT_PATH} <br>
INSTALLS += www <br>
<br><br>

#ВКЛЮЧЕНИЕ ПАПКИ www В RPM-файл, без этой секции ресурсы в RPM-файл включены не будут<br>
#В РАБОТЕ ДЛЯ WEBVIEW В QML ТЕПЕРЬ МОЖНО ИСПОЛЬЗОВАТЬ ССЫЛКИ ТИПА url: "www/index.html" где index.html находится в папке www ресурсов</code></pre>
<br><br><img src="https://github.com/mastercond-comp/AuroraQTWebApp/blob/main/step8.png?raw=true"><br><br>
<i>Физически на устростве (телефоне, планшете) папка www будет после установки приложения размещаться в папке /usr/share/имя_приложения/qml/pages</i><br><br>
9. Переходим к файлу имя_проекта.qml. Выставляем ориентации (опционально)<br><br>
<code>
    allowedOrientations: Orientation.All <br>
    _defaultPageOrientations: Orientation.All
</code>
<br><br><img src="https://github.com/mastercond-comp/AuroraQTWebApp/blob/main/step9.png?raw=true"><br><br>
10. Переходим к MainPage.qml. Для работы WebView при условии нахождения index.html в папке www будет работоспособным следующий код:
<br><br>
<pre><code>
import QtQuick 2.6
import Sailfish.Silica 1.0
import Sailfish.WebView 1.0
import Sailfish.WebEngine 1.0
Page {
    objectName: "mainPage"
    WebView {
        id: webView
        objectName: "webView"
        anchors {
            top: parent.top
            left: parent.left
            right: parent.right
            bottom:parent.bottom
        }
    }
    Component.onCompleted: {
                webView.url = Qt.resolvedUrl("www/index.html")
            }
}
</code></pre><br><br>

11. В разделе "Проекты" не забываем указать ключи для подписи пакетов, собираем приложение.<br><br><img src="https://github.com/mastercond-comp/AuroraQTWebApp/blob/main/step10.png?raw=true"><br><br>
12. При необходимости можем использовать имеющиеся в приложении компоненты, которые мы разместили в папке www, для комбинированного web/qml приложения
