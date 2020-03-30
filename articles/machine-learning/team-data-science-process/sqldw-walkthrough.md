---
title: Создание и развертывание модели с помощью аналитики Azure Synapse - Процесс анализа данных команды
description: Создайте и разнят модель машинного обучения с помощью Azure Synapse Analytics с общедоступным набором данных.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 96d0a5b2fb59e4612107d8ccbf7285fff7576585
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128391"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>Процесс анализа данных в действии: использование аналитики Azure Synapse
В этом уроке мы проведем вас через создание и развертывание модели машинного обучения с использованием Azure Synapse Analytics для общедоступного набора данных - набора данных [NYC Taxi Trips.](https://www.andresmh.com/nyctaxitrips/) Построенная модель двоичной классификации предсказывает, оплачивается ли чаевые за поездку.  Модели включают в себя многоклассовую классификацию (независимо от того, есть ли чаевые) и регрессию (распределение за уплаченные суммы чаевых).

Ниже представлена процедура рабочего процесса [обработки и анализа данных группы (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) . Мы покажем, как настроить среду науки о данных, как загрузить данные в аналитику Azure Synapse, и как использовать либо Azure Synapse Analytics, либо ноутбук IPython для изучения данных и инженерных функций для моделирования. Затем мы рассмотрим, как создать и развернуть модель в службе машинного обучения Azure.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>Набор данных "Поездки такси Нью-Йорка"
Данные о поездках такси Нью-Йорка содержатся в сжатых CSV-файлах размером около 20 ГБ (примерно 48 ГБ в распакованном виде), которые включают в себя сведения о более 173 млн отдельных поездок и платежах за каждую поездку. В каждой записи о поездке есть следующие данные: расположение пунктов посадки и высадки, время посадки и высадки, анонимизированный номер лицензии водителя, номер медальона (уникальный идентификатор такси). Данные включают в себя все поездки за 2013 год и предоставляются в виде следующих двух наборов данных за каждый месяц:

1. CSV-файл **trip_data.csv** содержит подробные сведения о поездке, например число пассажиров, пункты отправления и назначения, продолжительность поездки и ее расстояние. Вот несколько примеров записей:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. CSV-файл **trip_fare** содержит подробные сведения об оплате каждой поездки, такие как тип оплаты, сумма тарифа, надбавка и налоги, чаевые и пошлины, а также общая выплаченная сумма. Вот несколько примеров записей:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

**Уникальный ключ** для соединения trip\_data и trip\_fare состоит из следующих трех полей:

* medallion,
* hack\_license и
* pickup\_datetime.

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Определение трех типов задач прогнозирования
На основе *tip\_amount* мы сформулировали три проблемы прогнозирования, чтобы проиллюстрировать три типа задач моделирования.

1. **Двоичная классификация**: Чтобы предсказать, действительно ли чаевые были оплачены за поездку, то есть, *\_чаевые сумма,* которая больше, чем $ 0 является положительным примером, в то время как *чаевые\_сумма* $ 0 является негативным примером.
2. **Мультиклассовая классификация**: спрогнозировать диапазон суммы чаевых за поездку. Мы разделяем *tip\_amount* на пять ячеек или классов:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Задача регрессии:** Предсказать количество чаевых, оплаченных за поездку.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Настройка среды обработки и анализа данных Azure для расширенной аналитики
Чтобы настроить среду обработки и анализа данных в Azure, выполните следующие шаги.

**Создайте собственную учетную запись хранилища BLOB-объектов Azure**

* Во время подготовки своего хранилища BLOB-объектов Azure выберите географическое расположение как можно ближе к **центрально-южной части США**, где будут храниться данные о такси Нью-Йорка. С помощью AzCopy данные будут скопированы из контейнера общедоступного хранилища BLOB-объектов в контейнер в вашей учетной записи хранения. Чем ближе хранилище BLOB-объектов Azure к центрально-южная часть США, тем быстрее будет выполнена эта задача (шаг 4).
* Чтобы создать собственную учетную запись хранения Azure, выполните действия, изложенные в [учетных записях о хранилищах Azure.](../../storage/common/storage-create-storage-account.md) Убедитесь, что вы записали значения данных учетной записи хранения, так как они потребуются позже в этом пошаговом руководстве.

  * **Имя учетной записи хранилища**
  * **Ключ к счету хранения**
  * **Имя контейнера** (контейнер в хранилище BLOB-объектов Azure, где будут храниться данные)

**Предоставить экземпляр Azure Synapse Analytics.**
Следите за документацией в [создании и запросе хранилища данных Azure S'L на портале Azure,](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) чтобы предоставить экземпляр Azure Synapse Analytics. Убедитесь, что вы делаете обозначения следующих учетных данных Azure Synapse Analytics, которые будут использоваться в последующих шагах.

* **Имя сервера**: \<Имя сервера>.database.windows.net
* **имя (базы данных) хранилища данных SQL;**
* **Пользователя**
* **Пароль**

**Установите visual Studio и инструменты для обработки данных серверов.** Для получения инструкций [см.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md)

**Подключитесь к аналитике Azure Synapse с помощью Visual Studio.** Для получения инструкций просмотрите шаги 1 & 2 в [Подключении к складу данных Azure S'L.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-connect-overview.md)

> [!NOTE]
> Выполнить следующий запрос на базе данных, созданной в Azure Synapse Analytics (вместо запроса, предусмотренного в шаге 3 темы подключения), для **создания основного ключа.**
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Создайте рабочую область Машинного обучения Azure, используя подписку Azure.** Инструкции можно найти в статье [Создание рабочей области машинного обучения Azure](../studio/create-workspace.md).

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Загрузите данные в аналитику Azure Synapse
Откройте командную консоль Windows PowerShell. Выполнить следующие команды PowerShell, чтобы загрузить пример файлы скриптов S'L, которыми мы делимся с вами на GitHub, в локальный каталог, указанный с параметром *-DestDir.* Значение параметра *-DestDir* можно изменить и указать любой другой локальный каталог. Если *-DestDir* отсутствует, он будет создан в рамках сценария PowerShell.

> [!NOTE]
> При выполнении следующего сценария PowerShell может потребоваться **запуск от имени администратора** , если для создания каталога *DestDir* или записи в него нужны права администратора.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

После успешного выполнения текущий рабочий каталог изменится на *-DestDir*. Должен отобразиться экран, аналогичный показанному ниже:

![Изменения в текущем рабочем каталоге][19]

В каталоге *-DestDir*выполните следующий сценарий PowerShell в режиме администратора:

    ./SQLDW_Data_Import.ps1

Когда скрипт PowerShell будет работать в первый раз, вам будет предложено ввести информацию из вашей аналитики Azure Synapse и учетной записи хранения azure blob. После первого завершения этого сценария PowerShell введенные учетные данные будут записаны в файл конфигурации SQLDW.conf в используемом рабочем каталоге. При последующем запуске этого файла сценария PowerShell можно будет считать все необходимые параметры из файла конфигурации. Если некоторые параметры нужно изменить, их можно ввести на экране, когда отобразится запрос. Для этого нужно удалить файл конфигурации и ввести значения параметров по запросу. Кроме того, значения параметров можно изменить в файле SQLDW.conf в каталоге *-DestDir*.

> [!NOTE]
> Во избежание конфликтов с именами схем с теми, которые уже существуют в вашей Synapse Analytics Azure Azure, при чтении параметров непосредственно из файла sldW.conf к названию схемы добавляется 3-значное случайное число в название схемы из файла S'LDW.conf в качестве схемы по умолчанию имя для каждого запуска. Сценарий PowerShell может запросить имя схемы. Имя указывается по усмотрению пользователя.
>
>

Запуск этого файла **сценария PowerShell** предусматривает выполнение следующих задач:

* **Скачивание и установка AzCopy**, если она еще не установлена.

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
               Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }
* **Копирование данных в учетную запись частного хранилища BLOB-объектов** из общедоступного BLOB-объекта с помощью AzCopy.

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Загружает данные с помощью Polybase (выполняя LoadDataToSLDW.sql)** в свою аналитику Azure Synapse с вашей частной учетной записи хранения капли со следующими командами.

  * Создание схемы

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Создание учетных данных для определенной базы данных

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Создание внешнего источника данных для капли хранения Azure

          CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;

          CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
  * Создание формата внешнего файла для CSV-файла. Данные не сжимаются, а поля разделяются символом вертикальной черты.

          CREATE EXTERNAL FILE FORMAT {csv_file_format}
          WITH
          (
              FORMAT_TYPE = DELIMITEDTEXT,
              FORMAT_OPTIONS
              (
                  FIELD_TERMINATOR ='','',
                  USE_TYPE_DEFAULT = TRUE
              )
          )
          ;
  * Создание внешних таблиц (trip и fare) для хранения набора данных такси Нью-Йорка в хранилище BLOB-объектов Azure.

          CREATE EXTERNAL TABLE {external_nyctaxi_fare}
          (
              medallion varchar(50) not null,
              hack_license varchar(50) not null,
              vendor_id char(3),
              pickup_datetime datetime not null,
              payment_type char(3),
              fare_amount float,
              surcharge float,
              mta_tax float,
              tip_amount float,
              tolls_amount float,
              total_amount float
          )
          with (
              LOCATION    = ''/nyctaxifare/'',
              DATA_SOURCE = {nyctaxi_fare_storage},
              FILE_FORMAT = {csv_file_format},
              REJECT_TYPE = VALUE,
              REJECT_VALUE = 12
          )

            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                   medallion varchar(50) not null,
                   hack_license varchar(50)  not null,
                   vendor_id char(3),
                   rate_code char(3),
                   store_and_fwd_flag char(3),
                   pickup_datetime datetime  not null,
                   dropoff_datetime datetime,
                   passenger_count int,
                   trip_time_in_secs bigint,
                   trip_distance float,
                   pickup_longitude varchar(30),
                   pickup_latitude varchar(30),
                   dropoff_longitude varchar(30),
                   dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12
            )

    - Загрузка данных из внешних таблиц в хранилище Azure blob в Azure Synapse Analytics

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Создание примера таблицы данных (NYCTaxi_Sample) и вставка данных из него. Предполагает выбор SQL-запросов на таблицы trip и fare. (Некоторые шаги этого пошагового действия должны использовать эту таблицу выборки.)

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

Географическое расположение учетных записей хранения влияет на время загрузки.

> [!NOTE]
> В зависимости от географического расположения вашей учетной записи хранения капли, процесс копирования данных с публичной капли на ваш личный счет хранения может занять около 15 минут, или даже больше, и процесс загрузки данных с вашего счета хранения в ваш Azure Аналитика Azure Synapse может занять 20 минут или дольше.
>
>

Необходимо решить, какие действия предпринимать, если исходные и конечные файлы совпадают.

> [!NOTE]
> Если CSV-файлы, копируемые из общедоступного хранилища BLOB-объектов в учетную запись частного хранилища BLOB-объектов, уже существуют в учетной записи частного хранилища BLOB-объектов, AzCopy выведет запрос на их перезапись. Если перезаписывать их не нужно, по запросу системы введите **n** . Чтобы перезаписать **все** файлы, по запросу системы введите **a**. Чтобы перезаписать отдельные CSV-файлы, по запросу системы введите **y** .
>
>

![Выходные данные из AzCopy][21]

Можно использовать собственные данные. Если данные находятся на локальном компьютере в работающем приложении, AzCopy можно использовать для передачи локальных данных в частное хранилище BLOB-объектов Azure. Для этого в команде AzCopy в файле скрипта PowerShell необходимо лишь изменить расположение **источника**`$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, указав для него локальный каталог, содержащий данные.

> [!TIP]
> Если данные уже есть в личном хранилище Azure blob в приложении реальной жизни, вы можете пропустить шаг AzCopy в скрипте PowerShell и непосредственно загрузить данные в аналитику Azure Azure Synapse. В сценарий потребуется внести дополнительные изменения, чтобы настроить его согласно формату данных.
>
>

Этот скрипт PowerShell также подключает информацию Azure Synapse Analytics в файлы примера исследования данных SQLDW_Explorations.sql, SQLDW_Explorations.ipynb и SQLDW_Explorations_Scripts.py, чтобы эти три файла были готовы к опробованию мгновенно после завершения сценария PowerShell.

После успешного выполнения вы увидите экран, аналогичный этому:

![Результат успешного выполнения скрипта][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Исследование данных и проектирование объектов в Azure Synapse Analytics
В этом разделе мы проводим исследование данных и генерацию функций, запустив запросы s'L против Azure Synapse Analytics непосредственно с помощью **Visual Studio Data Tools.** Все SQL-запросы, используемые в этом разделе, можно найти в примере скрипта *SQLDW_Explorations.sql*. Этот файл скачан в локальный каталог во время выполнения сценария PowerShell. Вы также можете получить его из [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Но файл в GitHub не имеет информации Azure Synapse Analytics подключен.

Подключитесь к вашей Azure Synapse Analytics с помощью Visual Studio с логином именем и паролем Azure Synapse Analytics и откройте **explorer объектов для** подтверждения импортируемой базы данных и таблиц. Получите файл *SQLDW_Explorations.sql*.

> [!NOTE]
> Чтобы открыть редактор запросов хранилища параллельных данных (PDW), используйте команду **New Query**, выбрав PDW в **обозревателе объектов SQL**. PDW не поддерживает стандартный редактор SQL-запросов.
>
>

Ниже приведены типы задач по исследованию данных и генерации функций, выполняемых в этом разделе:

* Просмотрим распределение данных по нескольким полям в различных временных окнах.
* Исследуем качество данных по полям долготы и широты.
* Создадим метки двоичной и мультиклассовой классификации на основе **tip\_amount**.
* Создадим характеристики и вычислим/сравним расстояния поездок.
* Соединим две таблицы и извлечем случайную выборку, которая послужит основой для построения моделей.

### <a name="data-import-verification"></a>Проверка импорта данных
Эти запросы обеспечивают быструю проверку числа строк и столбцов в таблицах, заполненных ранее с помощью параллельного массового импорта Polybase.

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Выходные данные.** Вы должны получить 173 179 759 строк и 14 столбцов.

### <a name="exploration-trip-distribution-by-medallion"></a>Просмотр: распределение поездок по параметру medallion
В этом примере запроса определяются медальоны (номера) такси, которые осуществили больше 100 поездок за заданный период времени. Запрос будет использовать секционированный доступ к таблице, так как он обусловлен схемой секционирования **pickup\_datetime**. При запросе к полному набору данных также будет задействовано сканирование секционированной таблицы и/или индекса.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Выход:** Запрос должен вернуть таблицу с строками с указанием 13 369 медальонов (такси) и количества поездок, завершенных в 2013 году. В последнем столбце отображается счетчик поездок.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Просмотр: распределение поездок по параметрам medallion и hack_license
В этом примере определяются медальоны (номера) такси и номера hack_license (лицензий) водителей, которые осуществили больше 100 поездок за заданный период времени.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Выходные данные.** По результатам запроса возвращается таблица, в 13 369 строках которой перечислены 13 369 автомобилей (водителей), осуществивших в 2013 году более 100 поездок. В последнем столбце отображается счетчик поездок.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Оценка качества данных: проверка записей с неправильными значениями долготы и/или широты
В этом примере анализируется, содержится ли в каких-либо полях долготы и/или широты неверное значение (количество градусов должно быть в пределах от -90 до 90) или значение координат (0, 0).

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Выходные данные.** По результатам запроса возвращаются 837 467 поездок с недопустимыми значениями долготы и (или) широты.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Изучение: распределение поездок с чаевыми и без чаевых
В этом примере пользователь узнает о соотношении количества поездок, когда водителю дали на чай, к количеству поездок, когда чаевых не было (пользователь ищет сведения за определенный период времени или, если нужны сведения за полный год, во всех данных набора). Это распределение отражает распределение двоичных меток, которые в дальнейшем будут использоваться для моделирования двоичной классификации.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Выходные данные.** По результатам запроса возвращаются следующие результаты по частотности чаевых за 2013 год: 90 447 622 поездок с чаевыми и 82 264 709 — без чаевых.

### <a name="exploration-tip-classrange-distribution"></a>Изучение: распределение классов и диапазонов чаевых
В этом примере вычисляется распределение диапазонов чаевых за заданный период времени (или по полному набору данных в случае охвата целого года). Это распределение классов меток будет использоваться позже для моделирования многоклассовой классификации.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Выход:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Изучение: вычисление и сравнение расстояния поездок
В этом примере значения долготы и широты начальных и конечных пунктов поездок преобразуются в географические точки SQL, вычисляется расстояние поездки по разности географических точек и возвращается случайная выборка результатов для сравнения. В примере результаты ограничиваются только допустимыми координатами с помощью запроса оценки качества данных, описанного ранее.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Проектирование признаков с помощью функций SQL
Иногда функции SQL могут быть полезны при проектировании признаков. В этом пошаговом руководстве мы определили функцию SQL для расчета прямого расстояния между расположениями посадки и высадки. Описанные ниже сценарии SQL можно выполнять в **средствах Visual Studio для работы с данными**.

Вот сценарий SQL для определения функции расстояния:

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

Вот пример вызова этой функции в SQL-запросе для создания признаков:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Выходные данные.** По результатам запроса формируется таблица (из 2 803 538 строк) с координатами посадки и высадки пассажиров и дальностью поездок в милях. Вот результаты для первых трех строк:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40,731804 |–74,001083 |40,736622 |–73,988953 |0,7169601222 |
| 2 |40,715794 |–74,010635 |40,725338 |–74,00399 |0,7448343721 |
| 3 |40,761456 |–73,999886 |40,766544 |–73,988228 |0,7037227967 |

### <a name="prepare-data-for-model-building"></a>Подготовка данных для построения модели
Следующий запрос соединяет таблицы **nyctaxi\_trip** и **nyctaxi\_fare**, создает метку двоичной классификации **tipped**, метку многоклассовой классификации **tip\_class**, а также извлекает выборку из полного соединенного набора данных. Выборка предполагает получение подмножества поездок на основе данных времени посадок.  Этот запрос можно скопировать, а затем вставить непосредственно в [Azure Machine Learning Studio (классический)](https://studio.azureml.net) модуль импорта [данных импорта данных][import-data] для прямого приема данных из экземпляра базы данных ВСL в Azure. Запрос исключает записи с неверными (0, 0) координатами.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Когда вы будете готовы перейти к машинному обучению Azure, вы можете:

1. Сохраните окончательный запрос S'L, чтобы извлечь и отведать данные и копировать-вставить запрос непосредственно в модуль[импорта данных в] Azure Machine Learning, или
2. Сохраните отобранные и спроверленные данные, которые вы планируете использовать для создания модели в новой таблице аналитики Azure Synapse, и используйте новую таблицу в модуле[импорта данных] [импорта в]Azure Machine Learning. Сценарий PowerShell на более раннем этапе сделал эту задачу за вас. Данные можно считать прямо из этой таблицы в модуле "Импорт данных".

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Просмотр данных и проектирование характеристик в IPython Notebook
В этом разделе мы будем выполнять исследование данных и генерацию функций с использованием запросов Python и S'L против azure Synapse Analytics, созданной ранее. Пример файла IPython Notebook с именем **SQLDW_Explorations.ipynb** и файла скрипта Python **SQLDW_Explorations_Scripts.py** скачаны в локальный каталог. Они также доступны на веб-сайте [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). В сценариях Python эти два файла идентичны. Файл сценария Python предоставляется, если нет сервера IPython Notebook. Эти два примера файлов Python предназначены для использования с **Python 2.7**.

Необходимая информация Azure Synapse Analytics в образце IPython Notebook и файле скрипта Python, загруженном на локальную машину, была подключена сценарием PowerShell ранее. Они выполняются без изменений.

Если вы уже создали рабочее пространство Для машин Azure Machine Learning, вы можете непосредственно загрузить образец ноутбука IPython в службу ноутбука AzureML IPython и начать его запуск. Вот шаги для загрузки в службу ноутбука AzureML IPython:

1. Зайдите в рабочее пространство Azure Machine Learning, нажмите **"Студия"** в верхней части и нажмите **НА ПРИМЕЧАНИЕ** на левой стороне веб-страницы.

    ![Щелкните Studio, а затем — NOTEBOOKS][22]
2. Нажмите **NEW** на левом нижнем углу веб-страницы и выберите **Python 2**. Затем введите имя для Notebook и щелкните значок галочки для создания пустого IPython Notebook.

    ![Щелкните "СОЗДАТЬ", а затем выберите Python 2][23]
3. Нажмите символ **Jupyter** в левом верхнем углу нового ноутбука IPython.

    ![Щелкните символ Jupyter][24]
4. Перетащите пример IPython Notebook на страницу **дерева** службы IPython Notebook машинного обучения Azure и нажмите кнопку **Отправить**. После этого пример IPython Notebook будет отправлен в службу IPython Notebook Машинного обучения Azure.

    ![Щелкните "Отправить".][25]

Чтобы запустить пример IPython Notebook или файл сценария Python, необходимо установить следующие пакеты Python. Если вы используете службу IPython Notebook Машинного обучения Azure, эти пакеты уже установлены.

- pandas
- numpy
- matplotlib
- pyodbc
- PyTables

При создании передовых аналитических решений по машинном обучению Azure с большими данными, вот рекомендуемая последовательность:

* Считайте небольшую выборку данных во фрейм данных, размещенный в памяти.
* Выполните несколько визуализаций и просмотров с использованием выборки данных.
* Поэкспериментируйте с проектированием характеристик с использованием выборки данных.
* Для более масштабного исследования данных, манипулирования данными и проектирования функций используйте Python для выпуска запросов напрямую против Azure Synapse Analytics.
* Определите размер выборки, подходящий для создания модели машинного обучения Azure.

Ниже приведены несколько примеров изучения данных, визуализации данных и проектирования признаков. Дополнительные исследования данных можно найти в примере IPython Notebook и файле сценария Python.

### <a name="initialize-database-credentials"></a>Инициализация учетных данных базы данных
Инициализируйте параметры подключения к базе данных в следующих переменных:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Создание подключения к базе данных
Вот строка, которая создает подключение к базе данных.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Сообщение числа строк и столбцов в таблице <nyctaxi_trip>
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Общее число строк = 173179759
* Общее число столбцов = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Сообщение числа строк и столбцов в таблице <nyctaxi_fare>
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Общее число строк = 173179759
* Общее количество столбцов — 11.

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Чтение небольшого образца данных из базы данных Аналитики Azure Synapse
    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Время чтения таблицы выборки — 14,096 495 секунды.
Количество полученных строк и столбцов — 1000 и 21.

### <a name="descriptive-statistics"></a>Описательная статистика
Теперь все готово для изучения данных выборки. Начнем с рассмотрения описательной статистики для **trip\_distance** (или любых других выбранных полей).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Визуализация: пример блочной диаграммы
Далее рассмотрим блочную диаграмму расстояний поездок для визуализации квантилей.

    df1.boxplot(column='trip_distance',return_type='dict')

![Вывод блочной диаграммы][1]

### <a name="visualization-distribution-plot-example"></a>Визуализация: пример графика распределения
Это график, на котором показано распределение и гистограмма для примеров расстояний выборки.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Вывод графика распределения][2]

### <a name="visualization-bar-and-line-plots"></a>Визуализация: гистограммы и линейные графики
В этом примере мы сегментируем расстояния поездок на пять ячеек и визуализируем результаты сегментирования.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Мы можем изобразить описанное выше распределение по ячейкам в виде гистограммы или линейного графика, выполнив

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![вывод гистограммы][3]

и

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![вывод линейного графика][4]

### <a name="visualization-scatterplot-examples"></a>Визуализация: примеры точечных диаграмм
Мы отобразим точечную диаграмму для параметров **trip\_time\_in\_secs** и **trip\_distance**, чтобы проверить возможную корреляцию.

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Вывод точечной диаграмме связи между временем и расстоянием][6]

Точно также мы можем проверить связь между **rate\_code** и **trip\_distance**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Вывод точечной диаграмме связи между кодом и расстоянием][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Изучение выборки данных с помощью SQL-запросов в IPython Notebook
В этом разделе мы исследуем распределение данных с использованием отобранных данных, которые сохраняются в новой таблице, созданной выше. Аналогичные исследования могут быть выполнены с помощью исходных таблиц.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Исследование: сообщение количества строк и столбцов в таблице выборки
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Исследование: распределение поездок с чаевыми и без чаевых
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Изучение: распределение классов чаевых
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Исследование: построение диаграммы классового распределения чаевых
    tip_class_dist['tip_freq'].plot(kind='bar')

![График № 26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Исследование: ежедневное распределение поездок
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Исследование: распределение поездок по параметру medallion
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Изучение: распределение поездок по медальону и номеру лицензии водителя
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Просмотр: распределение поездок по времени
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Изучение: распределение поездок по расстоянию
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Изучение: распределение поездок по типу оплаты
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Проверка конечной формы таблицы с признаками
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Построение моделей в компоненте машинного обучения Azure
Теперь мы готовы приступить к созданию и развертыванию моделей в [Azure Machine Learning.](https://studio.azureml.net) Данные готовы для любой из задач прогнозирования, определенных ранее:

1. **Двоичная классификация**: Чтобы предсказать, был ли оплатить сярвые чаевые за поездку.
2. **Многоклассная классификация**: Для прогнозирования диапазона оплачиваемых чаевых, в соответствии с ранее определенными классами.
3. **Задача регрессии:** Предсказать количество чаевых, оплаченных за поездку.

Чтобы начать упражнение по моделированию, войдите в рабочее пространство **Azure Machine Learning (классическое).** Если вы еще не создали рабочее пространство для машинного обучения, [см.](../studio/create-workspace.md)

1. Чтобы начать работу с Azure Machine Learning, [см.](../studio/what-is-ml-studio.md)
2. Войти в [Azure Machine Learning Studio (классический)](https://studio.azureml.net).
3. Студия машинного обучения (классическая) Главная страница предоставляет огромное количество информации, видео, учебники, ссылки на ссылки на модули и другие ресурсы. Для получения дополнительной информации о машинном обучении Azure можно ознакомиться на [Центре документации по машинным обучениям Azure.](https://azure.microsoft.com/documentation/services/machine-learning/)

Типичный обучающий эксперимент предусматривает следующие шаги:

1. Создайте эксперимент **«НОВЫЙ».**
2. Получить данные в студию машинного обучения Azure (классический).
3. Предварительно обработайте, трансформируйте и манипулируя данными по мере необходимости.
4. Создать необходимые характеристики.
5. Разбить данные на учебные/проверочные/тестовые наборы данных (или иметь отдельные наборы данных для каждой из этих целей).
6. Выбрать один или несколько алгоритмов машинного обучения в зависимости от решаемой задачи обучения. Например, двоичная классификация, многоклассовая классификация, регрессия.
7. Обучить одну или несколько моделей с помощью учебного набора данных.
8. Оценить проверочный набор данных с помощью обученных моделей.
9. Вычислить модель (модели) для расчета соответствующих показателей для задачи по обучению.
10. Настройте модель (ы) и выберите лучшую модель для развертывания.

В этом упражнении мы уже изучили и спроектировали данные в Azure Synapse Analytics и определились с размером выборки, чтобы глотать в Azure Machine Learning Studio (классический). Вот процедура, позволяющая создать одну или несколько моделей прогнозирования:

1. Получите данные в студию машинного обучения Azure (классический) с помощью модуля[импорта данных] [импорта,]доступного в разделе **Ввод и выход данных.** Дополнительные сведения см. на странице со справочной информацией о модуле [импорта данных][import-data].

    ![Модуль "Импорт данных" в Машинном обучении Azure][17]
2. На панели **Properties** (Свойства) в списке **Data source** (Источник данных) выберите **Azure SQL Database** (База данных SQL Azure).
3. Введите DNS-имя базы данных в поле **Имя сервера базы данных** . Формат: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Введите **Имя базы данных** в соответствующее поле.
5. Введите *имя пользователя SQL* в поле **Server user account name** (Имя учетной записи пользователя сервера) и *пароль* в поле **Server user account password** (Пароль учетной записи пользователя сервера).
7. В области редактирования текста **запроса запроса** вставьте запрос, включащий необходимые поля базы данных (включая любые вычислительные поля, такие как метки) и понижайте образцы данных до желаемого размера выборки.

Пример бинарной классификации эксперимента чтения данных непосредственно из базы данных Azure Synapse Analytics находится в рисунке ниже (не забудьте заменить названия таблицnyctaxi_trip и nyctaxi_fare имя схемы и таблицы имена, которые вы использовали в вашей пошаговая пошагонка). Подобные эксперименты можно сконструировать для задач мультиклассовой классификации и регрессии.

![Чат Машинного обучения Azure][10]

> [!IMPORTANT]
> В примерах запросов на извлечение и выборку данных для моделирования, указанных в предыдущих разделах, **все метки для трех упражнений по моделированию включены в запрос**. Важным (обязательным) шагом каждого из упражнений по моделированию является **исключение** ненужных меток для двух остальных задач, а также любых **целевых утечек**. Например, при двоичной классификации используйте метку **tipped** и исключите поля **tip\_class**, **tip\_amount** и **total\_amount**. Последние являются целевыми утечками, поскольку подразумевают, что чаевые выплачены.
>
> Чтобы исключить все ненужные столбцы или целевые утечки, можно воспользоваться модулем [Select Columns in Dataset][select-columns] (Выбор столбцов в наборе данных) или [Edit Metadata][edit-metadata] (Изменение метаданных). Дополнительные сведения см. на страницах справки модулей [Select Columns in Dataset][select-columns] (Выбор столбцов в наборе данных) и [Edit Metadata][edit-metadata] (Изменение метаданных).
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Развертывание моделей в службе Машинного обучения Azure
Когда модель готова, ее можно легко развернуть в виде веб-службы непосредственно из эксперимента. Дополнительные сведения о развертывании веб-служб Azure ML см. в статье [Развертывание веб-службы Машинного обучения Azure](../studio/deploy-a-machine-learning-web-service.md).

Чтобы развернуть новую веб-службу, необходимо выполнить такие действия.

1. Создать эксперимент по количественной оценке.
2. Развернуть веб-службу.

Чтобы создать оценивающий эксперимент на основе учебного эксперимента со статусом **Завершено**, щелкните **Create scoring experiment** (Создать оценивающий эксперимент) на нижней панели действий.

![Система оценок Azure][18]

Служба машинного обучения Azure попытается создать эксперимент по количественной оценке на основе компонентов учебного эксперимента. В частности, она:

1. Сохранит обученную модель и удалит модули обучения модели.
2. Идентифицирует логический **порт ввода** для представления ожидаемой схемы входных данных.
3. Идентифицирует логический **порт вывода** для представления ожидаемой схемы вывода веб-службы.

Когда скоринг эксперимент создан, просмотрите результаты и сделать настроить по мере необходимости. Типичная корректировка заключается в замене набора данных ввода или запроса на набор, исключающий поля меток, поскольку эти поля меток не будут отображены в схеме при вызове службы. Также хорошей практикой является уменьшение размера набора входных данных и/или запроса до нескольких записей, достаточного для указания схемы ввода. Для порта вывода зачастую исключаются все входные поля и в вывод включаются только **Scored Labels** (Оцененные метки) и **Scored Probabilities** (Оцененные вероятности) с помощью модуля [Select Columns in Dataset][select-columns] (Выбор столбцов в наборе данных).

Пример оценивающего эксперимента показан на рисунке ниже. Когда все будет готово к развертыванию, нажмите кнопку **ОПУБЛИКОВАТЬ ВЕБ-СЛУЖБУ** на нижней панели действий.

![Публикация Машинного обучения Azure][11]

## <a name="summary"></a>Сводка
Итак, в этом пошаговом руководстве мы создали среду обработки и анализа данных, работали с большим общедоступным набором данных на протяжении всего процесса обработки и анализа данных группы от получения данных до обучения модели и развертывания веб-службы машинного обучения Azure.

### <a name="license-information"></a>Сведения о лицензии
Этот образец пошагового руководства и сопровождающие его сценарии и файлы IPython Notebook предоставлены корпорацией Майкрософт на условиях лицензии MIT. Для получения более подробной информации проверьте файл LICENSE.txt в каталоге примера кода на GitHub.

## <a name="references"></a>Ссылки
- [Андрес Монрой NYC Такси Поездки Скачать Страница](https://www.andresmh.com/nyctaxitrips/)
- [FOILing NYC такси Поездка данных Крис Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [NYC такси и лимузин комиссии исследований и статистики](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
