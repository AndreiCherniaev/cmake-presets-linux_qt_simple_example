<img src="https://habrastorage.org/webt/ew/dr/n6/ewdrn6iz_kw5il5zv5seze2pjuc.png" alt="image"/>

Каждый день я создаю множество однотипных проектов C++ с системой сборки cmake. И открывая проекты в IDE, каждый раз снимаю галочки с типа сборки, оставляя только Debug. А ещё меняю путь к папке сборки. Мелочь, а утомляет. 

<img src="https://habrastorage.org/webt/fo/oy/yg/fooyygtwmffzgs9xsf0pcwrqn1y.png" alt="image"/>
<i><font color="#4682B4">Первое открытие проекта (без пресета)</font></i>

Я решил изучить, как сделать эти действия автоматически, а в итоге узнал про удобный метод обмена настройками cmake между программистами.<cut/>

Оказывается, существуют <a href="https://habr.com/ru/articles/683204/">пресеты</a>. Пресеты позволяют вынести параметры сборки из CMakeLists.txt. Это нужно для того, чтобы ваши проекты без проблем собирались под разные платформы и тулчейны. Пресет представляет из себя json-файл, в котором задаются различные параметры, влияющие на сборку проекта (опции конфигурации, флаги компилятора и т. д.).

Так я познакомился с CMakePresets.json. В принципе, ничего сложного, но обилие настроек, о которых расскажет <a href="https://cmake.org/cmake/help/latest/manual/cmake-presets.7.html">официальная документация</a> может отбить желание в этом разбирать. Я решил разместить здесь минималистичный пример. Другие примеры от The Qt Company доступны <a href="https://doc.qt.io/qtcreator/creator-build-settings-cmake.html">здесь</a>.

Не смотря на то, что далее будет показан пример, как это работает в Ubuntu 22 с IDE Qt Creator и проектом Qt, cmake пресеты поддерживаются и в других IDE и могут быть применены в любых cmake проектах. Итак, требования к пресету:

<ol>
	<li>Только Debug сборка;</li>
	<li>Чтобы промежуточные файлы компиляции и исполняемый файл приложения лежали в смежной с исходниками папке под названием build;</li>
	<li>У меня много <a href="https://habr.com/ru/articles/551342/">экзотических</a> кастомных сборок Qt, но по-умолчанию мне нужна одна конкретная сборка (известен полный путь);</li>
	<li>Работа ведётся в Ubuntu 22.</li>
</ol>
В результате получился файл CMakePresets.json:

<source lang="yaml">{
  "version": 3,
  "configurePresets": [
    {
      "name": "HabrPresetName",
      "displayName": "GoodPreset",
      "generator": "Ninja",
      "binaryDir": "${sourceDir}/../build",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Debug"
      },
      "environment": {
        "PATH": "/home/user/Qt/6.6.0/gcc_64/lib/cmake/Qt6"
      }
    }
  ],
  "buildPresets": [
    {
      "name": "debug",
      "displayName": "Ninja Debug",
      "configurePreset": "HabrPresetName",
      "configuration": "Debug"
    }
  ]
}</source>
где:

<ul>
	<li>"PATH" задаёт полный путь до Qt, которую я использую;</li>
	<li>"binaryDir" задаёт путь для промежуточных файлов сборки и артефактов сборки;</li>
	<li>"version" кодирует минимально необходимую версию cmake. Если у вас старый cmake, то его cmake довольно <a href="https://habr.com/ru/articles/599125/">просто</a> собирается из исходных кодов;</li>
	<li>Описание остальных полей лучше смотреть в <a href="https://cmake.org/cmake/help/latest/manual/cmake-presets.7.html">документации</a>.</li>
</ul>
Обратите внимание, что в данном примере путь захардкожен да и ещё с кавычками в определенную сторону.

Файл-пресет CMakePresets.json должен находиться рядом с файлом CMakeLists.txt Также не должно существовать файла CMakeLists.txt.user а если он уже существует, удалите его.

<b>Структура каталога:</b>

<img src="https://habrastorage.org/webt/uu/in/vj/uuinvjwouwxu3tarpbxxclp41_8.png" alt="image"align="center">

Теперь при первой попытке открыть проект IDE использует cmake пресет, как показано на рисунке ниже.

<b>Первое открытие проекта с пресетом:</b>

<img src="https://habrastorage.org/webt/gj/iy/6e/gjiy6evo8ygvpz2q-mk33aragym.png" alt="image"align="center">
Кстати, что касается именно Qt Creator, то единожды обнаруженный пресет он запоминает в своей внутренней базе. Удалить (remove) его можно так

<b>Qt Creator's Preferences dialog:</b>

<img src="https://habrastorage.org/webt/it/ie/jt/itiejtzvj28ekd1rsp7jb--khwi.png" alt="image"align="center">
Также приведенный здесь пример доступен в <a href="https://github.com/AndreiCherniaev/cmake-presets-linux_qt_simple_example.git">репозитории</a>:

<source lang="yaml">git clone https://github.com/AndreiCherniaev/cmake-presets-linux_qt_simple_example.git</source>
Не только Qt Creator воспринимает CMakePresets.json. Предлагаю вам поделиться своей историей использования cmake пресета и привести свои примеры в комментариях.
