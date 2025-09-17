# Разработчик задач

**<tooltip term="developer">Разработчик задач</tooltip>** является ролью ответственной за разработку задач.
Данный пользователь имеет возможность создавать задачи, формировать и публиковать туры.

## Подготовка задач {id="developer-chapter-prepare-task"}

Для создания задачи требуется предварительно загрузить необходимые файлы.
К задаче можно прикрепить любое количество полигонов, одно упражнение, одно эталонное решение и
опционально условие задачи.

- *Полигон* — xml файл с [ограничениями](https://help.trikset.com/studio/2d-model/restrictions) который будет использоваться при проверке
- *Упражнение* — qrs файл, которой будет предоставлен участнику в качестве примера
- *Эталонное решение* — qrs файл, который будет использован для проверки корректности полигонов

После загрузки всех необходимых файлов вы можете создать задачу и запустить тестирование эталонного решения.
Далее, рекомендуется ознакомиться с результатами тестирования и убедиться, что эталонное решение получает ожидаемое
количество баллов.

## Разработка полигона {id="developer-chapter-prepare-poly"}

Первым этапом разработки полигона является расположение объектов окружения и разметки зон в TRIK Studio.
После этого необходимо сохранить модель мира на диск.
Дальнейшая работа будет происходить с сохранным файлом.

Данная секция направленна на знакомство с *ограничениями*, с подробной информацией можно ознакомиться в
[документации](https://help.trikset.com/studio/2d-model/restrictions) к TRIK Studio.
Ограничения добавляются в специальный блок
`constraints` в сохраненном файле. Также важной частью ограничений являются
[логические зоны](https://help.trikset.com/studio/2d-model/settings#less-than-region-greater-than),
они добавляются в блок `regions`.

> Ограничения описываются в формате xml, основным элементом xml является
> [тэг](https://help.trikset.com/studio/2d-model/settings#tegi),
> тэг может описываться в двух форматах `<element1>...</element1>` (для элементов с внутренней структурой)
> или `<element1/>` (без внутренней структуры). Также у тэгов могут быть параметры
> `<element1 param="value">...</element1>` или
> `<element1 param="value"/>`

```xml
<?xml version='1.0' encoding='utf-8'?>
<root version="20190819">
    <world>
        <regions>
            <!-- Сюда добавляются логические зоны -->
        </regions>
    </world>
    
    <constraints>
        <!-- Сюда добавляются ограничения -->
    </constraints>
</root>
```

Самым простым ограничением является ограничение на время выполнения, оно выглядит следующим образом
`<timelimit value="60000"/>`.
Значение указывается в миллисекундах, в нашем примере ограничение составляет одну минуту.

> Не рекомендуется устанавливать ограничение на выполнение более 3 минут на одно поле и
> более 12 минут на все поля в рамках одной задачи.
> { style="warning" }

Следующим важным видом ограничений является `constraint`. Данный тип ограничения позволяет
выполнять произвольные проверки. Рассмотрим подробнее какие параметры у него есть.

- `checkOnce` Если в значении стоит true, то ограничение будет
  проверено 1 раз при старте программы и больше проверяться не будет.
- `failMessage` Сообщение об ошибке, которое будет показано при нарушении ограничения.


Внутри блока `constraint` должно быть описано условие, этим условием может быть сравнение некоторых значений,
нахождение робота в определенной зоне и другое.
Рассмотрим несколько примеров, следующее ограничение проверяет при запуске,
что на порту А1 установлен инфракрасный датчик расстояния.

```xml
<constraint checkOnce="true" failMessage="Неправильная конфигурация робота">
    <equals>
        <typeOf objectId="robot1.A1"/>
        <string value="twoDModel::robotModel::parts::RangeSensor"/>
    </equals>
</constraint>
```

В данном примере используются следующие конструкции:
- `equals` Проверка равенства значений
- `<string value="twoDModel::robotModel::parts::RangeSensor"/>` Создание строкового значения
- `<typeOf objectId="robot1.A1"/>` Получение типа датчика установленного на соответствующем порту

Здесь и далее будут встречаться новые конструкции, со всеми ними можно ознакомиться
в [документации](https://help.trikset.com/studio/2d-model/restrictions) TRIK Studio.


Следующее ограничение проверяет, что робот находится в допустимом регионе
на протяжении всего времени выполнения программы.

```xml
<constraint failMessage="Робот покинул допустимую зону!">
    <inside objectId="robot1" regionId="zone1"/>
</constraint>
```

На случай если условие для ограничения состоит из нескольких компонентов есть специальный блок `conditions`.
Данный блока позволяет указывать несколько условий и связывать их логически **И** (and) или **ИЛИ** (or).
Тип связки указывается с помощью параметра `glue`. Рассмотрим пример ограничения проверяющего,
что у робота на портах А1 и A2 установлен инфракрасный датчик расстояния.

```xml
<constraint checkOnce="true" failMessage="Неправильная конфигурация робота">
    <conditions glue="and">
        <equals>
            <typeOf objectId="robot1.A1"/>
            <string value="twoDModel::robotModel::parts::RangeSensor"/>
        </equals>
        <equals>
            <typeOf objectId="robot1.A2"/>
            <string value="twoDModel::robotModel::parts::RangeSensor"/>
        </equals>
    </conditions>
</constraint>
```

Для выполнения действий перед началом работы программы есть специальный блок `init`. Его можно использовать, например,
для инициализации переменной в которой будут храниться набранные баллы или вывода приветственного сообщения.

```xml
<init>
    <setter name="score">
        <int value="0"/>
    </setter>
    <message text="Приветственное сообщение"/>
</init>
```

Иногда возникает необходимость не только проверить какое-то условие, но и выполнить определенные действия
во время работы программы, для таких ситуаций существует блок `event`.
Событие состоит из условия и триггера (действий, которые необходимо выполнить).
Также блок `event` имеет ряд параметров.

- `settedUpInitially` Атрибут, позволяющий указать взведено ли событие при старте программы.
  Событие может быть взведено или спущено (setted up и dropped).
  Во взведенном состоянии событие выполняет свой триггер по выполнению своего условия,
  в спущенном оно просто игнорируется системой.
- `id` Уникальный идентификатор события. По этому идентификатору
  можно обращаться к данному событию из других. Опциональный.
- `dropsOnFire` Логический атрибут, который указывает
  продолжать ли быть событию взведенным после его срабатывания или нет.

Рассмотрим событие, выводящее консоль сообщение о первом посещении зоны финиша.

```xml
<event id="finish checker" settedUpInitially="true" dropsOnFire="true">
	<condition>
		<inside objectId="robot1" regionId="finish"/>
	</condition>
	<trigger>
		<message text="Вы достигли финиша"/>
	</trigger>
</event>
```

Данное событие изначально взведено (так как нет дополнительных предусловий) и перестанет быть
взведенным после выполнения (так как мы хотим сообщить только о первом посещении).

Одним из важных действий разрешенных в блоке `trigger` является `success`,
при его исполнении программа завершит работу без ошибок. Данное действие обязательно
должно выполняться в триггерах проверяющих завершение выполнения задания. Дополним предыдущий пример.

```xml
<event id="finish checker" settedUpInitially="true" dropsOnFire="true">
	<condition>
		<inside objectId="robot1" regionId="finish"/>
	</condition>
	<trigger>
		<message text="Вы достигли финиша"/>
        <success/>
	</trigger>
</event>
```

С `success` связан одно особое событие с `id="on_success"`,
данный триггер выполняется после завершения работы программы. Его удобно использовать, например,
для вывода набранных баллов.

```xml
<event id="on_success">
    <condition>
        <equals>
            <bool value="true"/>
            <bool value="true"/>
        </equals>
    </condition>
	<trigger>
        <message text="Набрано баллов: %\text1%" >
            <replace var="text1">
                <variableValue name="score"/>
            </replace>
        </message>
	</trigger>
</event>
```

> Для распознавания TestSys результата проверки сообщение должно
> соответствовать следующему формату: "Набрано баллов: х", где х – количество баллов.
> В случае отсутствия такого вывода все решения будут оценены в 0 баллов.
> { style="warning" }

Рассмотрим процесс разработки ограничений для задачи, в которой робот должен посетить по порядку 3 зоны:
за посещение каждой зоны начисляется 10 баллов.
Также робот не должен покидать границы рабочей зоны и остановиться в последней зоне.
В данном примере существенной роли не играет какие датчики использует робот,
как он навигируется между зонами и как расположены сами зоны. Выбор
этих параметров остается на усмотрение пользователя.

Сначала необходимо настроить конфигурацию робота и другие параметры в TRIK Studio,
а затем сохранить модель мира.
Далее представлен пример добавления логических зон и ограничений для описанной задачи.


```xml
<?xml version='1.0' encoding='utf-8'?>
<root version="20190819">

    <!-- Неизменно относительно сохраненного файла кроме regions -->
    <world>
        <walls/>
        <skittles/>
        <balls/>
        <colorFields/>
        <images/>
        <!-- Логические зоны -->
        <regions>
            <region filled="true" text="work zone" textY="0" y="0" visible="true" width="1000" id="work_zone" type="rectangle" color="#00aaaa" height="1000" x="0" textX="300"/>
            <region filled="true" text="z1" textY="0" y="100" visible="true" width="50" id="zone1" type="rectangle" color="#ff0000" height="50" x="300" textX="0"/>
            <region filled="true" text="z2" textY="0" y="200" visible="true" width="50" id="zone2" type="rectangle" color="#ff0000" height="50" x="600" textX="0"/>
            <region filled="true" text="z3" textY="0" y="300" visible="true" width="50" id="zone3" type="rectangle" color="#ff0000" height="50" x="900" textX="0"/>
        </regions>
        <comments/>
        <robot direction="0" position="0:0">
            <startPosition direction="0" y="25" id="{f1dd87ed-2b26-482b-bc5d-b3cd2c0e8c79}" x="25"/>
        </robot>
    </world>
    
    <!-- Неизменно относительно сохраненного файла -->
    <robots>
        <robot id="trikKitRobot">
            <sensors>
                <sensor direction="0" type="trik::robotModel::parts::TrikInfraredSensor" port="A1###input###А1###sensorA1" position="60:26"/>
            </sensors>
            <wheels right="M3###output###М3###" left="M4###output###М4###"/>
        </robot>
    </robots>

    <!-- Неизменно относительно сохраненного файла -->
    <settings realisticPhysics="false" realisticMotors="false" realisticSensors="false"/>

    <!-- Ограничения -->
    <constraints>
        <!-- Устанавливаем ограничения на время выполнения -->
        <timelimit value="60000"/>

        <!-- Проверяем конфигурацию робота -->
        <!-- Значения выбраны для примера -->
        <constraint checkOnce="true" failMessage="Неправильная конфигурация робота">
            <equals>
                <typeOf objectId="robot1.A1"/>
                <string value="twoDModel::robotModel::parts::RangeSensor"/>
            </equals>
        </constraint>

        <!-- Инициализируем переменную для хранения набранных баллов -->
        <init>
            <setter name="score">
                <int value="0"/>
            </setter>
        </init>

        <!-- Событие посещения 1 зоны -->
        <!-- Событие взведено изначально так как это 1 зона, которую посетит робот -->
        <!-- Событие опускается после исполнения, так как мы хотим чтобы баллы начислялись однократно --> 
        <!-- В условие: проверяем посещение зоны 1 -->
        <!-- В триггере: прибавляем 10 баллов -->
        <!-- В триггере: взводим событие проверяющее посещение 2 зоны -->
        <event id="zone1_checker" settedUpInitially="true" dropsOnFire="true">
            <condition>
                <inside objectId="robot1" regionId="zone1"/>
            </condition>
            <triggers>
                <setter name="score">
                    <sum>
                        <variableValue name="score"/>
                        <int value="10"/>
                    </sum>
                </setter>
                <setUp id="zone2_checker"/>
            </triggers>
        </event>

        <!-- Событие посещения 2 зоны -->
        <!-- Событие не взведено изначально так как сначала робот должен посетить зону 1-->
        <!-- Событие опускается после исполнения, так как мы хотим чтобы баллы начислялись однократно -->
        <!-- В условие: проверяем посещение зоны 2 -->
        <!-- В триггере: прибавляем 10 баллов -->
        <!-- В триггере: взводим событие проверяющее посещение 3 зоны -->
        <event id="zone2_checker" settedUpInitially="false" dropsOnFire="true">
            <conditions glue="and">
                <inside objectId="robot1" regionId="zone2"/>
            </conditions>
            <triggers>
                <setter name="score">
                    <sum>
                        <variableValue name="score"/>
                        <int value="10"/>
                    </sum>
                </setter>
                <setUp id="zone3_checker"/>
            </triggers>
        </event>

        <!-- Событие посещения 3 зоны -->
        <!-- Событие не взведено изначально так как сначала робот должен посетить зону 2-->
        <!-- Событие опускается после исполнения, так как мы хотим чтобы баллы начислялись однократно -->
        <!-- В условие: проверяем посещение зоны 2 -->
        <!-- В условие: проверяем что робот выключил моторы -->
        <!-- В триггере: прибавляем 10 баллов -->
        <!-- В триггере: вызываем success поскольку выполнение задачи завершено -->
        <event id="zone3_checker" settedUpInitially="false" dropsOnFire="true">
            <conditions glue="and">
                <inside objectId="robot1" regionId="zone3"/>
                <equals>
                    <objectState object="robot1.M3.power"/>
                    <int value="0"/>
                </equals>
                <equals>
                    <objectState object="robot1.M4.power"/>
                    <int value="0"/>
                </equals>
            </conditions>
            <triggers>
                <setter name="score">
                    <sum>
                        <variableValue name="score"/>
                        <int value="10"/>
                    </sum>
                </setter>
                <success/>
            </triggers>
        </event>

        <!-- Событие покидания рабочей зоны -->
        <!-- В триггере: вызываем success поскольку выполнение задачи завершено, так как робот покинул рабочую зону -->
        <event id="work_zone_checker" settedUpInitially="true" dropsOnFire="true">
            <condition>
                <not>
                    <inside objectId="robot1" regionId="work_zone"/>
                </not>
            </condition>
            <triggers>
                <success/>
            </triggers>
        </event>

        <!-- Выводим набранные баллы -->
        <event id="on_success">
            <condition>
                <equals>
                    <bool value="true"/>
                    <bool value="true"/>
                </equals>
            </condition>
            <triggers>
                <message text="Набрано баллов: %\text1%">
                    <replace var="text1">
                        <variableValue name="score"/>
                    </replace>
                </message>
            </triggers>
        </event>

    </constraints>
</root>
```

## Подготовка тура {id="developer-chapter-prepare-tour"}

Тур состоит из набора задач, даты начала, даты окончания и времени на прохождение.
Дата начала и дата окончания регулируют в какое время участник сможет приступить к выполнению,
а "время на прохождение" регулирует сколько у участника будет времени на выполнение задач.

<snippet id="task_warning">

> После прикрепления задачи к туру ее нельзя будет изменять, за исключением названия и описания
> { style="warning" }

</snippet>

После того как вы создали тур его можно будет опубликовать — все организаторы из той же группы пользователей
(все, в случае публичной) смогут добавить его в свои группы.

<snippet id="contest_warning">

> После публикации тура нельзя менять набор задач, также нельзя отменить публикацию
> { style="warning" }

</snippet>


[//]: # (--------------------------------------------------------------------------------------------------------------)
## Вход в систему {id="developer-chapter-enter-system"}

### Видео инструкция {collapsible="true" default-state="%video_header_default_state%"}
<include from="common.md" element-id="auth-video"/>

### Текстовая инструкция {collapsible="true" default-state="%text_header_default_state%"}
<include from="common.md" element-id="auth-text"/>

[//]: # (--------------------------------------------------------------------------------------------------------------)
## Загрузка файлов {id="developer-chapter-upload-file"}

### Видео инструкция {collapsible="true" default-state="%video_header_default_state%"}
<video src="$PROJECT_DIR$/TestSys/video/developer/developer_upload_file.mp4" preview-src="video_preview.png"/>

### Текстовая инструкция {collapsible="true" default-state="%text_header_default_state%"}
Для загрузки файлов вам необходимо выполнить следующие шаги:

1. Нажмите на кнопку "Файлы" в <tooltip term="menu">меню</tooltip>
2. В открывшемся разделе нажмите на кнопку "Создать файл"
3. Введите название и выберите тип загружаемого файла
4. Выберите файл, который хотите загрузить
5. Нажмите на кнопку "Создать"

Созданный файл отобразится на текущей странице в соответствующей таблице.

[//]: # (--------------------------------------------------------------------------------------------------------------)
## Создание задачи {id="developer-chapter-create-task"}

### Видео инструкция {collapsible="true" default-state="%video_header_default_state%"}
<video src="$PROJECT_DIR$/TestSys/video/developer/developer_create_task.mp4" preview-src="video_preview.png"/>

### Текстовая инструкция {collapsible="true" default-state="%text_header_default_state%"}
Для создания вам необходимо выполнить следующие шаги:

1. Нажмите на кнопку "Задачи" в <tooltip term="menu">меню</tooltip>
2. В открывшемся разделе нажмите на кнопку "Создать задачу"
3. Заполните название и нажмите кнопку "Создать"

Созданная задача отобразится в списке задач, далее необходимо прикрепить к ней файлы:

1. Нажмите на название созданной задачи
2. Прикрепите необходимые файлы
3. Запустите тестирование

> Рекомендуется ориентироваться не только на статус тестирования, но и на количество набранных эталонным решением
> баллов, с баллами можно ознакомится при помощи инструкции "Просмотр результатов"

[//]: # (--------------------------------------------------------------------------------------------------------------)
## Просмотр результатов {id="developer-chapter-view-result"}

### Видео инструкция {collapsible="true" default-state="%video_header_default_state%"}
<video src="$PROJECT_DIR$/TestSys/video/developer/developer_test_tesult.mp4" preview-src="video_preview.png"/>

### Текстовая инструкция {collapsible="true" default-state="%text_header_default_state%"}
Для просмотра результатов вам необходимо выполнить следующие шаги:

1. Нажмите на кнопку "Задачи" в <tooltip term="menu">меню</tooltip>
2. Нажмите на название интересующей задачи
3. В открывшемся разделе нажмите на кнопку "Результаты тестирования"


[//]: # (--------------------------------------------------------------------------------------------------------------)
## Создание и публикация тура {id="developer-chapter-create-tour"}

### Видео инструкция {collapsible="true" default-state="%video_header_default_state%"}
<video src="$PROJECT_DIR$/TestSys/video/developer/developer_contest_pipeline.mp4" preview-src="video_preview.png"/>

### Текстовая инструкция {collapsible="true" default-state="%text_header_default_state%"}
Для создания тура вам необходимо выполнить следующие шаги:

1. Нажмите на кнопку "Туры" в <tooltip term="menu">меню</tooltip>
2. В открывшемся разделе нажмите на кнопку "Создать тур"
3. Заполните необходимые поля и нажмите на кнопку "Создать"

Созданная тур отобразится в списке туров, далее необходимо прикрепить к нему задачи:

<include from="developer.md" element-id="task_warning"/>

1. Нажмите на название созданного тура
2. Прикрепите необходимые задачи

После наполнения тура задачами его можно опубликовать:

<include from="developer.md" element-id="contest_warning"/>

1. Нажмите на название созданного тура
2. Выберите группу для которой хотите опубликовать тур
3. Нажмите кнопку "Добавить группу"
