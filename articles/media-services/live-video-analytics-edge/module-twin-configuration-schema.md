---
title: Схема JSON двойникаа модуля — Azure
description: В этом разделе описывается схема JSON двойника модуля Live Video Analytics на IoT Edge.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a342c59b35c7ebb4b6021163da76bdd3e0d449c3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84266820"
---
# <a name="module-twin-json-schema"></a>Схема JSON двойника модуля

Двойникови устройств — это документы JSON, в которых хранятся сведения о состоянии устройства, включая метаданные, конфигурации и условия. Центр Интернета вещей Azure сохраняет двойник устройства для каждого устройства, подключаемого к Центру Интернета вещей. Подробное описание см. [в статье изучение и использование модуля двойников в центре Интернета вещей](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) .

В этом разделе описывается схема JSON двойника модуля Live Video Analytics на IoT Edge.

> [!NOTE]
> Чтобы получить разрешение для доступа к ресурсам служб мультимедиа и API служб мультимедиа, вы должны пройти аутентификацию. Дополнительные сведения см. [в статье доступ к API служб мультимедиа Azure](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api).

## <a name="module-twin-properties"></a>Свойства двойника модуля

Интерактивная аналитика видео на IoT Edge предоставляет следующие свойства модуля двойника. 

|Свойство |Обязательно |Динамический |Описание: |
|---|---|---|---|
|аппликатиондатадиректори |Да |Нет |Путь к подключенному тому для сохранения конфигурации. |
|азуремедиасервицесармид |Да |Нет |Уникальный идентификатор управления ресурсами Azure для учетной записи служб мультимедиа.|
|Aadtenantid и |Да |Нет |Идентификатор клиента Azure AD.|
|аадсервицепринЦипалаппид |Да |Да |Пользователь создал идентификатор Azure AD.|
|аадсервицепринЦипалцертификате |Да<sup>*</sup>  |Да |Клиент создал сертификат AppId Azure AD.|
|аадсервицепринЦипалпассворд |Да<sup>*</sup>  |Да |Пользователь создал пароль для AppId Azure AD.|
|аадендпоинт |Нет |Нет |Конечная точка Azure AD, относящаяся к облаку. <br/>По умолчанию: `https://login.microsoftonline.com` |
|aadResourceId |Нет |Нет |Идентификатор аудитории или ресурса Azure AD, относящийся к облаку <br/>По умолчанию: `https://management.core.windows.net/` |
|armEndpoint |Нет |Нет |Конечная точка управления ресурсами Azure, зависящая от облака. <br/>По умолчанию: `https://management.azure.com/` |
|диагностикслевел |Нет |Да |Уровень детализации событий: <br/>Сведения & # x02758; Предупреждение & # x02758; Ошибка & # x02758; Критическая & # x02758; None |
|диагностиксевентсаутпутнаме |Нет |Да |Выходные данные концентратора для событий диагностики. <br/>(Пусто означает, что диагностика не публикуется)|
|оператионалевентсаутпутнаме|Нет|Да|Выходные данные концентратора для рабочих событий.<br/>(Пустое означает, что операционные события не публикуются.)
|LogLevel|Нет|Да|Это может быть: <br/>& # x000B7; Verbose<br/>& # x000B7; Сведения (по умолчанию)<br/>& # x000B7; !<br/>& # x000B7; План<br/>& # x000B7; None|
|логкатегориес|Нет|Да|Разделенный запятыми список следующих элементов: Application, Медиапипелине, Events <br/>По умолчанию: приложение, события|
|дебуглогсдиректори|Нет|Да|Каталог для журналов отладки. Если эти журналы созданы, при отсутствии отладочных журналов отключаются.

<sup>*</sup>НЕОБХОДИМО указать сертификат или пароль субъекта-службы. 

Динамические свойства можно обновлять без перезапуска модуля. Чтобы получить значения для некоторых из этих свойств, выполните инструкции из статьи [Получение доступа к API служб мультимедиа](../latest/access-api-cli-how-to.md) . 

Дополнительные сведения о роли необязательных параметров диагностики см. в статье [мониторинг и ведение журнала](monitoring-logging.md) .

```
{ 
    "properties.desired": { 
        // Required 
        "applicationDataDirectory": "/var/lib/azuremediaservices", 
        "azureMediaServicesArmId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/microsoft.media/mediaservices/<ams_account>", 
        "aadTenantId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalAppId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalPassword": "{Service principal password}", 

        // Optional API Access 
        "aadEndpoint": "https://<aad-endpoint>", 
        "aadResourceId": "https://management.core.windows.net/", 
        "armEndpoint": "https://management.azure.com/", 
        
        // Optional Diagnostics 
        "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
        "operationalEventsOutputName": "lvaEdgeOperational",
        "logLevel": "Information",
        "logCategories": "Application,Events"
    } 
} 
```

## <a name="next-steps"></a>Дальнейшие шаги

[Прямые методы](direct-methods.md)
