---
title: Использование службы "Аналитика временных рядов" для хранения и анализа данных телеметрии устройств Azure IoT Plug and Play | Документация Майкрософт
description: Настройка среды службы "Аналитика временных рядов" и подключение Центра Интернета вещей для просмотра и анализа данных телеметрии устройств IoT Plug and Play.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: aa99b9059fe8e3cd5b0dfe6f7e62bd02012fd144
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422269"
---
# <a name="tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Руководство по созданию службы "Аналитика временных рядов" 2-го поколения и подключению к ней для хранения, визуализации и анализа данных телеметрии устройств IoT Plug and Play

Из этого учебника вы узнаете, как создать и правильно настроить среду службы ["Аналитика временных рядов Azure" 2-го поколения](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (TSI) для интеграции с решением IoT Plug and Play. С помощью TSI можно собирать, обрабатывать, хранить, запрашивать и визуализировать данные временного ряда в масштабе Интернета вещей.

Сначала вы подготовите среду TSI и подключите Центр Интернета вещей в качестве источника событий потоковой передачи. Затем вы выполните синхронизацию модели, чтобы создать модель временного ряда среды TSI, основанную на файлах примера модели [языка определения цифровых двойников (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl), используемых для контроллера температуры и термостатов.

## <a name="prerequisites"></a>Обязательные условия

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Чтобы не устанавливать Azure CLI локально, можно использовать Azure Cloud Shell для настройки облачных служб.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="time-series-id-selection"></a>Выбор идентификатора временного ряда

При подготовке среды TSI вам потребуется выбрать идентификатор временного ряда. Выбор соответствующего идентификатора временного ряда очень важен, так как свойство является неизменяемым и его нельзя модифицировать после установки. Выбор идентификатора временного ряда подобен выбору ключа раздела для базы данных. Как правило, идентификатор временного ряда должен быть конечным объектом модели активов. Другими словами, обычно требуется выбрать свойство идентификатора наиболее детализированного актива или датчика, который выдает данные телеметрии.

При выборе идентификатора временного ряда пользователь IoT Plug and Play задается актуальным вопросом о наличии [компонентов](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component) в моделях устройств. 

![Выбор идентификатора временного ряда](./media/tutorial-configure-tsi/ts-id-selection-pnp.png)

* Если вы выполнили инструкции из краткого руководства и устройство Центра Интернета вещей представляет собой [термостат](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json), используйте `iot-hub-connection-device-id` в качестве идентификатора временного ряда.

* Если вы выполнили инструкции оного из учебников по работе с многокомпонентным проектом [TemperatureController](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json), используйте составной ключ в разделе ниже, записанный как `iot-hub-connection-device-id, dt-subject`.

## <a name="provision-your-azure-time-series-insights-gen2-environment"></a>Планирование среды службы "Аналитика временных рядов Azure" 2-го поколения

Приведенная ниже команда выполняет следующие действия:

* Создает учетную запись хранения Azure для [холодного хранилища](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store) вашей среды, которое предназначено для долгосрочного хранения и анализа данных журнала.
  * Замените `mytsicoldstore` уникальным именем для учетной записи.
* Создает среду службы "Аналитика временных рядов Azure" 2-го поколения, включая теплое хранилище с периодом хранения 7 дней, а также холодное хранилище для неограниченного хранения. 
  * Замените значение `my-tsi-env` уникальным именем для среды TSI. 
  * Замените значение `my-pnp-resourcegroup` именем группы ресурсов, которую вы использовали во время настройки.
  * Замените `my-ts-id-property` значением свойства идентификатора временного ряда на основе описанных выше условий выбора.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties my-ts-id-property --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Теперь вы настроите созданный ранее Центр Интернета вещей в качестве [источника события](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources) среды. Когда источник событий подключен, TSI начнет индексировать события из центра, начиная с самого раннего события в очереди.

Сначала создайте уникальную группу потребителей в Центре Интернета вещей для среды TSI. Замените значение `my-pnp-hub` именем использованного ранее Центра Интернета вещей.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group 
```

Подключитесь к Центру Интернета вещей. Замените `my-pnp-resourcegroup`, `my-pnp-hub` и `my-tsi-env` собственными значениями.

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```
Перейдите к группе ресурсов на [портале Azure](https://portal.azure.com) и выберите созданную среду службы "Аналитика временных рядов" 2-го поколения. Перейдите по *URL-адресу Обозревателя Аналитики временных рядов*, показанному на странице обзора экземпляра.

![Страница обзора портала](./media/tutorial-configure-tsi/view-environment.png)

В обозревателе в разделе "Все иерархии" должны отобразиться два термостата. Далее необходимо будет курировать модель временного ряда на основе модели устройства.

![Представление обозревателя 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="model-synchronization-between-digital-twins-definition-language-and-time-series-insights-gen2"></a>Синхронизация моделей между языком определения цифровых двойников и службой "Аналитика временных рядов" 2-го поколения

Далее вы преобразуете модель устройства DTDL в модель актива в службе "Аналитика временных рядов Azure" (TSI). Модель временного ряда TSI — это средство семантического моделирования для контекстуализации данных в TSI. Модель временного ряда содержит три основных компонента:

* [Экземпляры модели временного ряда.](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-instances) Экземпляры — это собственно виртуальные представления временного ряда. Экземпляры будут однозначно идентифицированы идентификатором временного ряда.
* [Иерархии модели временного ряда.](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-hierarchies) Иерархии организуют экземпляры, определяя имена свойств и их связи.
* [Типы модели временного ряда.](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-types) Типы помогают определить [переменные](https://docs.microsoft.com/azure/time-series-insights/concepts-variables) или формулы для выполнения вычислений. Типы связаны с конкретным экземпляром.

> [!NOTE]
> Ниже приведены примеры для многокомпонентного проекта TemperatureController.

### <a name="define-your-types"></a>Определение типов

Вы можете начать прием данных в службе "Аналитика временных рядов Azure" 2-го поколения без предварительно определенной модели. Когда вы получите данные телеметрии, TSI попытается выполнить автоматическое распределение экземпляров временного ряда на основе значения свойства идентификатора временного ряда. Всем экземплярам будет назначен *тип по умолчанию*. Для представления моделей необходимо вручную создать тип. На следующем рисунке показан простой метод синхронизации модели DTDL и типа TSM:

![Синхронизация DTDL и типа TSM](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

* Идентификатор модели цифрового двойника (DTMI) будет вашим идентификатором типа.
* Именем типа может быть либо имя модели, либо отображаемое имя.
* Описание модели преобразуется в описание типа.
* Для каждого компонента телеметрии с числовой схемой создается по крайней мере одна переменная типа. 
  * Для переменных можно использовать только числовые типы данных, но если значение отправляется как строка, которая может быть проанализирована (например, `"0"`), можно использовать функцию [преобразования](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) (например, `toDouble`).
* Именем переменной может быть либо имя телеметрии, либо отображаемое имя.
* При определении выражения временного ряда (TSX) переменной обратите внимание на имя телеметрии в сети, а также ее тип данных.

> [!NOTE]
> В этом примере показаны только две переменные — агрегатная и числовая. Каждый тип может иметь до 100 переменных. Разные переменные могут ссылаться на одно и то же значение телеметрии, чтобы при необходимости выполнять различные расчеты. Полный список фильтров, статистических выражений и скалярных функций см. в документации по [синтаксису выражения временного ряда службы "Аналитика временных рядов" 2-го поколения](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).

Откройте любой текстовый редактор и сохраните приведенный ниже код JSON на локальном диске:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:Thermostat;1",
      "name": "Thermostat",
      "description": "Reports current temperature and provides desired temperature control.",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.temperature.Double"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

В Обозревателе Аналитики временных рядов перейдите на вкладку "Модель", щелкнув значок модели слева. Щелкните **Типы**, а затем — **Отправка файла JSON**:

![Передать](./media/tutorial-configure-tsi/upload-type.png)

Щелкните **Выбрать файл**, выберите сохраненный ранее файл JSON, а затем — **Отправить**.

Вы увидите только что определенный тип термостата.

### <a name="optional---create-a-hierarchy"></a>Создание иерархии (необязательно)

При необходимости можно создать иерархию для упорядочения двух компонентов термостата под родительским элементом TemeraptureController.

Щелкните *Иерархии*, а затем выберите *Добавить иерархию*. Введите `Device Fleet` в качестве имени и создайте один уровень с именем `Device Name`, а затем нажмите кнопку *Сохранить*.

![Добавление иерархии](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Присвойте экземплярам правильный тип

Далее необходимо изменить тип экземпляров и при необходимости разместить их в иерархии.

Выберите вкладку *Экземпляры* и щелкните значок *редактирования* в правом углу.

![Редактирование экземпляров](./media/tutorial-configure-tsi/edit-instance.png)

Щелкните раскрывающийся список "Тип" и выберите `Thermostat`. 

![Изменение типа экземпляра](./media/tutorial-configure-tsi/change-type.png)

Если вы создали иерархию, выберите *Поля экземпляра* и установите флажок `Device Fleet`. Введите `Temperature Controller` в качестве значения родительского устройства, а затем нажмите кнопку *Сохранить*.

![Назначение иерархии](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Повторите описанные выше действия для второго термостата.

### <a name="view-your-data"></a>Просмотр данных

Вернитесь к панели диаграмм и разверните узлы парка устройств и TemperatureController. Щелкните thermostat1, выберите переменную `Temperature`, а затем — *Добавить*, чтобы построить диаграмму значения. Повторите это действие для thermostat2.

![Изменение типа экземпляра для thermostat2](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о различных параметрах построения диаграмм, включая размер интервала и элементы управления оси Y, см. в документации по [Обозревателю Аналитики временных рядов Azure](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels).

* Подробный обзор модели временного ряда среды см. [в этой статье](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview).

* Чтобы получить подробные сведения об интерфейсах API запросов и синтаксисе выражений временного ряда, ознакомьтесь с [этой статьей](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis).




