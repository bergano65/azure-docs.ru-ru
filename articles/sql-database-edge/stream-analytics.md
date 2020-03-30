---
title: Используя пакеты DAC базы данных s'L и задания Stream Analytics с Azure S'L Database Edge Документы Майкрософт
description: Узнайте об использовании заданий Stream Analytics в Edge базы данных S'L
keywords: sql края базы данных, потоковой аналитики, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74384161"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>Используя пакеты DAC базы данных S'L и задания Stream Analytics с Edge базы данных S'L

Azure S'L Database Edge Preview — это оптимизированный реляционный движок базы данных, ориентированный на развертывание IoT и edge. Он построен на последних версиях microsoft S'L Server Database Engine, который обеспечивает лидирующие в отрасли возможности обработки, безопасности и обработки запросов. Наряду с ведущими в отрасли возможностями управления реляционными базами данных сервера, Azure S'L Database Edge предоставляет встроенные возможности потоковой передачи для аналитики в реальном времени и сложной обработки событий.

Azure S'L Database Edge также обеспечивает наирующую реализацию SqlPackage.exe, которая позволяет развертывать пакет [DAC базы данных S'L](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) во время развертывания Edge базы данных S'L.

Azure S'L Database Edge предоставляет два `module twin's desired properties` дополнительных параметра через опцию модуля IoT Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Поле | Описание |
|------|-------------|
| SqlPackage | Хранилище хранилища Azure Blob URI для файла q.zip, содержащего пакет DAC базы данных S'L.
| ASAJobInfo | Хранилище хранилища Azure Blob URI для работы ASA Edge. Для получения дополнительной информации [см.](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Использование пакетов DAC базы данных S'L с edge базы данных S'L

Для использования пакета DAC базы данных S'L ('dacpac) с y'L Database Edge, выполнить следующие действия:

1. Создайте или извлеките пакет DAC базы данных S'L. См [Извлечение DAC из базы данных](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) для получения информации о том, как создать пакет DAC для существующей базы данных Сервера.

2. Запиши q.dacpac и загрузите его в учетную запись хранения Azure Blob. Для получения дополнительной информации о загрузке файлов в хранилище Azure Blob [см.](../storage/blobs/storage-quickstart-blobs-portal.md)

3. Создайте общую подпись доступа для файла zip с помощью портала Azure. Для получения дополнительной информации [см. Доступ делегата с общими подписями доступа (SAS).](../storage/common/storage-sas-overview.md)

4. Обновление конфигурации модуля «S'L Database Edge», чтобы включить общий доступ URI для пакета DAC. Для обновления модуля «Край базы данных», следующие действия:

    1. На портале Azure перейдите к развертыванию Концентратора IoT.

    2. В левой области щелкните **IoT Edge**.

    3. На странице **IoT Edge** найдите и выберите край IoT, где развернут модуль S'L Database Edge.

    4. На странице устройства **IoT Edge** выберите **Модуль Set.**

    5. На странице **модулей Set** **выберите на передний** модуль S'L Database Edge.

    6. В панели **пользовательских модулей IoT Edge** выберите **желаемые свойства двухместных модулей Set.** Обновление желаемых свойств, чтобы `SQLPackage` включить URI для опции, как показано в следующем примере.

        > [!NOTE]
        > SAS URI в следующем JSON является лишь примером. Замените URI фактическим URI из развертывания.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Нажмите кнопку **Сохранить**.

    8. На странице **модулей Set** выберите **Следующий**.

    9. На странице **модулей Set** выберите **«Следующий»,** а затем **отправьте.**

5. После обновления модуля файл пакета DAC загружается, рассеивается и развертывается в экземпляре S'L Database Edge.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Использование задания потоковой передачи с помощью Edge базы данных S'L

Azure S'L Database Edge имеет наируюую реализацию времени выполнения потоковой аналитики. Эта реализация позволяет создать краевую работу Azure Stream Analytics и развернуть это задание в качестве задания потоковой передачи базы данных S'L Edge. Чтобы создать задание края Stream Analytics, выполните следующие действия:

1. Перейдите на портал Azure, используя [URL-адрес](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)предварительного просмотра. Этот URL-адрес предварительного просмотра позволяет настроить выход базы данных s'L для задания края Stream Analytics.

2. Создайте новую **аналитику потоков Azure на** задания IoT Edge. Выберите среду хостинга, которая нацелена на **Edge.**

3. Определите ввод и выход для задания Azure Stream Analytics. Каждый выход сотов, который вы наведете здесь, привязан к одной таблице в базе данных. Если вам необходимо передавать данные в несколько таблиц, необходимо создать несколько выходных данных базы данных. Можно настроить выходы S'L, чтобы указать на различные базы данных.

    **Входный вход**. Выберите EdgeHub в качестве ввода для задания края и предоставьте информацию о ресурсе.

    **Выход**. Выберите базу данных S'L в качестве вывода. Выберите **настройки базы данных S'L вручную.** Предоставьте сведения о конфигурации для базы данных и таблицы.

    |Поле      | Описание |
    |---------------|-------------|
    |Псевдоним выходных данных | Название псевдонима выхода.|
    |База данных | Название базы данных S'L. Это должно быть действительное имя базы данных, которая существует в экземпляре Edge базы данных.|
    |Имя сервера | Имя (или IP-адрес) и сведения о номере порта для экземпляра S'L. Для развертывания edge базы данных s'L можно использовать **tcp:.,1433** для имени сервера.|
    |Имя пользователя | Учетная запись для записи, которая имеет доступ к базе данных, указанной ранее, с помощью учетной записи данных.|
    |Пароль | Пароль для учетной записи, указанной вами ранее.|
    |Таблица | Название таблицы, которая будет вывесана на выходе для задания потоковой передачи.|
    |Унаследовать раздел| Позволяет наследовать схему раздела предыдущего шага или ввода запроса. Когда эта опция включена, можно ожидать, что при записи на дисковую таблицу будет улучшена пропускная запись и есть полностью параллельная топология для вашей работы.|
    |Размер пакета| Максимальное количество записей, отправляемых с каждой транзакцией навалом вставки.|

    Вот пример ввода / выходной конфигурации:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Для получения более подробной информации о адаптере выходного интерфейса для аналитики Azure [Stream—](../stream-analytics/stream-analytics-sql-output-perf.md)см.

4. Определите запрос задания ASA для задания края. В этом запросе следует использовать определенные псевдонимы ввода/вывода в качестве имен ввода и вывода в запросе. Для получения дополнительной информации [см.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

5. Установите настройки учетной записи хранилища для задания края. Учетная запись хранилища используется в качестве цели публикации для задания края.

6. Под **настройкой**, выберите **Опубликовать,** а затем выберите кнопку **Публикация.** Сохраните SAS URI для использования с модулем S'L Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Развертывание задания края Stream Analytics в край базы данных S'L Edge

Чтобы развернуть задание потоковой передачи в модуль S'L Database Edge, обновите конфигурацию модуля S'L Database Edge, чтобы включить SAS URI для выполнения задания с более раннего шага. Для обновления модуля «Край базы данных»:

1. На портале Azure перейдите к развертыванию Концентратора IoT.

2. В левой области щелкните **IoT Edge**.

3. На странице **IoT Edge** найдите и выберите край IoT, где развернут модуль S'L Database Edge.

4. На странице устройства **IoT Edge** выберите **Модуль Set.**

5. На странице **модулей Set** **выберите на передний** модуль S'L Database Edge.

6. В панели **пользовательских модулей IoT Edge** выберите **желаемые свойства двухместных модулей Set.** Обновление желаемых свойств, чтобы `ASAJobInfo` включить URI для опции, как показано в следующем примере.

    > [!NOTE]
    > SAS URI в следующем JSON является лишь примером. Замените URI фактическим URI из развертывания.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Нажмите кнопку **Сохранить**.

8. На странице **модулей Set** выберите **Следующий**.

9. На странице **модулей Set** выберите **«Следующий»,** а затем **отправьте.**

10. После обновления модуля файл вакансий потоковой аналитики загружается, распаковывается и развертывается в экземпляре S'L Database Edge.

## <a name="next-steps"></a>Дальнейшие действия

- Подробная информация о ценах и доступности, [см.](https://azure.microsoft.com/services/sql-database-edge/)
- Запрос на включение в базу данных Azure S'L Edge для вашей подписки.
- Для начала — [см. Развертывание edge базы данных с помощью системы «Развертывание» через портал Azure.](deploy-portal.md)
