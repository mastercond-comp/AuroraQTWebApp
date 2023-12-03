# AuroraQTWebApp

<strong>Руководство по созданию веб-приложения на ОС Аврора при помощи Аврора IDE (Qt Creator), не требующее для своей работы веб-сервер</strong><br><br>

Приложение, работающее без веб-сервера, построенное на базе html+js. Руководство для версии Аврора IDE 4.0.2.249 (актуальность - декабрь 2023 года).<br><br>

Шаги:<br><br>
1. Создаем новое приложение, заполняем все необходимые поля диалога создания нового Qt QML-приложения<br><br>
2. Весь веб-контент (html,js,контент) кладем в папку www в директории приложения (например, для Linux это будет /home/имя_пользователя/AuroraApps/имя_приложения)<br><br>
3. Правой кнопкой мыши по имени проекта, в меню выбираем "Добавить новый", в окне выбираем Qt -> Файл ресурсов Qt.<br><br>
Нажимаем "Выбрать", в следующем диалоге пишем в имени файла resources и в следующем диалоге "Завершить". В проекте будет создан файл ресурсов resources.qrc, который будет прописан в .pro файле нашего прииложения (будет внизу .pro файла строка RESOURCES += resources.qrc)<br><br>
4. Добавлем в проект приложения нашу папку www: правой кнопкой по названию проекта -> "Добавить существующий каталог", далее выбираем наш каталог www.<br><br>
5. Всё содержимое каталога www будет включено в .pro файл нашего проекта. Открываем .pro файл и видим список файлов в разделе DISTFILES += . Выбираем и копируем все оттуда, начинающееся с www (Ctrl-C).<br><br>
6. Правой кнопкой мыши по нашему файлу ресурсов resources.qrc -> Открыть в редакторе -> Где префикс - там ставим /, добавляем префикс. Теперь правой кнопкой мыши по resources.qrc -> Открыть с помощью -> Текстовый редактор.<br><br>
   <pre>
      <code>
   <RCC>
   <qresource prefix="/">
    //Сюда вставлем то, что скопировали в пункте 5. Добавляем <file> перед названием файла и </file> после названия файла, при большом количестве файлов пользуемся функцией Найти и заменить (Ctrl-F)//<br>
    </qresource>
</RCC>
</code></pre><br><br>

Должно получиться в итоге примерно следующее:<br><br>
<pre><code>
<RCC>
    <qresource prefix="/">
    ...
        <file>www/blockly/accessible/app.component.js</file>
        <file>www/blockly/accessible/clipboard.service.js</file>
        <file>www/blockly/accessible/field.component.js</file>
        <file>www/blockly/accessible/libs/Rx.umd.min.js</file>
        <file>www/blockly/accessible/libs/angular2-all.umd.min.js</file>
        <file>www/blockly/accessible/libs/angular2-polyfills.min.js</file>
        <file>www/blockly/accessible/libs/es6-shim.min.js</file>
        <file>www/blockly/accessible/messages.js</file>
    ...
   </qresource>
</RCC>
</code></pre><br><br>

Не забываем сохранить изменения в файлах.<br><br>

7. Идем в .pro файл нашего проекта и добавляем  в #CONFIG += resources_big. Должно получиться следующее:<br><br>
   
<code>
    CONFIG += \ <br>
    auroraapp \ <br>
    resources_big <br>
</code><br><br>

<strong>ВНИМАНИЕ! 
Без этой директивы в случае большого веб-проекта на несколько десятков мегабайт сборка выдаст ошибку "virtul memory exhauted", "qrc_resources.o Error 1"
</strong><br><br>

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
<br><br>
9. Переходим к файлу имя_проекта.qml. Выставляем ориентации (опционально)<br><br>
<code>
    allowedOrientations: Orientation.All <br>
    _defaultPageOrientations: Orientation.All
</code>
<br><br>
10. Перходим к MainPage.qml. Для работы WebView при условии нахождения index.html в папке www будет работоспособным следующи код:
<br><br>
<pre><code>
import QtQuick 2.6
import QtWebKit 3.0
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

11. В разделе "Проекты" не забываем указать ключи для подписи пакетов, собираем приложение.
