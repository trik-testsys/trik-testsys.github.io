# Организатор

**<tooltip term="manager">Организатор</tooltip>** является ролью ответственной за администрирование групп участников.
Данный пользователь имеет возможность создавать группы, добавлять в них туры, 
генерировать участников и выгружать информацию о них.

[//]: # (--------------------------------------------------------------------------------------------------------------)
## Регистрация {id="manager-chapter-registration"}

### Видео инструкция {collapsible="true" default-state="%video_header_default_state%"}
<video src="$PROJECT_DIR$/TestSys/video/manager/manager_reg.mp4" preview-src="video_preview.png"/>

### Текстовая инструкция {collapsible="true" default-state="%text_header_default_state%"}
Для регистрации в системе вам необходимо выполнить следующие шаги:

1. Получите код-регистрации от %registration_code_distributor%
2. Зайдите на сайт TestSys по [ссылке](%site_link%)
3. Нажмите на кнопку "Зарегистрироваться"
4. В поле "Код-регистрации" введите раннее полученный код-регистрации
5. В поле "Псевдоним" введите %manager_alias%
6. Нажмите на кнопку "Зарегистрироваться"
7. В открывшемся личном кабинете найдите поле "Код-доступа" и сохраните его

> Обязательно сохраните полученный код-доступа, при повторной регистрации вы получите **другой** кабинет
{style="warning"}

[//]: # (--------------------------------------------------------------------------------------------------------------)
## Вход в систему {id="manager-chapter-enter-system"}

### Видео инструкция {collapsible="true" default-state="%video_header_default_state%"}
<include from="common.md" element-id="auth-video"/>

### Текстовая инструкция {collapsible="true" default-state="%text_header_default_state%"}
<include from="common.md" element-id="auth-text"/>

[//]: # (--------------------------------------------------------------------------------------------------------------)
## Создание группы {id="manager-chapter-create-group"}

### Видео инструкция {collapsible="true" default-state="%video_header_default_state%"}
<video src="$PROJECT_DIR$/TestSys/video/manager/manager_create_group.mp4" preview-src="video_preview.png"/>

### Текстовая инструкция {collapsible="true" default-state="%text_header_default_state%"}
Для создания группы вам необходимо выполнить следующие шаги:

1. Нажмите на кнопку "Группы" в <tooltip term="menu">меню</tooltip>
2. В открывшемся разделе нажмите на кнопку "Создать группу"
3. Заполните поле "Название группы", по желанию заполните поле "Описание"
4. Нажмите на кнопку "Создать"

[//]: # (--------------------------------------------------------------------------------------------------------------)
## Добавление тура {id="manager-chapter-add-tour"}

### Видео инструкция {collapsible="true" default-state="%video_header_default_state%"}
<video src="$PROJECT_DIR$/TestSys/video/manager/manager_add_contest.mp4" preview-src="video_preview.png"/>

### Текстовая инструкция {collapsible="true" default-state="%text_header_default_state%"}
Для добавления тура вам необходимо выполнить следующие шаги:

1. Нажмите на кнопку "Группы" в <tooltip term="menu">меню</tooltip>
2. В открывшемся разделе нажмите на название группы в которую вы хотите добавить тур
3. В открывшемся разделе в таблице "Доступные к прикреплению туры" найдите тур который вы хотите добавить
4. Нажмите кнопку "Прикрепить"

> Добавленный тур будет отображаться в таблице "Прикрепленные туры"

[//]: # (--------------------------------------------------------------------------------------------------------------)

## Генерация участников {id="manager-chapter-generate-students"}

### Видео инструкция {collapsible="true" default-state="%video_header_default_state%"}
<video src="$PROJECT_DIR$/TestSys/video/manager/manager_generate_students.mp4" preview-src="video_preview.png"/>

### Текстовая инструкция {collapsible="true" default-state="%text_header_default_state%"}
Для генерации участников вам необходимо выполнить следующие шаги:

1. Нажмите на кнопку "Группы" в <tooltip term="menu">меню</tooltip>
2. В открывшемся разделе нажмите на название группы для которой вы хотите сгенерировать участников
3. В открывшемся разделе введите необходимое количество участников
4. Нажмите кнопку "Сгенерировать"
5. Для получения файла со сгенерированными участниками нажмите кнопку "Экспорт CSV"


[//]: # (--------------------------------------------------------------------------------------------------------------)
## Скачивание результатов {id="manager-chapter-download-results"}

### Видео инструкция {collapsible="true" default-state="%video_header_default_state%"}
<video src="$PROJECT_DIR$/TestSys/video/manager/manager_export_results.mp4" preview-src="video_preview.png"/>

### Текстовая инструкция {collapsible="true" default-state="%text_header_default_state%"}
Для скачивания результатов вам необходимо выполнить следующие шаги:

1. Нажмите на кнопку "Группы" в <tooltip term="menu">меню</tooltip>
2. В открывшемся разделе нажмите на название группы для которой вы хотите скачать результаты
3. В открывшемся разделе нажмите на кнопку "Скачать результаты"
