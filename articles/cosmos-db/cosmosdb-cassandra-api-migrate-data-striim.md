---
title: Перенос данных в учетную запись API Azure Cosmos Cassandra С использованием Striim
description: Узнайте, как использовать Striim для переноса данных из базы данных Oracle в учетную запись Azure Cosmos DB Cassandra API.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 50028e81c4ca130aa3266c164a431dc935a271cb
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730038"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Перенос данных в учетную запись API Azure Cosmos Cassandra С использованием Striim

Изображение Striim на рынке Azure обеспечивает непрерывное перемещение данных в режиме реального времени от складов данных и баз данных к Azure. При перемещении данных можно выполнять в режиме строки денормализации, преобразования данных, включить аналитику в режиме реального времени, и сценарии отчетности данных. С Striim легко начать постоянно перемещать корпоративные данные на API Azure Cosmos DB Cassandra API. Azure предоставляет предложение на рынке, которое упрощает развертывание Striim и перенос данных в DB Azure Cosmos. 

В этой статье показано, как использовать Striim для переноса данных из **базы данных Oracle** в **учетную запись Azure Cosmos DB Cassandra API.**

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет [подписки Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начать работу.

* База данных Oracle работает на месте с некоторыми данными в нем.

## <a name="deploy-the-striim-marketplace-solution"></a>Развертывание решения для рынка Striim

1. Войдите на [портал Azure](https://portal.azure.com/).

1. Выберите **Создать ресурс** и поиск **Striim** на рынке Azure. Выберите первый вариант и **создайте**.

   ![Найти предмет рынка Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Затем введите свойства конфигурации экземпляра Striim. Среда Striim развернута в виртуальной машине. С панели **Basics** введите **имя пользователя VM,** **пароль VM** (этот пароль используется для SSH в VM). Выберите **информацию о подписке,** **ресурсной группе**и **местоположении,** где вы хотите развернуть Striim. После завершения, выберите **OK**.

   ![Настройка базовых настроек для Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)


1. В **панели настроек Striim Cluster** выберите тип развертывания Striim и размер виртуальной машины.

   |Параметр | Значение | Описание |
   | ---| ---| ---|
   |Тип развертывания Striim |Автономный | Striim может работать в типах **развертывания Standalone** или **Cluster.** Автономный режим развернет сервер Striim на одной виртуальной машине, и вы можете выбрать размер виртуальных машин в зависимости от объема данных. Режим кластера развернет сервер Striim на двух или более вс-мизантах с выбранным размером. Кластерные среды с более чем 2 узлами обеспечивают автоматическую высокую доступность и сбой.</br></br> В этом уроке вы можете выбрать вариант Standalone. Используйте по умолчанию "Standard_F4s" размер VM. | 
   | Название стилийского кластера|    <Striim_cluster_Name>|  Название стилийского кластера.|
   | Кластерный пароль Striim|   <Striim_cluster_password>|  Пароль для кластера.|

   После заполнения формы выберите **OK** для продолжения.

1. В **настройках доступа Striim панель,** настроить **общественный IP-адрес** (выбрать значения по умолчанию), **доменное имя для Striim**, **пароль админа,** который вы хотели бы использовать для входа в Striim UI. Нанастройка VNET и Subnet (выберите значения по умолчанию). После заполнения деталей, выберите **OK,** чтобы продолжить.

   ![Настройки доступа Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure проверит развертывание и убедится, что все выглядит хорошо; проверка занимает несколько минут. После завершения проверки выберите **OK**.
  
1. Наконец, просмотрите условия использования и выберите **Создать** для создания экземпляра Striim. 

## <a name="configure-the-source-database"></a>Настройка исходной базы данных

В этом разделе вы настраиваете базу данных Oracle в качестве источника для передачи данных.  Для подключения к Oracle вам понадобится [драйвер Oracle JDBC.](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) Чтобы прочитать изменения из исходной базы данных Oracle, вы можете использовать [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) или [AIS XStream.](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647) Драйвер Oracle JDBC должен присутствовать в Java-классе Striim для чтения, записи или сохранения данных из базы данных Oracle.

Скачать драйвер [ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) на локальной машине. Вы установите его в кластере Striim позже.

## <a name="configure-target-database"></a>Настройка целевой базы данных

В этом разделе вы наверстируете учетную запись Azure Cosmos DB Cassandra API в качестве цели для движения данных.

1. Создание [учетной записи Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account) с помощью портала Azure.

1. Перейдите к панели **Data Explorer** в учетной записи Azure Cosmos. Выберите **новую таблицу** для создания нового контейнера. Предположим, что вы мигрируете *продукты* и *заказываете* данные из базы данных Oracle в Azure Cosmos DB. Создайте новое Keyspace под названием **StriimDemo** с контейнером заказов. Предоставление контейнера с **1000 RUs**(этот пример использует 1000 RUs, но вы должны использовать пропускную емкость, оцененную для вашей рабочей нагрузки), и **/ORDER_ID** в качестве основного ключа. Эти значения будут отличаться в зависимости от исходных данных. 

   ![Создание учетной записи Cassandra API](./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Настройка Oracle в поток данных Azure Cosmos DB

1. Теперь вернемся к Стрийму. Прежде чем взаимодействовать со Striim, установите драйвер Oracle JDBC, который вы скачали ранее.

1. Перейдите к экземпляру Striim, развернутому на портале Azure. Выберите кнопку **«Подключение»** в верхней панели меню и на вкладке **SSH,** скопируйте URL в **Login с помощью локального поля учетной записи VM.**

   ![Получить URL-адрес SSH](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Откройте новое окно терминала и запустите команду SSH, скопированную с портала Azure. В этой статье используется терминал в MacOS, вы можете следовать аналогичным инструкциям с помощью PuTTY или другого клиента SSH на машине Windows. Когда попросят, введите **да,** чтобы продолжить и ввести **пароль,** который вы установили для виртуальной машины на предыдущем этапе.

   ![Подключение к Striim VM](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. Теперь откройте новую вкладку терминала, чтобы скопировать файл **ojdbc8.jar,** который вы загрузили ранее. Используйте следующую команду SCP, чтобы скопировать файл банки из локальной машины в папку tmp экземпляра Striim, работающего в Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Копирование файла Jar от машины местоположения к Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Затем перейдите обратно в окно, где вы сделали SSH в экземплярs Striim и Логин как судо. Переместите файл **ojdbc8.jar** из **каталога /tmp** в каталог **lib** вашего экземпляра Striim со следующими командами:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Переместите файл Jar в папку lib](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. Из того же окна терминала перезапустите сервер Striim, выполняя следующие команды:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Стрейим займет минуту, чтобы начать. Если вы хотите увидеть статус, запустите следующую команду: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Теперь перейдите в Azure и скопируйте общедоступный IP-адрес вашего Striim VM. 

   ![Копия IP-адреса Striim VM](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Чтобы перейти к веб-интерфейсу Striim, откройте новую вкладку в браузере и скопируйте общедоступный IP, за которым следует: 9080. Восвай, используя имя пользователя **admin,** вместе с паролем админ, указанным на портале Azure.

   ![Войти на Стмим](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. Теперь вы прибудете на домашнюю страницу Стриима. Есть три различных панелей - **панели мониторинга**, **приложения**, и **SourcePreview**. Панель dashboards позволяет перемещать данные в режиме реального времени и визуализировать их. Панель приложений содержит потоки потоков потоков потоков потоков потоков потоков потоков потоковой передачи данных. Справа от страницы находится SourcePreview, где вы можете просмотреть данные, прежде чем перемещать их.

1. Выберите панель **приложений,** мы сосредоточимся на этом панели на данный момент. Есть целый ряд примеров приложений, которые можно использовать, чтобы узнать о Striim, однако в этой статье вы будете создавать наши собственные. Выберите кнопку **Добавить app** в верхнем правом углу.

   ![Добавить приложение Striim](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Существует несколько различных способов создания приложений Striim. Выберите **«Старт с нуля»** для этого сценария.

   ![Запустите приложение с нуля](./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png)

1. Дайте дружественное имя для вашего приложения, что-то вроде **oraToCosmosDB** и выберите **Сохранить**.

   ![Создание приложения](./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png)

1. Вы прибудете к конструктору потока, где вы можете перетащить и выпасть из разъемов коробки для создания потоковых приложений. Введите **Oracle** в панели поиска, перетащите и бросьте источник **Oracle CDC** на холст приложения.  

   ![Источник Oracle CDC](./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png)

1. Введите свойства исходной конфигурации экземпляра Oracle. Название источника является лишь конвенцией именования для приложения Striim, вы можете использовать имя, такое как **src_onPremOracle**. Также введите другие детали, такие как тип Адаптера, URL-адрес соединения, имя пользователя, пароль, имя стола. Выберите **Сохранить** для продолжения.

   ![Настройка параметров источника](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png)

1. Теперь щелкните значок волны потока, чтобы соединить целевой экземпляр Azure Cosmos DB. 

   ![Подключение к целевому устройству](./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png)

1. Перед настройкой цели убедитесь, что вы добавили [корневой сертификат Балтимора в среду Java Striim.](/azure/developer/java/sdk/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store)

1. Введите свойства конфигурации в экземпляре Azure Cosmos DB и выберите **Сохранение** для продолжения. Вот ключевые параметры, которые следует отметить:

   * **Адаптер** - Использование **DatabaseWriter**. При записи в Azure Cosmos DB Cassandra API требуется DatabaseWriter. Водитель Cassandra 3.6.0 в комплекте со Striim. Если DatabaseWriter превысит количество RUs, предусмотренных в контейнере Azure Cosmos, приложение выйдет из строя.

   * **URL-адрес подключения** - Укажите URL-адрес подключения Azure Cosmos DB JDBC. URL-адрес находится в формате`jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * **Имя пользователя** - Укажите имя учетной записи Azure Cosmos.
   
   * **Пароль** - Укажите основной ключ вашей учетной записи Azure Cosmos.

   * **Таблицы** - Целевые таблицы должны иметь первичные ключи, а первичные клавиши не могут быть обновлены.

   ![Настройка целевых свойств](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png)

   ![Настройка целевых свойств](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png)

1. Теперь мы запустим приложение Striim. В верхней панели меню выберите **Созданный,** а затем **развернуть app**. В окне развертывания можно указать, хотите ли выполнить определенные части приложения на определенных частях топологии развертывания. Поскольку мы работаем в простой топологии развертывания через Azure, мы будем использовать опцию по умолчанию.

   ![Развертывание приложения](./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png)


1. Теперь мы просмотрим поток, чтобы увидеть данные, протекающие через Striim. Нажмите значок волны и нажмите значок глаза рядом с ним. После развертывания можно просмотреть поток, чтобы увидеть данные, протекающие через. Выберите значок **волны** и **глазное яблоко** рядом с ним. Выберите кнопку **«Развертывание»** в верхней панели меню и выберите **start App.**

   ![Запуск приложения](./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png)

1. С помощью считывателя **CDC (Change Data Capture)** Striim будет подбирать только новые изменения в базе данных. Если данные протекают через исходные таблицы, вы их увидите. Однако, поскольку это таблица выборки, источник, который не подключен к любому приложению. Если вы используете генератор выборочных данных, вы можете вставить цепочку событий в базу данных Oracle.

1. Вы увидите данные, протекающие через платформу Striim. Striim также подбирает все метаданные, связанные с таблицей, что полезно для мониторинга данных и защиты прав данных.

   ![Настройка конвейера CDC](./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png)

1. Наконец, давайте ввешемся в Azure и перейдем к учетной записи Azure Cosmos. Обновите исследователь данных, и вы можете видеть, что данные поступили. 

Используя решение Striim в Azure, вы можете непрерывно переносить данные в Azure Cosmos DB из различных источников, таких как Oracle, Cassandra, MongoDB, и различные другие в Azure Cosmos DB. Чтобы узнать больше, пожалуйста, посетите [веб-сайт Striim](https://www.striim.com/), [скачать бесплатную 30-дневную пробную версию Striim](https://go2.striim.com/download-free-trial), и для любых вопросов при настройке пути миграции со Striim, файл [запрос на поддержку.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Следующие шаги

* Если вы переносите данные на API Azure Cosmos DB S'L, узнайте, как перенести данные в [учетную запись Cassandra API с помощью Striim](cosmosdb-sql-api-migrate-data-striim.md)

* [Мониторинг и отладка данных с помощью метрик Azure Cosmos DB](use-metrics.md)
