---
title: Действие веб-перехватчика в фабрике данных Azure
description: Действие веб-перехватчика не позволяет продолжить выполнение конвейера до тех пор, пока не будет выполнена проверка присоединенного набора данных с определенным критерием, указанным пользователем.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 8c52bb21276071581a83fb3ee6a3a4a31ba0bb4a
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399999"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Действие веб-перехватчика в фабрике данных Azure
Действие веб-перехватчика можно использовать для управления выполнением конвейеров с помощью пользовательского кода. С помощью действия веб-перехватчика клиенты могут вызвать конечную точку и передать URL-адрес обратного вызова. Перед переходом к следующему действию конвейер выполняет ожидание вызова метода callback.

## <a name="syntax"></a>Синтаксис

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>Свойства типа



Свойство | Description | Допустимые значения | Обязательно
-------- | ----------- | -------------- | --------
name | Имя действия веб-перехватчика | String | Да |
type | Необходимо задать для **веб-перехватчика**. | String | Да |
method | Метод REST API для целевой конечной точки. | Строка. Поддерживаемые типы: "POST" | Да |
url | Целевая конечная точка и путь | Строка (или выражение с типом результата "строка"). | Да |
Заголовки | Заголовки, которые отправляются в запрос. Например, чтобы задать язык и тип запроса: "Headers": {"Accept-Language": "en-US", "Content-Type": "Application/JSON"}. | Строка (или выражение с типом результата "строка") | Да, требуется заголовок Content-type. "headers":{ "Content-Type":"application/json"} |
текст | Представляет полезные данные, отправляемые конечной точке. | Допустимый формат JSON (или выражение с типом resultType для JSON). Просмотрите схему полезных данных запроса в разделе [Схема полезных данных запроса](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0). | Да |
проверка подлинности | Метод проверки подлинности, используемый для вызова конечной точки. Поддерживаются следующие типы: "базовый" или "ClientCertificate". Дополнительные сведения см. в разделе [Проверка подлинности](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0). Если проверка подлинности не требуется, исключите это свойство. | Строка (или выражение с типом результата "строка") | нет |
timeout | Время, в течение которого действие будет ожидать &#39;вызова&#39; каллбаккури. Время, в течение которого действие будет ожидать вызова "Каллбаккури". Значение по умолчанию — 10mins ("00:10:00"). Формат — TimeSpan, т. е. чч: мм: СС. | String | нет |
Отчет о состоянии при обратном вызове | Разрешает пользователю сообщать о неудачном состоянии действия веб-перехватчика, которое помечает действие как неудачное | Логическое | нет |

## <a name="authentication"></a>Аутентификация

Ниже приведены поддерживаемые типы проверки подлинности в действии веб-перехватчика.

### <a name="none"></a>None

Если проверка подлинности не требуется, не включайте свойство authentication.

### <a name="basic"></a>Basic

Укажите имя пользователя и пароль для использования с обычной проверкой подлинности.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Сертификат клиента

Укажите содержимое в кодировке base64 PFX-файла и пароль.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Управляемое удостоверение

Укажите URI ресурса, для которого маркер доступа будет запрошен с помощью управляемого удостоверения для фабрики данных. Для вызова API управления ресурсами Azure используйте `https://management.azure.com/`. Дополнительные сведения об управляемых удостоверениях для ресурсов Azure см. в статье [Что такое управляемые удостоверения для ресурсов Azure?](/azure/active-directory/managed-identities-azure-resources/overview)

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Если для фабрики данных настроен репозиторий Git, необходимо сохранить учетные данные в Azure Key Vault, чтобы использовать проверку подлинности "базовый" или "сертификат клиента". В службе "Фабрика данных Azure" не хранятся пароли в Git.

## <a name="additional-notes"></a>Дополнительные замечания

Фабрика данных Azure передаст дополнительное свойство "Каллбаккури" в тексте в конечную точку URL-адреса и будет ждать вызова этого URI до указанного значения времени ожидания. Если URI не вызывается, действие завершится ошибкой с состоянием "TimedOut".

Действие веб-перехватчика завершается ошибкой при сбое вызова пользовательской конечной точки. Любое сообщение об ошибке можно добавить в тело обратного вызова и использовать в последующем действии.

Дальнейшие действия для каждого REST API вызова клиента истечет, если конечная точка не ответит через 1 минуту. Это рекомендуемый стандарт HTTP. Чтобы устранить эту проблему, необходимо реализовать шаблон 202 в этом случае, когда конечная точка будет возвращать 202 (принято) и клиент будет опрашивать.

1-минутный тайм-аут запроса не имеет ничего времени с временем ожидания действия. , Который будет использоваться для ожидания Каллбаккури.

Текст, переданный обратно в URI обратного вызова, должен быть допустимым JSON. Для заголовка Content-Type необходимо задать значение `application/json`.

При использовании параметра "сообщить о состоянии при обратном вызове" необходимо добавить следующий фрагмент кода в тело при выполнении обратного вызова:

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных:

- [Действие условия If](control-flow-if-condition-activity.md)
- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
- [Действие Until](control-flow-until-activity.md)
