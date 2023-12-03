# AuroraQTWebApp

<strong>Руководство по созданию веб-приложения на ОС Аврора при помощи Аврора IDE (Qt Creator), не требующее для своей работы веб-сервер</strong>

Приложение, работающее без веб-сервера, построенное на базе html+js. Руководство для версии Аврора IDE 4.0.2.249 (актуальность - декабрь 2023 года).

Шаги:
1. Создаем новое приложение, заполняем все необходимые поля диалога создания нового Qt QML-приложения
2. Весь веб-контент (html,js,контент) кладем в папку www в директории приложения (например, для Linux это будет /home/имя_пользователя/AuroraApps/имя_приложения)
3. Правой кнопкой мыши по имени проекта, в меню выбираем "Добавить новый", в окне выбираем Qt -> Файл ресурсов Qt.
Нажимаем "Выбрать", в следующем диалоге пишем в имени файла resources и в следующем диалоге "Завершить". В проекте будет создан файл ресурсов resources.qrc, который будет прописан в .pro файле нашего прииложения (будет внизу .pro файла строка RESOURCES += resources.qrc)
4. Добавлем в проект приложения нашу папку www: правой кнопкой по названию проекта -> "Добавить существующий каталог", далее выбираем наш каталог www.
5. Всё содержимое каталога www будет включено в .pro файл нашего проекта. Открываем .pro файл и видим список файлов в разделе DISTFILES += . Выбираем и копируем все оттуда, на ->чинающееся с www.
6. Правой кнопкой мыши по нашему файлу ресурсов resources.qrc -> Открыть в редакторе -> Где префикс ставим /, добавляем префикс. Теперь правой кнопкой мыши по resources.qrc -> Открыть с помощью -> Текстовый редактор.
  <code> 
  <RCC>
    <qresource prefix="/">
    //Сюда вставлем то, что скопировали в пункте 5. Добавляем <file> перед названием файла и </file> после названия файла, при большом количестве файлов пользуемся функцией Найти и заменить (Ctrl-F)//
    </qresource>
</RCC>
</code>

Должно получиться в итоге примерно следующее:
<code>
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
</code>

Не забываем сохранить изменения.

7. Идем в .pro файл нашего проекта и добавляем  в #CONFIG += resources_big. Должно получиться следующее:
   
<code>
    CONFIG += \ <br>
    auroraapp \ <br>
    resources_big <br>
</code>

<strong>ВНИМАНИЕ! 
Без этой директивы в случае большого веб-проекта на несколько десятков мегабайт сборка выдаст ошибку "virtul memory exhauted", "qrc_resources.o Error 1"
</strong>

<code>
#ЗА ПОМОЩЬ С РЕШЕНИЕМ ПРОБЛЕМ С НЕХВАТКОЙ ПАМЯТИ ПРИ СБОРКЕ ОГРОМНОЕ СПАСИБО ДАНИИЛУ МИХАЙЛОВУ-ОВСЯННИКОВУ
#CONFIG += resources_big
#ПРИ ПОМОЩИ ЭТОЙ ДИРЕКТИВЫ ПРОЕКТ СОБИРАЕТСЯ БЕЗ ПРОБЛЕМ ДАЖЕ ПРИ РАЗМЕРЕ ПАПКИ WWW в 150 Мб
</code>

8. Добавляем в конец нашего .pro файла следующий код:
   <code>
DEPLOYMENT_PATH = /usr/share/$${TARGET}/qml/pages <br>
www.files =www <br>
www.path = $${DEPLOYMENT_PATH} <br>
INSTALLS += www <br>
</code>

#ВКЛЮЧЕНИЕ ПАПКИ www В RPM-файл, без этой секции ресурсы в RPM-файл включены не будут
#В РАБОТЕ ДЛЯ WEBVIEW В QML ТЕПЕРЬ МОЖНО ИСПОЛЬЗОВАТЬ ССЫЛКИ ТИПА url: "www/index.html" где index.html находится в папке www ресурсов

9. Переходим к файлу имя_проекта.qml. Выставляем ориентации (опционально)
<code>
    allowedOrientations: Orientation.All <br>
    _defaultPageOrientations: Orientation.All
</code>

11. Перходим к MainPage.qml. Для работы WebView при условии нахождения index.html в папке www будет работоспособным следующи код:

<code>
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
</code>

11. В разделе "Проекты" не забываем указать ключи для подписи пакетов, собираем приложение.
