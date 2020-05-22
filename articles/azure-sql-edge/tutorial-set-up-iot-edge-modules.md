---
title: Настройка модулей IoT Edge в службе SQL Azure для пограничных вычислений
description: В части 2 (из 3) этого руководства по SQL Azure для пограничных вычислений, посвященного прогнозированию примеси железной руды, вы настроите модули и подключения IoT Edge.
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: bbbbe09aac30165a2f9b7bbe54f58e0c09a6cf09
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593503"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Настройка модулей и подключений IoT Edge

В части 2 (из 3) этого руководства по прогнозированию примеси железной руды в SQL Azure для пограничных вычислений вы настроите следующие модули IoT Edge:

- SQL Azure для пограничных вычислений
- Модуль генератора данных IoT Edge

## <a name="create-azure-stream-analytics-module"></a>Создание модуля Azure Stream Analytics

Создайте модуль Azure Stream Analytics, который будет использоваться в этом руководстве. Дополнительные сведения об использовании заданий потоковой передачи с SQL Server см. в статье [Использование потоковых заданий с Базой данных SQL Azure для пограничных вычислений](https://docs.microsoft.com/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

После создания задания Azure Stream Analytics с помощью среды размещения, установленной в качестве Edge, настройте для учебника входные и выходные данные.

1. Чтобы создать **входные данные**, щелкните **+ Добавить потоковый вход**. Заполните раздел сведений, используя следующие сведения:

   Поле|Значение
   -----|-----
   Формат сериализации событий|JSON
   Кодирование|UTF-8
   Тип сжатия событий|None

2. Чтобы создать **Выходные данные**, щелкните **+ Добавить** и выберите "База данных SQL". Заполните раздел сведений, используя приведенную ниже информацию.

   > [!NOTE]
   > При развертывании модуля SQL Azure для пограничных вычислений в разделе **"Развертывание модуля SQL Azure для пограничных вычислений"** пароль, указанный в этом разделе, необходимо указать в качестве пароль системного администратора SQL.

   Поле|Значение
   -----|-----
   База данных|IronOreSilicaPrediction
   Имя сервера|tcp:.,1433
   Имя пользователя|sa
   Пароль|Укажите надежный пароль
   Таблица|IronOreMeasurements1

3. Перейдите к разделу **Запрос** и настройте запрос следующим образом:

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. В разделе **Настройка** выберите **Опубликовать**, а затем нажмите кнопку **Опубликовать**. Сохраните URI SAS, чтобы использовать его с модулем Базы данных SQL для пограничных вычислений.

## <a name="specify-container-registry-credentials"></a>Указание учетных данных реестра контейнеров

Необходимо указать учетные данные контейнера для образов модуля, на которых размещены образы модулей. Их можно найти в реестре контейнеров, созданном в группе ресурсов. Перейдите к разделу **Ключи доступа**. Запишите следующие поля:

- Имя реестра
- сервер входа;
- Имя пользователя
- Пароль

Теперь укажите учетные данные контейнера в модуле IoT Edge.

1. Перейдите в центр Интернета вещей, созданный в группе ресурсов.

2. В подразделе **IoT Edge** в разделе **Автоматическое управление устройствами** щелкните **Идентификатор устройства**. В этом руководстве используется идентификатор `IronOrePredictionDevice`.

3. Выберите раздел **Выбор модулей**.

4. В разделе **Учетные данные реестра контейнеров** введите следующие значения:

   _Поле_|_Значение_
   -------|-------
   Имя|Имя реестра
   Адрес|сервер входа;
   Имя пользователя|Имя пользователя
   Пароль|Пароль
  
## <a name="deploy-the-data-generator-module"></a>Развертывание модуля генератора данных

1. В разделе **Модули IoT Edge** щелкните **+ Добавить** и выберите **Модуль IoT Edge**.

2. Укажите имя модуля IoT Edge и URI образа.
   Универсальный код ресурса (URI) образа можно найти в реестре контейнеров в группе ресурсов. В разделе **Службы** выберите **Репозитории**. Для работы с этим руководством выберите репозиторий с именем `silicaprediction`. Выберите соответствующий тег. URI изображения будет иметь формат:

   *сервер входа containerregistry*/*имя репозитория*:*имя тега*

   Пример:

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. Нажмите кнопку **Добавить**.

## <a name="deploy-the-azure-sql-edge-module"></a>Развертывание модуля SQL Azure для пограничных вычислений

1. Разверните модуль SQL для пограничных вычислений, выполнив действия, описанные в статье [Развертывание предварительной версии Базы данных SQL Azure для пограничных вычислений](https://docs.microsoft.com/azure/sql-database-edge/deploy-portal#deploy-sql-database-edge).

2. На странице **Выбор модулей** в разделе **Specify Route** (Задание маршрута) укажите маршруты для связи модуля с центром IoT Edge следующим образом. 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   Пример:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. В параметрах **двойник модуля** убедитесь, что SQLPackage и ASAJonInfo обновлены с помощью соответствующих URL-адресов SAS, сохраненных ранее в этом руководстве.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>Next Steps

- [Развертывание модели ML в SQL Azure для пограничных вычислений с помощью ONNX](tutorial-run-ml-model-on-sql-edge.md)
