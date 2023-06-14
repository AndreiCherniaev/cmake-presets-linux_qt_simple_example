very simple example how use cmake preset for demo Qt proj for Linux

# cmake пресеты
Каждый день я создаю множество однотипных проектов C++. И, открывая их в IDE, каждый раз снимаю галочки с типа сборки, оставляя только Debug. А ещё меняю путь к папке сборки. Мелочь, а утомляет.


<p align="center">
  <img alt="First time opening project without any preset. Qt Creator's Configure dialog image" src="Qt%20Creator%20Configure%20dialog%20Default.png" width="800">
  <br>
    <em>Первое открытие проекта (без пресета)</em>
</p>

Я решил узнать как сделать это автоматически, а в итоге узнал про удобный метод обмена настройками cmake между программистами.
Оказывается, существуют [пресеты](https://habr.com/ru/articles/683204/). Пресеты позволяют вынести параметры сборки из CMakeLists.txt. Это нужно для того, чтобы ваши проекты без проблем собирались под разные платформы и тулчейны. Пресет представляет из себя json-файл, в котором задаются различные параметры, влияющие на сборку проекта (опции конфигурации, флаги компилятора и т.д.).
Так я познакомился с CMakePresets.json. в принципе ничего сложного, но обилие настроек, о которых расскажет официальная [документация](https://cmake.org/cmake/help/latest/manual/cmake-presets.7.html) может отбить желание в этом разбирать. Я решил разместить здесь минималистичный пример. Другие примеры от The Qt Company доступны [здесь](https://doc.qt.io/qtcreator/creator-build-settings-cmake.html).
Не смотря на то, что далее будет показан пример как это работает в Ubuntu 22 с IDE Qt Creator и проектом Qt, cmake пресеты поддерживаются и в других IDE и могут быть применены в любых cmake проектах. Итак, требования к пресету

1. Только Debug сборка;
2. Чтобы промежуточные файлы компиляции и исполняемый файл приложения лежали в смежной с исходниками папке под названием build;
3. У меня много [экзотических](https://habr.com/ru/articles/551342/) кастомных сборок Qt, но по-умолчанию мне нужна одна конкретная сборка (известен полный путь), работа ведётся в Ubuntu 22.

В результате получился файл CMakePresets.json
```xml
{
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
}
```

где
* "PATH" задаёт полный путь до Qt, которую я использую;
* "binaryDir" задаёт пусть для промежуточных файлов сборки и артефактов сборки;
* "version" кодирует минимально необходимую версию cmake. Если у вас старый cmake, то его cmake довольно [просто](https://habr.com/ru/articles/599125/) собирается из исходных кодов;
* Описание остальных полей лучше смотреть в [документации](https://cmake.org/cmake/help/latest/manual/cmake-presets.7.html).

Файл-пресет CMakePresets.json должен находиться рядом с файлом CMakeLists.txt Также не должно существовать файла CMakeLists.txt.user а если он уже существует, удалите его.
<p align="center">
  <img alt="Right folder structure image" src="right%20folder%20structure.png" width="300">
  <br>
    <em>Структура каталога</em>
</p>

Теперь при первой попытке открыть проект IDE использует cmake пресет, как показано на рисунке ниже.
<p align="center">
  <img alt="First time opening project with my preset. Qt Creator's Configure dialog image" src="Qt%20Creator%20Configure%20dialog%20with%20my%20preset.png" width="600">
  <br>
    <em>Первое открытие проекта с пресетом</em>
</p>
Кстати что касается именно Qt Creator, то единожды обнаруженный пресет он запоминает в своей внутренней базе. Удалить (remove) его можно так
<p align="center">
  <img alt="Qt Creator's Preferences dialog image" src="presets%20kits%20Qt%20Creator.png" width="600">
  <br>
    <em>Qt Creator's Preferences dialog</em>
</p>
Не только Qt Creator воспринимает CMakePresets.json. Предлагаю вам поделиться своей историей использования cmake пресета, привести свои примеры в комментариях.