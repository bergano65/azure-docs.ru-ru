---
title: Общие сведения о определение представления в управляемых приложений Azure | Документация Майкрософт
description: Описание концепции создания определение представления для управляемых приложений Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 6735787f9b43f98ab611584f3c7191c9f927dbc2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478749"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Просмотр определения артефакта в управляемых приложений Azure

Определение представления — это необязательный артефакт в управляемых приложений Azure. Это позволяет настроить на странице "Обзор" и добавить дополнительные представления, например, метрики и пользовательские ресурсы.

Эта статья содержит общие сведения о представлении определения артефакта и его возможности.

## <a name="view-definition-artifact"></a>Просмотр артефакта определения

Артефакт определения представления должны быть именованными **viewDefinition.json** и размещаются на том же уровне, что **createUiDefinition.json** и **mainTemplate.json** в ZIP-файл пакет, который создает определение управляемого приложения. Чтобы узнать, как создать ZIP-файл пакета и опубликовать определения управляемого приложения, см. в разделе [публикации определение управляемого приложения Azure](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Схема определения представления

**ViewDefinition.json** файл имеет только один верхнего уровня `views` свойство, которое представляет собой массив представлений. Каждое представление отображается в пользовательском интерфейсе управляемое приложение как элемент меню отдельные таблицы содержимое. Каждое представление имеет `kind` свойство, которое задает тип представления. Оно должно быть присвоено одно из следующих значений: [Общие сведения о](#overview), [метрики](#metrics), [CustomResources](#custom-resources). Дополнительные сведения см. в разделе текущего [схему JSON для viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Пример JSON для определения представления.

```json
{
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { },
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        }
    ]
}

```

## <a name="overview"></a>Обзор

`"kind": "Overview"`

Если вы указываете это представление в **viewDefinition.json**, он переопределяет странице обзора по умолчанию в приложение.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Свойство|Обязательно для заполнения|Описание|
|---------|---------|---------|
|Верхний колонтитул|Нет|Заголовок на странице обзора.|
|description|Нет|Описание управляемого приложения.|
|Команды|Нет|Массив дополнительные кнопки панели инструментов страницы "Обзор", см. в разделе [команды](#commands).|

## <a name="metrics"></a>metrics

`"kind": "Metrics"`

Представление метрик позволяет собирать и статистической обработки данных из ресурсов управляемого приложения [метрик Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Свойство|Обязательно для заполнения|Описание|
|---------|---------|---------|
|displayName|Нет|Отображаемый заголовок представления.|
|версия|Нет|Версия платформы, используемый для визуализации представления.|
|Диаграммы|Да|Массив диаграммы метрик страницы.|

### <a name="chart"></a>Диаграмма

|Свойство|Обязательно для заполнения|Описание|
|---------|---------|---------|
|displayName|Да|Отображаемый заголовок диаграммы.|
|chartType|Нет|Визуализацию, чтобы использовать для этой диаграммы. По умолчанию он использует график. Поддерживаемые типы диаграммы: `Bar, Line, Area, Scatter`.|
|metrics|Да|Массив метрики для отображения на диаграмме. Дополнительные сведения о метрики, поддерживаемые на портале Azure, см. в разделе [поддерживается метрики Azure Monitor](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Метрика

|Свойство|Обязательно для заполнения|ОПИСАНИЕ|
|---------|---------|---------|
|name|Да|Имя метрики.|
|AggregationType|Да|Тип статистической обработки, используемый для этой метрики. Поддерживаемые типы статистической обработки: `none, sum, min, max, avg, unique, percentile, count`|
|пространство_имен|Нет|Дополнительные сведения для использования при определении поставщика правильные метрики.|
|resourceTagFilter|Нет|Ресурс теги массива (разделяются с помощью `or` word) по каким метрикам будет отображаться. Применяется на основе фильтра типа ресурсов.|
|resourceType|Да|Тип ресурса, для которого будет одновременно показывать несколько метрик.|

## <a name="custom-resources"></a>Настраиваемые ресурсы

`"kind": "CustomResources"`

Вы можете определить несколько представлений этого типа. Представляет каждое представление **уникальный** тип настраиваемого ресурса из пользовательского поставщика вы определили в **mainTemplate.json**. Общие сведения о предварительной версии настраиваемых поставщиков Azure см. в [этой статье](custom-providers-overview.md).

В этом представлении, которые можно выполнять GET, PUT, удаление и ПУБЛИКОВАТЬ операции для вашего типа настраиваемого ресурса. Операции POST может быть глобальной пользовательских действий или настраиваемые действия в контексте к типу настраиваемого ресурса.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Свойство|Обязательно для заполнения|Описание|
|---------|---------|---------|
|displayName|Да|Отображаемый заголовок представления. Заголовок должен быть **уникальный** для каждого представления CustomResources в вашей **viewDefinition.json**.|
|версия|Нет|Версия платформы, используемый для визуализации представления.|
|resourceType|Да|Тип настраиваемого ресурса. Должно быть **уникальный** типу настраиваемого ресурса настраиваемого поставщика.|
|createUIDefinition|Нет|Создание пользовательского интерфейса определения схемы для создания команды настраиваемого ресурса. Общие сведения о создании определений пользовательского интерфейса, см. в разделе [начало работы с CreateUiDefinition](create-uidefinition-overview.md)|
|Команды|Нет|Массив дополнительные кнопки панели инструментов представления CustomResources, см. в разделе [команды](#commands).|
|columns|Нет|Массив столбцов настраиваемого ресурса. Если не определен `name` столбец будет отображаться по умолчанию. Столбец должен иметь `"key"` и `"displayName"`. Для ключа укажите ключ свойства для отображения в представлении. Если вложенными, использовать точку в качестве разделителя, например, `"key": "name"` или `"key": "properties.property1"`. Для отображаемого имени укажите отображаемое имя свойства для отображения в представлении. Вы также можете предоставить `"optional"` свойство. Если задано значение true, столбец скрыт в представлении по умолчанию.|

## <a name="commands"></a>Команды

Команды представляет собой массив дополнительные кнопки панели инструментов, которые отображаются на странице. Каждая команда представляет ЗАВЕРШАЮЩЕЕ действие из Azure пользовательского поставщика, определенного в **mainTemplate.json**. Общие сведения о настраиваемых поставщиков, см. в разделе [Общие сведения о пользовательских поставщиках Azure](custom-providers-overview.md).

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Свойство|Обязательно для заполнения|Описание|
|---------|---------|---------|
|displayName|Да|Отображаемое имя кнопки.|
|path|Да|Имя действия пользовательского поставщика. Действие должно быть определено в **mainTemplate.json**.|
|icon|Нет|Значок кнопки. Список поддерживаемых значков определяется в [схему JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Нет|Создайте схему определения пользовательского интерфейса для команды. Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об управляемых приложениях Azure см. в [этой статье](overview.md).
- Общие сведения о настраиваемых поставщиков, см. в разделе [Общие сведения о пользовательских поставщиках Azure](custom-providers-overview.md).
