---
title: Параметры ресурсов трудовых книжек Azure Monitor
description: Упростите создание сложных отчетов с помощью готовых настраиваемых параметризованных книг
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cc2cde7932f783f63ee2783f0589ce4f88f248a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658108"
---
# <a name="workbook-resource-parameters"></a>Параметры ресурсов рабочей книги

Параметры ресурсов позволяют собирать ресурсы в трудовых книжках. Это полезно при определении сферы, из которой можно получить данные. Пример позволяет пользователям выбирать набор VMs, который диаграммы позже будут использовать при представлении данных.

Значения, полученные от сборщиков ресурсов, могут исходить из контекста рабочей книги, статического списка или запросов Azure Resource Graph.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Создание параметра ресурса (трудовые ресурсы)
1. Начните с пустой трудовой книжки в режиме рерит.
2. Выберите _Параметры добавления_ из ссылок в трудовой книжке.
3. Нажмите на синюю кнопку _Добавить параметр._
4. В новом параметре панели, которая всплывает ввести:
    1. Название параметра:`Applications`
    2. Тип параметра:`Resource picker`
    3. Обязательно:`checked`
    4. Разрешить несколько вариантов:`checked`
5. Получить данные от:`Workbook Resources`
6. Включите только типы ресурсов:`Application Insights`
7. Выберите "Сохранить" из панели инструментов для создания параметра.

![Изображение, показывающее создание параметра ресурса с использованием ресурсов рабочей книги](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Создание параметра ресурса (график ресурсов Azure)
1. Начните с пустой трудовой книжки в режиме рерит.
2. Выберите _Параметры добавления_ из ссылок в трудовой книжке.
3. Нажмите на синюю кнопку _Добавить параметр._
4. В новом параметре панели, которая всплывает ввести:
    1. Название параметра:`Applications`
    2. Тип параметра:`Resource picker`
    3. Обязательно:`checked`
    4. Разрешить несколько вариантов:`checked`
5. Получить данные от:`Query`
    1. Тип запроса:`Azure Resource Graph`
    2. Подписки:`Use default subscriptions`
    3. В элементе управления запросом добавьте этот фрагмент
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Выберите "Сохранить" из панели инструментов для создания параметра.

![Изображение, показывающее создание параметра ресурса с помощью графика ресурсов Azure](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> График ресурсов Azure пока не доступен во всех облаках. Убедитесь, что он поддерживается в вашем целевом облаке, если вы выберете этот подход.

[Документация по графику ресурсов Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>Создание параметра ресурса (список JSON)
1. Начните с пустой трудовой книжки в режиме рерит.
2. Выберите _Параметры добавления_ из ссылок в трудовой книжке.
3. Нажмите на синюю кнопку _Добавить параметр._
4. В новом параметре панели, которая всплывает ввести:
    1. Название параметра:`Applications`
    2. Тип параметра:`Resource picker`
    3. Обязательно:`checked`
    4. Разрешить несколько вариантов:`checked`
5. Получить данные от:`JSON`
    1. В управлении содержимого добавьте этот фрагмент Json
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Нажмите _Update_ на кнопку "Синяя" обновление.
6. Дополнительно установить `Include only resource types` для _приложения Исследования_
7. Выберите "Сохранить" из панели инструментов для создания параметра.

## <a name="referencing-a-resource-parameter"></a>Ссылка на параметр ресурса
1. Добавьте элемент управления запросами в трудовую книгу и выберите ресурс Application Insights.
2. Используйте _application Insights_ Drop down, чтобы привязать параметр к элементу управления. При этом область запроса определяется ресурсами, возвращенными параметром во время выполнения.
4. В управлении КЗЛ добавьте этот фрагмент
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Выполнить запрос, чтобы увидеть результаты. 

![Изображение, показывающее параметр ресурса, на который ссылаются в элементе управления запросом](./media/workbooks-resources/resource-reference.png)

> Этот подход может быть использован для привязки ресурсов к другим элементам управления, таким как метрики.

## <a name="resource-parameter-options"></a>Параметры параметров ресурса
| Параметр | Объяснение | Пример |
| ------------- |:-------------|:-------------|
| `{Applications}` | Выбранный идентификатор ресурса | _/подписка/<подиденное>/ресурсгруппы/<ресурсная группа>/поставщики/<ресурсного типа>/акмеаутаутация_ |
| `{Applications:label}` | Метка выбранного ресурса | `acmefrontend` |
| `{Applications:value}` | Значение выбранного ресурса | _'/подписка/<подиденные>/ресурсгруппы/<ресурс-группы>/провайдеры/<ресурсного типа>/акмеаутативация'_ |
| `{Applications:name}` | Название выбранного ресурса | `acmefrontend` |
| `{Applications:resourceGroup}` | Ресурсная группа выбранного ресурса | `acmegroup` |
| `{Applications:resourceType}` | Тип выбранного ресурса | _microsoft.insights/components_ |
| `{Applications:subscription}` | Подписка выбранного ресурса |  |
| `{Applications:grid}` | Сетка, показывающая свойства ресурса. Полезно отобразить в текстовом блоке во время отладки  |  |

## <a name="next-steps"></a>Дальнейшие действия

* [Начать](workbooks-visualizations.md) узнавать больше о рабочих книгах много богатых вариантов визуализации.
* [Контролируйте](workbooks-access-control.md) и делитесь доступом к ресурсам вашей трудовой книжки.
