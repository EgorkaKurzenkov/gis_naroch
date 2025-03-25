# Техническое обеспечение и администрирование геоинформационных систем в деятельности ООПТ

## 1. Развертывание и настройка ArcGIS Enterprise

Список портов, которые необходимы для работы ArcGIS Enterprise (необходимо открыть в брандмауэре):
- сервер: 6443, 6080, 6006;
- портал: 7080, 7443;
- датастор: 2443, 9876, 29080, 29081, 4369, 29079, 29085, 29090, 9220, 9320, 6443, 9829. 

[Развертывание системы ArcGIS Enterprise](https://blogs.esri-cis.com/2022/07/14/arcgis_enterprise_deployment1091/):

- [Руководство по установке ArcGIS Server 10.9.1 (Windows)](https://enterprise.arcgis.com/ru/server/10.9.1/install/windows/welcome-to-the-arcgis-for-server-install-guide.htm)
  
- [Руководство по установке Portal for ArcGIS 10.9.1 (Windows)](https://enterprise.arcgis.com/ru/portal/10.9.1/install/windows/welcome-to-the-portal-for-arcgis-installation-guide.htm)
  
- [Руководство по установке ArcGIS Data Store 10.9.1 (Windows)](https://enterprise.arcgis.com/ru/data-store/10.9.1/install/windows/welcome-to-arcgis-data-store-installation-guide.htm)
  
- [Руководство по установке ArcGIS Web Adaptor 10.9.1 (Java Platform)](https://enterprise.arcgis.com/ru/web-adaptor/10.9.1/install/java-windows/welcome-arcgis-web-adaptor-install-guide.htm)

[Подводные камни установки серверных продуктов ArcGIS](https://blogs.esri-cis.ru/2015/06/08/underwater-stones-installation-arcgis-server/)

## 2. Развертывание настройка PostgreSQL, интеграция с ArcGIS Desktop

[]()

Версии ПО в ГИС ГПУ "НП "Нарочанский":
- ArcGIS Enterprise (ArcGIS Server, Portal for ArcGIS, ArcGIS Data Store, ArcGIS Web Adaptor) все версии 10.9.1.
- PostgreSQL 10.8.
- ArcGIS Desktop 10.8 или 10.6.1.
- ArcGIS Pro 3.0.0.

Все это корректно работает между собой.

[Требования к версии PostgreSQL ПО ArcGIS оф. документация](https://enterprise.arcgis.com/ru/system-requirements/10.9.1/windows/database-requirements-postgresql.htm)

По умалчанию PostgreSQL использует порт 5432, его необходимо открыть в брандмауэре.

### Шаг 1. Установка

[Установка PostreSQL](https://metanit.com/sql/postgresql/1.1.php)

### Шаг 2. Настройка входящих подключений

[Настраиваем подключения через файл pg_hba.conf](http://postgrespro.ru/docs/postgrespro/10/auth-pg-hba-conf)

Файл по умолчанию находится в директории, которая была указана при установке PostreSQL для хранения базы данных.

[Наш рабочий файл pg_hba.conf](http://gisoopt.by/gis/data/temp/pg_hba.rar)


### Шаг 3. Добавление типа ST_Geometry в базу данных PostgreSQL

Для соединения с СУБД PostreSQL ПО ArcGIS Desktop  необходимо добавть библиотеку ST_Geometry в директорию установки PostgreSQL, для этого:
- из директории установки ArcGIS Desktop (по умолчанию c:\Program Files (x86)\ArcGIS\Desktop10.8\DatabaseSupport\PostgreSQL\) для соответсвующей версии СУБД и операционной системы скопировать файл st_geometry.dll;
- перейти в директорию установки СУБД PostreSQL (по умолчанию c:\Program Files\PostgreSQL\10\lib\) и вставить скопированую библиотеку.

Как должно выглядеть подключение к многопользовательской БД в ArcGIS Desktop / ArcGIS Pro:

![](https://github.com/a91n0d/gis_naroch/blob/main/source/connect_DB.jpg?raw=true)

где:
- 1 - Имя платформы БД;
- 2 - Адрес сервера;
- 3 - Имя пользователя и пароль;
- 4 - Выбор подключаемой БД из перечня.

## 3. Создание многопользовательской базы данных в ArcGIS Pro

### Шаг 1. Создание многопользовательской БД

Для создания базы данных зайти на вкладку *Анализ* -> *Инструменты*.

![Панель управления ArcGIS Pro](https://github.com/user-attachments/assets/7c814672-f8ed-49e6-b916-112380cb8c80)

Во вкладке *Геообработка* ввести **Создать многопользовательскую базу данных**.

![Инструменты Геообработки](https://github.com/user-attachments/assets/a36bb97c-96a2-41a5-b05c-4b1369039e15)

Последовательность действий при создание многопользовательской БД:

1. в окне **`Создать многопользовательскую базу`** в поле *Платформа базы данных* указать PostgreSQL;
2. в поле *Экземпляр* – экземпляр БД;
3. в поле *База данных* – название базы данных;
4. в поле *Администратор базы данных* – postgres;
5. в поле *Пароль администратора базы данных* – пароль, заданный при установки PostgreSQL;
6. в поле *Пароль администратора базы данных* – повторно ввести предыдущий пароль;
7. в поле *Файл авторизации* – файл авторизации к ArcGIS Enterprise;
8. в поле *Пространственный тип* – PostGIS.

![Параметры создания многопользовательской БД](https://github.com/user-attachments/assets/dfdc6969-5208-4822-941b-804ad3fda836)

### Шаг 2. Подключение к созданной БД

После создания многопользовательской базы данных нажать правой кнопкой на Базы данных и в выпадающем списке выбрать Новое подключение к базе данных.

![Подключени к БД](https://github.com/user-attachments/assets/f1b20f09-734c-4c05-a94a-8eab0e31a27c)

Последовательность действий при подключении к БД:

1. в окне *Подключение базы данных* в поле *Платформа базы данных* указать PostgreSQL;
2. в поле *Экземпляр* – экземпляр базы данных;
3. в поле *Тип авторизации* – Аутентификация в базе данных;
4. в поле *Имя пользователя* – имя пользователя к базе данных;
5. в поле *Пароль* – пароль к данной базе;
6. в поле *База данных* в выпадающем списке выбрать необходимую базу;
7. поставить галочку напротив *Сохранить Пользователя\Пароль*.

![Параметры подключения БД](https://github.com/user-attachments/assets/73eba0f4-7895-4d86-972a-34bb74de3953)

### Шаг 3. Администрирование БД

На следующем этапе необходимо произвести администрирование базы данных. Для этого нажать правой клавишей мыши на добавленную базу данных в выпадающем списке выбрать *Администрирование* –> *Создать пользователя базы данных*. 

![Окно администрирования](https://github.com/user-attachments/assets/4d471acf-17c9-471e-8fcd-3013dda0d0c2)

Последовательность действий при подключении к БД

1. в появившемся окне **Создать пользователя базы данных** в поле *Входное подключение к базе данных* указать необходимую базу данных;
2. в поле *Пользователь базы данных* ввести имя пользователя;
3. в поле *Пароль пользователя базы данных* указать пароль пользователя.

![Параметры создания пользователя БД](https://github.com/user-attachments/assets/2006d8fa-3777-4597-bb63-c52c283dd985)

### Шаг 4. Создание нового подключения к БД

После создания нового пользователя в базе данных необходимо создать новое подключение к базе данных под данным пользователем. Для этого следуем инструкции Шаг 2.

![Создание нового подключения](https://github.com/user-attachments/assets/615c8731-80c8-49bd-81f9-fec167b807bf)

![Повторное подключение к БД](https://github.com/user-attachments/assets/cc19abe8-2996-4551-bb05-1d6a4cfb33ba)

![Результат подключения БД](https://github.com/user-attachments/assets/187d6010-250f-4a2b-b7e8-abc9548d6d89)

## 4. Создание слоя полевых обследований в ArcGIS Pro

### Шаг 1. Создание класса пространственных объектов

Для создания слоя в ArcGIS Pro необходимо выбрать *Вид* -> *Панель Каталог*.

![img20](https://github.com/user-attachments/assets/37912d37-88e3-483c-b7df-78e914469b6c)

В окне **Каталог** раскрыть *Базы данных* и нажать правой кномкой мыши на необходимую базу данных, в которой будет создан новый слой.

![img19](https://github.com/user-attachments/assets/82043ff4-62a6-472f-8e6f-06132c353a97)

Последовательность действий при создании класса пространственных объектов:

1. в поле *Имя* указать название слоя (обязательно к заполнению);
2. в поле *Псевдоним* указать псевдоним (обязательно к заполнению);
3. в поле *Тип класса пространственных объектов* указать тип объекта(например: полигон, точка, линия), в данном случае рассмотрим на примере создания точечного слоя;
4. нажимаем на кнопку *Следующая*;
5. на 3 странице указываем необходимую систему координат слоя и наживаем *Готово*.

![img21](https://github.com/user-attachments/assets/051ee4b0-9aa2-4828-b445-557dd4a03d67)

![img22](https://github.com/user-attachments/assets/7748c99a-e269-4aeb-986b-1386ac46f50e)

### Шаг 2. Настройка атрибутивной информации слоя

Для настройки атрибутивной информации и создания необходимых полей необходимо нажать правой кнопкой мыши на созданный слой и выбрать *Таблица атрибутов*.

![img23](https://github.com/user-attachments/assets/23254860-263c-4300-afb4-c335881d4568)

Переход к настройке атрибутивной информации осуществляется путём нажатия на три горизонтальные линии в правом верхнем углу всплывающего окна и выбором вкладки *Вид Поля* как показано на рисунке ниже.

![img24](https://github.com/user-attachments/assets/f04dd370-ecde-4dd2-8f3b-cf06e04b6b8a)

Для создать новое поля необходимо щелкнуть в специально отведённое место как показано на рисунке ниже. 

![img25](https://github.com/user-attachments/assets/8e2fc432-3b54-431b-a44d-d2aad88a7b6d)

Последовательность действий при создании нового поля:

1. указать имя поля (Имя поля обязательно должно быть на английском языке,с маленькой буквы и не содержать более 28 символов, иначе при публикации данного слоя в базу данных PotgresSQL поля не будут отображатся и его название автоматически обрежется согласно установленому количеству символов);
2. указать псевдоним (Псевдоним может быть на любом языке, но менее 32 символов);
3. указать тип данных (Здесь вы должны заранее определить какие поля вам необходимы и какого типа они будут)
   - если это название, поле с описание, они должны быть текстовыми (Тип -Текст);
   - если это количество растений, видов, то данные должны быть целочисленными (Тип - Short, Long);
   - если это поле площади, то данные должны быть дробными (Тип - Double, Float) и т.д.
4. обязательно необходимо сохранить слой после добавления полей и настройки атрибутивной информации слоя.

![img26](https://github.com/user-attachments/assets/95aec2c1-1da0-47bf-93dd-a24de7e796b9)

![img27](https://github.com/user-attachments/assets/07925fc1-af1f-4ecf-97a9-3a03aacc31c9)

## 5. Преднастройка слоёв в ArcGIS Pro

Для предварительной настройки слоя в базе данных необходимо нажать правой кнопкой мыши на слой и выбрать *Управление*.

![img29](https://github.com/user-attachments/assets/227bccb0-60cb-448c-9118-88bb7ce7f489)

В окне **Управление** производим преднастройку слоя (Рекомендуется производить настройку слоя согласно рисунку, представленному ниже).

![img28](https://github.com/user-attachments/assets/fdabac05-930b-4f62-ba53-ba47221d5ca6)

1. Поле *Управление версиями* позволяет выполнять редактирование одних и тех же данных в одно и то же время без установки блокировок или дублирования данных.

Подробнее с вопросом версионности можно ознакомится в официальной документации на  сайте ESRI по ссылкам представленным ниже.

(https://pro.arcgis.com/ru/pro-app/latest/help/data/geodatabases/overview/overview-of-versioning-in-arcgis-pro.htm)

(https://pro.arcgis.com/ru/pro-app/latest/help/data/geodatabases/overview/change-settings-for-editing-versioned-data.htm)

(https://pro.arcgis.com/ru/pro-app/latest/help/data/geodatabases/overview/versioning-tab.htm)

2. Поле *Архивирование* предоставляет механизм сбора сведений об изменении данных, управления ими и их анализа.

Подробнее с вопросом архивирования можно ознакомится в официальной документации на сайте ESRI по ссылкам представленным ниже.

(https://pro.arcgis.com/ru/pro-app/latest/help/data/geodatabases/overview/what-is-archiving-.htm)

3. Поле *Отслеживание реплики* включает отслеживание реплики для данных, позволяя работать с автономными картами и в распределенном сотрудничестве. Отслеживание реплики применяется к сервисам, настроенным с возможностью синхронизации с опцией создания версии для каждой загруженной карты.

Подробнее с вопросом отслеживания реплик можно ознакомится в официальной документации на сайте ESRI по ссылкам представленным ниже.

(https://pro.arcgis.com/ru/pro-app/latest/tool-reference/data-management/enable-replica-tracking.htm)

4. Поле *Вложения* предоставляет доступ к добавлению файловых вложений к записям класса пространственных объектов или таблицы базы геоданных. Вложения хранятся в базе геоданных в отдельной таблице вложений, которая связана с целевым набором данным. Вложения добавляются к целевому набору данных с помощью таблицы соответствия, которая указывает для каждой входной записи (или группы атрибутов записей) путь к файлу для добавления в качестве вложения к этой записи.
   
Подробнее с вопросом вложений можно ознакомится в официальной документации на сайте ESRI по ссылке представленной ниже.  

(https://pro.arcgis.com/ru/pro-app/latest/tool-reference/data-management/add-attachments.htm)

5. В поле *Глобальные идентификаторы* рекомендуем всегда ставить галочку. Данное поле добавляет идентификаторы в перечень классов пространственных объектов базы геоданных, таблиц и наборов классов объектов.

Подробнее с вопросом глобальных идентификаторов можно ознакомится в официальной документации на сайте ESRI по ссылке представленной ниже.  

(https://pro.arcgis.com/ru/pro-app/latest/tool-reference/data-management/add-global-ids.htm)

6. Поле *Отслеживание изменений* предоставляет доступ для отслеживания изменений в слое во время редактирования и формирует записи о данных изменениях.

Подробнее с вопросомотслеживания изменений можно ознакомится в официальной документации на сайте ESRI по ссылке представленной ниже.  

(https://pro.arcgis.com/ru/pro-app/latest/tool-reference/data-management/disable-editor-tracking.htm)

## 6. Публикация данных из ArcGIS Pro на портал ArcGIS Enterprise

### Шаг 1. Подключение к порталу в ArcGIS Pro

В правом верхнем углу проекта ArcGIS Pro необходимо произвести вход на портал ArcGIS Enterprise.

![img13](https://github.com/user-attachments/assets/06dc23c0-a892-4d5b-81b7-2ab28ccafdbd)

В появившемся окне нажать на *Добавить портал* либо выбрать уже имеющийся портал и войти в него.

![img14](https://github.com/user-attachments/assets/50cf67d0-d53b-4396-84dc-a657a80b57f1)

В всплывающем окне *Добавить портал* ввести URL ссылку вашего портала и нажать *ОК*.

![img15](https://github.com/user-attachments/assets/f01fdb07-c5df-41c5-bbad-2d026bd9b1db)

Во вкладке **Порталы** справа от URL ссыллки портала нажать на ... и выбрать *Войти*.

![img31](https://github.com/user-attachments/assets/e1b18d21-14c1-4964-bd1f-52e1e2d0900b)

В сплывающем окне указать учётные данные к порталу ArcGIS Enterprise и нажать *Вход*.

![img17](https://github.com/user-attachments/assets/7f2824a9-139a-4916-9f55-9de767dc9171)

Если у вас несколько активных порталов, то для переключения на нужный необходимо нажать на ... справа от портала и выбрать *Задать как Активный портал*.

![img32](https://github.com/user-attachments/assets/ecd799a4-660b-40ab-8042-db60ea8d0bfd)

Убедиться, что вы вошли под своей учётной записью на портал можно в правом верхнем углу, согласно рисунку ниже.

![img33](https://github.com/user-attachments/assets/bcd50a97-698f-4a4d-bbbe-0712d7afc620)

### Шаг 2. Публикация слоя на портал

Для публикации слоя необходимо выгрузитиь необходимый слой из базы данных в проект. Затем нажать парвой кнопкой мыши на слой и выбрать *Публикация* -> *Опубликовать как веб-слой*.

![img30](https://github.com/user-attachments/assets/eda4957a-fd3b-4851-a4c0-00d9c024b98b)

Последовательность действий при настройке окна **Общие** при публикации выбранного слоя как веб-слой:

1. во вкладке **Информация об элементе** в поле *Имя* указать имя веб-слоя (желательно указывать имя на английском, обязательно к заполнению);
2. в поле *Краткая информация* указать информацию о назначении данного слоя (обязательно к заполенению);
3. в поле *Теги* указать любой тег (обязательно к заполнению);
4. во вкладке **Данные и тип поля** выбрать *Изображение карты*  -> *Объект*;
5. во вкладе **Местоположение** в поле *Папка Portal* указать необходиму папку для сохранения (обязательно для заполнения);
6. в поле *Сервер и папка* указать необходимый сервер и папку для публикации (обязательно для заполнения);
7. во вкладке **Общий доступ с помощью** производится распределение доступа к данному слою (настраивается при необходимости).

![img34](https://github.com/user-attachments/assets/f70fcfb3-0202-441f-99c2-bdc6936fb13d)

Преднастройку окна **Настройка** при публикации выбранного слоя произвести согласно рисунку ниже.

![img35](https://github.com/user-attachments/assets/17d135b5-5680-429f-a1b5-f36bd60e466e)

После настройки вернуться к окну **Общие** и нажать кнопку *Анализировать*.

![img36](https://github.com/user-attachments/assets/e35a72ea-bb86-4348-a59d-c7db87e705c8)

В окне **Сообщения** часто при публикации возникает ошибка *Уникальные числовые ID не назначены*, для исправления данной ошибки необходимо нажать на ... и выбрать *Автоматическое последовательное назначение ID*.

![img37](https://github.com/user-attachments/assets/ae683b56-ebe3-4792-b5cd-995abe0f7937)

После исправления всех ошибок желательно повторно нажать на кнопку *Анализировать*.

![img38](https://github.com/user-attachments/assets/e621d84a-3b19-480b-8f65-8657c5bc74ad)

Опубликованные слои на портале выглядят следующим образом (на рисунке представлено 2 слоя, первый слой Feature Layer - слой объектов, который можно используется как для редактирования, так и  для просмотра; второй слой Map Image Layer - слой карты, который используется исключительно для просмотра).

![img39](https://github.com/user-attachments/assets/f5a72828-90f1-4693-aa6e-e208d8b01aed)

## 7. Создание пакета листов карт в ArcGIS Pro

### Шаг 1. Пакет листов карт

Формирование пакета листов карт позволяет объединять растровые и веторные слои, упаковывать их в один пакет листов или несколько небольших пакетов листов. Данная операция позволят формировать базовые тематические подложки как при работе в мобильной ГИС ArcGIS Field Maps, так и на портал ArcGIS Enterprise,  для реализации различных научно-практических исследований и сбора полевых данных.

Подробнее с вопросом создания пакета листов карт можно ознакомится в официальной документации на  сайте ESRI по ссылке представленной ниже.

(https://pro.arcgis.com/ru/pro-app/latest/tool-reference/data-management/create-map-tile-package.htm)

### Шаг 2. Концепция создания базовых подложек для ООПТ

Концепция формирования базовых подложек в виде пакета листов карт:

1. выгрузить слой городов, населённых пунктов из общедоступной базы OSM (данный слой выполняет функцию маршрутизации и ориентации на местности);
2. выгрузить слой рек, водотоков и каналов из общедоступной базы OSM (данный слой выполняет информационну функцию о гидрологических объектах);
3. выгрузить слой озёр, водохранилищ и болотных комплексов из общедоступной базы OSM (данный слой выполняет информационну функцию о гидрологических объектах);
4. выгрузить слой дорог и путей сообщения различной велечины из общедоступной базы OSM (данный слой выполняет функцию маршрутизации и планирования полевых обследований, позволяет определить оптимальные пути проезда к зоне интересов, особенно подъезд и подход к трудодоступным местам);
5. добавления слоя лесничеств, кварталов и выделов (данный слой является основным при любых работах в ООПТ, позволяет ориентироваться и привязываться к квартально-выделенной сети);
6. добавление слоя границ ООПТ (вспомогательная информация территории ООПТ);
7. создание актуальной высокодетальной мозаики спутниковых снимков на территории ООПТ (является основой для понимания местности и более точного представления выше упомянутых данных).

Общий вид подложки при различных уровнях масштабирования.

![img54](https://github.com/user-attachments/assets/34c62499-1f75-4415-95ba-8116fdd74783)

![img55](https://github.com/user-attachments/assets/72701e46-ec71-4c31-bb21-abd97164156d)

![img56](https://github.com/user-attachments/assets/262037ba-05cf-463a-a386-edef7f932172)

### Шаг 3. Последовательность действий при создании пакета листов карт

Рассмотрим создание пакета листов карт на территорию ГПУ НП "Нарочанский". 

Инструкция по созданию пакета листов карт:

1. задаём необходимую систему координат карты;

   Нажимаем правой кнопкой мыши на вкладку **Карта** и выбираем *Свойства*.

   ![img57](https://github.com/user-attachments/assets/f3a2b283-3663-4a22-b759-3a5790be82fe)

   В появившемся окне выбираем вкладку **Системы координат**.
   
   ![img58](https://github.com/user-attachments/assets/19e01fd7-96d5-4b1b-9e2f-4647c5829f8d)

   В появившемся окне указываем необходимую систему координат и нажимаем *ОК*.

   ![img59](https://github.com/user-attachments/assets/ccaa2bb9-5359-4ac9-aaea-a2e5b219d54b)

 2. загружаем все необходимые слои для создания пакета листов;

 3. проверка систем координат всех слоёв карты (при создании пакета листов все слои должны быть приведены к единой системе координат иначе пакет листов не сформируется);

    Проверить систему координат слоя можно путём нажатия правой кнопкой мыши на слой и выбрать *Свойства*.
    
    ![img60](https://github.com/user-attachments/assets/a9909acb-5f82-4dc3-bee4-7053e670b3ae)

    В появившемся окно выбрать вкладку **Источник**.

    ![img61](https://github.com/user-attachments/assets/a0a2b5ab-53bb-429d-8094-f91fff145121)

    Затем необходимо раскрыть вкладку **Пространственная привязка** и убедится, что все слои в вашей карте имеют единую систему координат.

    ![img62](https://github.com/user-attachments/assets/e78684fa-7877-448a-9162-f505a94394ee)

    ![img63](https://github.com/user-attachments/assets/f614aa71-288e-4484-a8d2-51005d7ee149)

    В случае, если слои имеют разные системы координат, например все слои OSM имеют систему координат WGS 1984 их необходимо перепроецировать в WGS 1984 UTM Zone 35N (смотреть пункт 4).

 4. перепроецирование слоёв;

    Последовательность действий при перепроецировании слоёв:
    
    - на панели задач выбрать вкладку **Анализ** -> **Инструменты** как показано на рисунке ниже;

      ![img64](https://github.com/user-attachments/assets/8d0ff62f-2327-409e-ab3f-3c3eee9f9ee9)

    - во вкладке **Геообработка** в поисковой строке вписать *Проецировать* и выбрать инструмент (для проецирования векторных данных используем инструмент *Проецировать*, а для проецирования растровых данных используем - *Проецировать растр*);

      ![img65](https://github.com/user-attachments/assets/e2e7c19d-7d31-4731-962b-9a1b1b303fe7)

    - в параметрах инструмента **Проецировать** в поле *Входной набор классов объектов или класса объектов* выбираем слой для проецирования, в поле *Выходная система координат* выбираем систему координат в которую мы проецируем данный слой и нажимаем кнопку *Запустить*;

      ![img66](https://github.com/user-attachments/assets/1f88ad0a-5be8-4751-adad-e2a276bcaf44)

  5. масштабирование, настройка слоя объектов, надписей и символов (даннау тему рассмотрим в пункте ............................., сделать ссылку на этт пункт);

  6. обязательно удалить базовые карты из проекта

  ![img74](https://github.com/user-attachments/assets/329d4e54-e696-4c44-94bc-00bc6373391a)
  
  7. создание пакета листов карты;

     Последовательность действий при создании листов карты:

     - переходим во вкладку **Геообработка** и вписываем в поисковую строку *Создать пакет листов карты*;
       
       ![img67](https://github.com/user-attachments/assets/5e4f2652-fa82-4654-ad1a-81f71efe3778)

     - в параметрах инструмента **Создать пакет листов карты** в поле *Входная карта* выбрать карту, в поле *Выходной файл* выбрать место для сохранения, в поле *Формат разбиения на листы* выбрать *PNG - 8 битовой*, в поле *Минимальный уровень детализации* выбрать *0 (591657527,591555)*, в поле *Максимальный уровень детализации* выбрать *16 (9027,977411)*,      в поле *Теги* ввести тег, все остальные параметрыф оставить по умолчанию и нажать *Запустить*;

       ![img68](https://github.com/user-attachments/assets/4ccb7aad-046a-4367-9c16-6ccac88274bb)
       
## 8. Публикация пакета листов карты на портал ArcGIS Enterprise

Публикация пакета листов карты производится во вкладке **Ресурсы** -> **Мои ресурсы** -> **Новый элемент**.

![img69](https://github.com/user-attachments/assets/41c17914-725b-4aa4-b34a-7ef637ec9299)

В появившееся окно перетащить файл подложки с разрешением .tpkx.

![img70](https://github.com/user-attachments/assets/aadc8227-7167-438b-9df4-f4a54c5199a4)

На следующем этапе выбрать *Добавить пакет листов и создать размещённый слой листов* и нажать кнопку *Далее*.

![img71](https://github.com/user-attachments/assets/bac56b92-47c3-42a2-a5ed-c2c97cbbcb3b)

В окне **Новый элемент** в поле *Название* указать название слоя, в поле *Папка* указать папку для сохранения на портале, в поле *Теги* указать теги, в поле *Краткая информация* указать краткую информацию о публикуемом слое и нажать кнопку *Сохранить*.

![img72](https://github.com/user-attachments/assets/c6a98668-49f0-43ce-9490-89ae27b5563b)

Далее происходить процесс публикации слоя на портал. В появившемся окне во вкладке **Tile Layer** поставить галочку в поле *Автономный режим*, для разрешения загрузки этого слоя и использование его на автономной карте и нажать кнопку *Сохранить*.

![img73](https://github.com/user-attachments/assets/c958217e-59c5-43ec-a4e1-ee2fb72f293b)

## 9. Создание веб-карты полевых обследований на портале ArcGIS Enterprise

Создать веб-карту в ArcGIS Enterprise можно двумя способами.

**1 Способ**. Нажимаем на слой, на основе которого будет создаваться веб-карта как показано на рисунке ниже (Если данный слой будет использован для редактирования, то добавлять в карту необходимо слой Feature Layer).

![img40](https://github.com/user-attachments/assets/a1c5cda5-a87d-4d5d-8307-c095b5aac418)

В появившемся окне в левом углу нажимаем на надпись *Открыть в Map Viewer Classic*, либо в правом углу на кнопку, как показано на рисунке ниже.

![img41](https://github.com/user-attachments/assets/91626750-1b7b-4700-a2fa-550ce5c9f573)

**2 Способ**. На панели выбираем вкладку **Карта** как показано на рисунке ниже.

![img44](https://github.com/user-attachments/assets/d3cd43b2-cf61-43fe-ad88-12bdb1d87a9d)

Для добавления слоя на карту выбрать вкладку *Добавить* -> *Поиск слоёв*.

![img45](https://github.com/user-attachments/assets/8dc6070b-06dc-448d-a193-63523c877401)

Для добавления нового слоя необходимо ввести название слоя в поисковой строке, либо выбрать из списка и нажать на + в правом нижнем углу слоя как показано на рисунке ниже.

![img43](https://github.com/user-attachments/assets/d2a34415-3afd-422f-877c-276c61c77a71)

После добавления в карту всех необходимых слоёв, обязательно нужно удалить базовую карту. Для этого нажать левой клавишей мыши на вкладку *Топографический*.

![img75](https://github.com/user-attachments/assets/e281f44a-edbd-40aa-9bb8-71d367470f55)

В раскрышмеся списке нажать на ... и выбрать *Удалить*, аналогично сделать для всех подложек.

![img76](https://github.com/user-attachments/assets/e53df4d2-a7ec-47f8-ae1a-d578829f4b23)

Затем необходимо переместить нашу подложку в базовую, для этого нажать на ... и выбрать *Переместить в базовую карту*.

![img77](https://github.com/user-attachments/assets/580f0f90-5675-4579-b2e7-25cadddf5a03)

После перемещения удалить оставшуюся подложку аналогичным способом, чтобы осталась только наша необходимая подложка. Данные операции производятся для того, чтобы данная веб-карта корректно работала в офлайн режиме без доста в Интернет.

![img78](https://github.com/user-attachments/assets/703c5147-8f50-40fd-8e8f-71af436505fe)

После всех операций обязательно нужно сохранить веб-карту. Для этого в верхней части нажать на кнопку *Сохранить* -> *Сохранить как*.

![img46](https://github.com/user-attachments/assets/fe9df032-1f46-4bb6-99e5-59092964a961)

Последовательность действий при сохранении веб-карты:

1. в появившемся окне **Сохранить карту** в поле *Заголовок* задать название веб-карты (обязательно к заполнению);
2. в поле *Теги* указать теги для более удобного поиска слоёв (обязательно к заполнению);
3. в поле *Краткая информация* указать ключевые слова (обязательно к заполнению);
4. в поле *Сохранить в папке* указать папку для сохранения (обязательно к заполнению).

![img47](https://github.com/user-attachments/assets/6ba3b749-568d-4234-b87a-8d10202b55f2)

Проверить наличие карты можно в папке, куда вы сохранили данную карту.

![img48](https://github.com/user-attachments/assets/ce1a9ea8-a8f3-4c33-801a-520dcc333f3e)

## 10. Настройка автономного режима в ArcGIS Enterprise

Настройка автономного режима осуществляется для веб-карты полевых обследований, которую мы ранее создали. Для этого нажать на веб-карту как показано на рисунке.

![img49](https://github.com/user-attachments/assets/d393ed19-1b99-4ecc-bf08-66c6e4fb99de)

В открывшемся окне в правом верхнем углу выбрать вкладку *Открыть в Field Maps*.

![img50](https://github.com/user-attachments/assets/7760cc35-c069-44a7-bdc7-60bc6bde0e7d)

В окне можно увидеть, что во вкладке **Статус офлайн** отключена возможность автономного режима.

![img51](https://github.com/user-attachments/assets/9c175bc0-eb7d-4d73-a44a-50e9bbeca95e)

Для активации офлайн режима перейти на вкладку **Автономный режим** и перевестим ползунок в активное состояние.

![img52](https://github.com/user-attachments/assets/1c6b5c2d-dc44-44e8-8589-8201f0e692cc)

Переходим в поле *Для автономной базовой карты используйте пакет листов* и нажимаем на него левой кнопкой мыши. В выпадающем списке рекомендуем выбрать *Устройство будет использовать пакет листов, который на него уже загружен.*, а в поле *Имя файла пакета* необходимо указать название пакета листов, который вы загрузите на ваше устрпойство и нажать *Сохранить* как указано на рисунке ниже.

![img79](https://github.com/user-attachments/assets/57cc3420-04f4-4382-88f2-2afb7b4fc6d6)

Проверить статус офлайн режима можно на вкладке **Обзор**.

![img80](https://github.com/user-attachments/assets/5c3ca743-54bb-4d47-bf3f-f6e72265efcf)

Для корректной работы чень важным моментом является распределение достапа на слои, так как если слои доступны только владельцу, тому кто их создал, то все остальные участники не смогут видеть данные слои, карты и приложения и не смогут работать в Field Maps, поэтому на первых этапах ознакомления с инфраструкторой ArcGIS рекомендуется устанавливать уровень общего доступа *Для всех (общий)* на все используемые и зависимые элементы как представлено ниже. Это можно проверить нажав в парвом верхнем углу любого слоя

![img81](https://github.com/user-attachments/assets/94c7fcff-f61f-4e76-b9e4-7c70054f0835)

![img82](https://github.com/user-attachments/assets/d774afdf-0e98-428e-b064-dc7aa8618889)

Обязательно проверить доступ на всех слоях, так как по умолчанию при публикации слоёв на портал или создания веб-карт общий доступ будет устанавливаться только для владельца, того кто опубликовал либо создал данный элемент.





















