---
title: Обзор пользовательских поставщиков
description: Узнайте о поставщиках пользовательских ресурсов Azure и о том, как расширить плоскость API Azure в соответствии с рабочими процессами.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398473"
---
# <a name="azure-custom-resource-providers-overview"></a>Обзор поставщиков пользовательских ресурсов Azure

Поставщики пользовательских ресурсов Azure — это платформа для размнозамости для Azure. Это позволяет определить пользовательские AAP, которые могут быть использованы для обогащения опыта Azure по умолчанию. Эта документация описывает:

- Как создать и развернуть поставщика пользовательских ресурсов Azure.
- Как использовать поставщики пользовательских ресурсов Azure для расширения существующих рабочих процессов.
- Где найти руководства и образцы кода, чтобы начать работу.

![Обзор пользовательского поставщика](./media/overview/overview.png)

> [!IMPORTANT]
> Пользовательские поставщики в настоящее время в публичном предварительном просмотре.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Что могут делать поставщики пользовательских ресурсов

Вот несколько примеров того, чего можно достичь с помощью поставщиков пользовательских ресурсов Azure:

- Расширьте API-ам-исуправления ресурсов Azure, включив в него внутренние и внешние службы.
- Включите пользовательские сценарии поверх существующих рабочих процессов Azure.
- Настройте элементы управления и эффекта диспетчера ресурсов Azure.

## <a name="what-is-a-custom-resource-provider"></a>Что такое поставщик пользовательских ресурсов

Поставщики пользовательских ресурсов Azure создаются путем создания контракта между Azure и конечным пунктом. Этот контракт определяет список новых ресурсов и действий через новый ресурс, **Microsoft.CustomProviders/resourceProviders**. Поставщик пользовательских ресурсов разоблачает эти новые AA в Azure. Поставщики пользовательских ресурсов Azure состоят из трех частей: поставщика пользовательских ресурсов, **конечных точек**и пользовательских ресурсов.

## <a name="how-to-build-custom-resource-providers"></a>Как создать пользовательские поставщики ресурсов

Поставщики пользовательских ресурсов представляют собой список контрактов между Azure и конечными точками. В этом контракте описывается, как Azure должен взаимодействовать с конечной точкой. Поставщик ресурсов действует как прокси-сервер и будет пересылать запросы и ответы на и из указанной **конечных точек.** Поставщик ресурсов может указать два типа контрактов: [**ресурсТипы**](./custom-providers-resources-endpoint-how-to.md) и [**действия.**](./custom-providers-action-endpoint-how-to.md) Они включены через определения конечных точек. Определение конечной точки состоит из трех полей: **имя,** **routingType**и **конечная точка.**

Пример конечная точка:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Свойство | Обязательно | Описание
---|---|---
name | *Да* | Имя определения конечной точки. Azure предоставит это имя через свой API-интерфейс в папке "/subscriptions/{ИД_подписки}/resourceGroups/{Имя_группы_ресурсов}/providers/Microsoft.CustomProviders/<br>resourceProviders/{Имя_поставщика ресурсов}/{Имя_определения_конечной_точки}"
routingType | *Нет* | Определяет тип договора с **конечным пунктом.** Если не указан, по умолчанию будет «Прокси».
endpoint | *Да* | Конечная точка для направления запросов. Ответ будет обработан так же, как и любые побочные эффекты запроса.

### <a name="building-custom-resources"></a>Создание пользовательских ресурсов

**РесурсТипы** описывают новые пользовательские ресурсы, которые добавляются в Azure. Они разоблачают основные методы RESTful CRUD. Подробнее [о создании пользовательских ресурсов](./custom-providers-resources-endpoint-how-to.md)

Пример поставщика пользовательских ресурсов с **помощью ресурсовТипы:**

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

AA, добавленные в Azure для приведенного выше образца:

HttpMethod | Образец URI | Описание
---|---|---
ОТПРАВКА | /subscriptions/{subscriptionId}/resourceGroups/{имя_группы_ресурсов}/<br>providers/Microsoft.CustomProviders/resourceProviders/{имя поставщика ресурсов}/<br>myCustomResources/«customResourceName»?api-версия 2018-09-01-предварительный просмотр | API Azure REST призывает к созданию нового ресурса.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{имя_группы_ресурсов}/<br>providers/Microsoft.CustomProviders/resourceProviders/{имя поставщика ресурсов}/<br>myCustomResources/«customResourceName»?api-версия 2018-09-01-предварительный просмотр | API Azure REST призывает удалить существующий ресурс.
GET | /subscriptions/{subscriptionId}/resourceGroups/{имя_группы_ресурсов}/<br>providers/Microsoft.CustomProviders/resourceProviders/{имя поставщика ресурсов}/<br>myCustomResources/«customResourceName»?api-версия 2018-09-01-предварительный просмотр | API Azure REST для извлечения существующего ресурса.
GET | /subscriptions/{subscriptionId}/resourceGroups/{имя_группы_ресурсов}/<br>providers/Microsoft.CustomProviders/resourceProviders/{имя поставщика ресурсов}/<br>myCustomРесурсы?апи-версия 2018-09-01-предварительный просмотр | API Azure REST для получения списка существующих ресурсов.

### <a name="building-custom-actions"></a>Создание пользовательских действий

**Действия** описывают новые действия, добавленные в Azure. Они могут быть выставлены на вершине поставщика ресурсов или вложенные под **resourceType**. Подробнее [о создании пользовательских действий](./custom-providers-action-endpoint-how-to.md)

Пример поставщика пользовательских ресурсов с **действиями:**

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

AA, добавленные в Azure для приведенного выше образца:

HttpMethod | Образец URI | Описание
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{имя_группы_ресурсов}/<br>providers/Microsoft.CustomProviders/resourceProviders/{имя поставщика ресурсов}/<br>myCustomAction?api-версия 2018-09-01-предварительный просмотр | API Azure REST для активации действия.

## <a name="looking-for-help"></a>Требуется помощь?

Если у вас есть вопросы к разработке поставщика пользовательских ресурсов Azure, попробуйте задать вопрос о [переполнении стеков.](https://stackoverflow.com/questions/tagged/azure-custom-providers) Подобный вопрос, возможно, уже был задан, поэтому перед его публикацией сначала проверьте наличие ответа. Чтобы быстрее получить ответ, добавьте к вопросу тег ```azure-custom-providers```!

## <a name="next-steps"></a>Следующие шаги

Из этой статьи вы узнали о настраиваемых поставщиках. Перейдите к следующей статье, чтобы создать настраиваемый поставщик.

- [Быстрый запуск: Создайте поставщика пользовательских ресурсов Azure и развернуть пользовательские ресурсы](./create-custom-provider.md)
- [Учебник: Создавайте пользовательские действия и ресурсы в Azure](./tutorial-get-started-with-custom-providers.md)
- [Как: Добавление пользовательских действий в API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Как: Добавление пользовательских ресурсов в API Azure REST](./custom-providers-resources-endpoint-how-to.md)
