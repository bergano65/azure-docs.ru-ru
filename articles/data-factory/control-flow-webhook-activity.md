---
title: Веб-перехватчика действия в фабрике данных Azure | Документация Майкрософт
description: Действия веб-перехватчика не производит выполнение конвейера, пока не проверяет вложенного набора данных с определенным критериям, которые пользователь указывает.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 6ec43b06ce266b9ceaddb5dd21cbf52f509d6596
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764311"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Веб-перехватчика действия в фабрике данных Azure
Обработчик веб-действие можно использовать для управления выполнением конвейеров через пользовательский код. Использование действия веб-перехватчика, клиенты могут вызвать конечную точку и передать URL-адрес обратного вызова. Запуск конвейера ожидает обратного вызова, вызываемый перед переходом к следующему действию.

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



Свойство | ОПИСАНИЕ | Допустимые значения | Обязательно для заполнения
-------- | ----------- | -------------- | --------
name | Имя веб-перехватчик действия | String | Да |
type | Должно быть присвоено **веб-перехватчика**. | String | Да |
метод | Метод REST API для целевой конечной точки. | Строка. Поддерживаемые типы: «POST» | Да |
url | Целевая конечная точка и путь | Строка (или выражение с типом результата "строка"). | Да |
Заголовки | Заголовки, которые отправляются в запрос. Например, чтобы задать язык и тип в запросе: «заголовки»: {«Accept-Language»: «en-us», «Content-Type»: «application/json»}. | Строка (или выражение с типом результата "строка") | Да, требуется заголовок Content-type. "headers":{ "Content-Type":"application/json"} |
текст | Представляет полезные данные, отправляемые конечной точке. | Текст передается обратно в обратный вызов URI должен быть допустимым JSON. Просмотрите схему полезных данных запроса в разделе [Схема полезных данных запроса](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0). | Да |
проверка подлинности | Метод проверки подлинности, используемый для вызова конечной точки. Поддерживаемые типы: «Базовый» или «ClientCertificate.» Дополнительные сведения см. в разделе [Проверка подлинности](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0). Если проверка подлинности не требуется, исключите это свойство. | Строка (или выражение с типом результата "строка") | Нет |
timeout | Время ожидания для действия &#39;callBackUri&#39; вызываемого. Как долго будет ожидать действие «callBackUri» должен быть вызван. Значение по умолчанию — 10mins («00: параметра»). Формат имеет Timespan д.чч:мм:сс т. е. | String | Нет |

## <a name="additional-notes"></a>Дополнительные замечания

Фабрика данных Azure, передаст дополнительное свойство «callBackUri» в тексте URL-адрес конечной точки и будет ожидать, что этот uri должен быть вызван перед значение времени ожидания, указанное. Если uri не вызывается, происходит сбой действия с состоянием «TimedOut».

Веб-перехватчик действие сам завершается сбоем, только если вызов к пользовательской конечной точки завершается сбоем. Все сообщения об ошибках могут быть добавлены в тело функции обратного вызова и использовать в последующем действии.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных:

- [Действие условия If](control-flow-if-condition-activity.md)
- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
- [Действие Until](control-flow-until-activity.md)
