---
title: Создание интерактивных отчетов с помощью Azure Monitor книг | Параметры ресурса | Документация Майкрософт
description: Упростите создание сложных отчетов с помощью готовых настраиваемых параметризованных книг
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2cd170fdc3bed04a81c66d83b9e75ed77e3c0a5a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165853"
---
# <a name="workbook-resource-parameters"></a>Параметры ресурса книги

Параметры ресурсов позволяют выбирать ресурсы в книгах. Это полезно при задании области, из которой следует получать данные. Например, пользователи могут выбрать набор виртуальных машин, которые впоследствии будут использоваться в диаграммах при представлении данных.

Значения из подбора ресурсов могут поступать из запросов к контексту книги, статического списка или из запроса графа ресурсов Azure.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Создание параметра ресурса (ресурсы книги)
1. Начните с пустой книги в режиме редактирования.
2. Выберите _Добавить параметры_ из ссылок в книге.
3. Нажмите синюю кнопку _Добавить параметр_ .
4. В новой области параметров, которая появляется на экране, введите:
    1. Имя параметра: `Applications`
    2. Тип параметра: `Resource picker`
    3. Обязательный: `checked`
    4. Разрешить множественный набор элементов: `checked`
5. Получить данные из: `Workbook Resources`
6. Включать только типы ресурсов: `Application Insights`
7. Нажмите кнопку "Сохранить" на панели инструментов, чтобы создать параметр.

![Изображение, показывающее создание параметра ресурса с помощью ресурсов книги](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Создание параметра ресурса (граф ресурсов Azure)
1. Начните с пустой книги в режиме редактирования.
2. Выберите _Добавить параметры_ из ссылок в книге.
3. Нажмите синюю кнопку _Добавить параметр_ .
4. В новой области параметров, которая появляется на экране, введите:
    1. Имя параметра: `Applications`
    2. Тип параметра: `Resource picker`
    3. Обязательный: `checked`
    4. Разрешить множественный набор элементов: `checked`
5. Получить данные из: `Query`
    1. Тип запроса: `Azure Resource Graph`
    2. Подписки: `Use default subscriptions`
    3. В элементе управления запросом добавьте этот фрагмент кода.
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Нажмите кнопку "Сохранить" на панели инструментов, чтобы создать параметр.

![Изображение, показывающее создание параметра ресурса с помощью графа ресурсов Azure](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Граф ресурсов Azure пока недоступен во всех облаках. Если выбран этот подход, убедитесь, что он поддерживается в целевом облаке.

[Документация по графу ресурсов Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>Создание параметра ресурса (список JSON)
1. Начните с пустой книги в режиме редактирования.
2. Выберите _Добавить параметры_ из ссылок в книге.
3. Нажмите синюю кнопку _Добавить параметр_ .
4. В новой области параметров, которая появляется на экране, введите:
    1. Имя параметра: `Applications`
    2. Тип параметра: `Resource picker`
    3. Обязательный: `checked`
    4. Разрешить множественный набор элементов: `checked`
5. Получить данные из: `JSON`
    1. Добавьте этот фрагмент JSON в элемент управления содержимым
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Нажмите синюю кнопку _обновления_ .
6. При необходимости задайте для `Include only resource types` значение _Application Insights_
7. Нажмите кнопку "Сохранить" на панели инструментов, чтобы создать параметр.

## <a name="referencing-a-resource-parameter"></a>Ссылка на параметр ресурса
1. Добавьте в книгу элемент управления запроса и выберите ресурс Application Insights.
2. Используйте _Application Insights_ раскрывающийся список, чтобы привязать параметр к элементу управления. Это задает область запроса для ресурсов, возвращаемых параметром во время выполнения.
4. В элементе управления ККЛ добавьте этот фрагмент кода.
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Выполните запрос, чтобы просмотреть результаты. 

![Изображение, показывающее параметр ресурса, на который ссылается элемент управления запросом](./media/workbooks-resources/resource-reference.png)

> Этот подход можно использовать для привязки ресурсов к другим элементам управления, таким как метрики.

## <a name="resource-parameter-options"></a>Параметры параметра resource
| Параметр | Пояснение | Пример |
| ------------- |:-------------|:-------------|
| `{Applications}` | Выбранный идентификатор ресурса | _/Subscriptions/< >/resourceGroups/< Resource-Group >/providers/< типа ресурсов >/акмеаусентикатион_ |
| `{Applications:label}` | Метка выбранного ресурса | `acmefrontend` |
| `{Applications:value}` | Значение выбранного ресурса | _"/Subscriptions/< >/resourceGroups/< ресурсов-Group >/providers/< Resource-Type >/акмеаусентикатион"_ |
| `{Applications:name}` | Имя выбранного ресурса | `acmefrontend` |
| `{Applications:resourceGroup}` | Группа ресурсов выбранного ресурса | `acmegroup` |
| `{Applications:resourceType}` | Тип выбранного ресурса | _Microsoft. Insights/компоненты_ |
| `{Applications:subscription}` | Подписка выбранного ресурса |  |
| `{Applications:grid}` | Сетка, показывающая свойства ресурса. Полезно для отображения в текстовом блоке во время отладки  |  |

## <a name="next-steps"></a>Дальнейшие действия

* [Узнайте](workbooks-visualizations.md) больше о книгах с множеством разнообразных вариантов визуализации.
* [Управление](workbooks-access-control.md) доступом к ресурсам книги и предоставление общего доступа к ним.