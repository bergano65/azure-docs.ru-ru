---
title: Обзор определения представления
description: Описывает концепцию создания определения представления для управляемых приложений Azure.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: d0c60f5738bf634f9d43d6d4f0d78c1239b7ff3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650699"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Просмотр артефакта определения в управляемых приложениях Azure

Определение представления является необязательным артефактом в управляемых приложениях Azure. Это позволяет настроить обзор страницы и добавить больше просмотров, таких как метрики и пользовательских ресурсов.

В этой статье содержится обзор артефакта определения представления и его возможностей.

## <a name="view-definition-artifact"></a>Просмотр артефакта определения

Артефакт определения представления должен быть назван **viewDefinition.json** и помещен на том же уровне, что и **createUiDefinition.json** и **mainTemplate.json** в пакете .zip, который создает управляемое определение приложения. Чтобы узнать, как создать пакет .zip и опубликовать определение управляемого приложения, смотрите [Опубликовать определение управляемого приложения Azure](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Просмотр схемы определения

Файл **viewDefinition.json** имеет только `views` одно свойство верхнего уровня, которое представляет собой массив представлений. Каждое представление отображается в пользовательском интерфейсе управляемого приложения в качестве отдельного элемента меню в таблице содержимого. Каждое представление `kind` имеет свойство, которое устанавливает тип представления. Она должна быть установлена на одном из следующих значений: [Обзор](#overview), [Метрики](#metrics), [CustomResources](#custom-resources), [Ассоциации](#associations). Для получения дополнительной информации смотрите текущую [схему JSON для просмотраDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Пример JSON для определения представления:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
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
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>Обзор

`"kind": "Overview"`

Когда вы предоставляете это представление в **viewDefinition.json,** оно переопределяет страницу Обзора по умолчанию в управляемом приложении.

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

|Свойство|Обязательно|Описание|
|---------|---------|---------|
|Верхний колонтитул|нет|Заголовок страницы обзора.|
|description|нет|Описание управляемого приложения.|
|команды|нет|Массив дополнительных кнопок панели инструментов на странице обзора [см.](#commands)|

![Обзор](./media/view-definition/overview.png)

## <a name="metrics"></a>Метрики

`"kind": "Metrics"`

Представление метрик позволяет собирать и агрегировать данные из управляемых ресурсов приложения в [Azure Monitor Metrics.](../../azure-monitor/platform/data-platform-metrics.md)

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

|Свойство|Обязательно|Описание|
|---------|---------|---------|
|displayName|нет|Отображено название представления.|
|version|нет|Версия платформы, используемая для визуализации представления.|
|диаграммы|Да|Массив диаграмм страницы метрик.|

### <a name="chart"></a>Диаграмма

|Свойство|Обязательно|Описание|
|---------|---------|---------|
|displayName|Да|Отображено название диаграммы.|
|диаграммаТип|нет|Визуализация для использования для этой диаграммы. По умолчанию он использует диаграмму строки. Поддерживаемые типы диаграмм: `Bar, Line, Area, Scatter`.|
|Метрики|Да|Массив метрик для графика на этом графике. Чтобы узнать больше о метриках, поддерживаемых на портале Azure, [см.](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Метрика

|Свойство|Обязательно|Описание|
|---------|---------|---------|
|name|Да|Имя метрики.|
|агрегацияТип|Да|Тип агрегации для использования для этой метрики. Поддерживаемые типы агрегирования:`none, sum, min, max, avg, unique, percentile, count`|
|namespace|нет|Дополнительная информация для использования при определении правильного поставщика метрик.|
|resourceTagFilter|нет|Массив тегов ресурсов (будет `or` отделен словом), для которого будут отображаться метрики. Применяется поверх фильтра типа ресурса.|
|тип_ресурса|Да|Тип ресурса, для которого будут отображаться метрики.|

![Метрики](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Настраиваемые ресурсы

`"kind": "CustomResources"`

Можно определить несколько представлений этого типа. Каждое представление представляет собой **уникальный** тип пользовательского ресурса от пользовательского поставщика, который вы определили в **mainTemplate.json.** Общие сведения о предварительной версии настраиваемых поставщиков Azure см. в [этой статье](../custom-providers/overview.md).

В этом представлении вы можете выполнять операции GET, PUT, DELETE и POST для пользовательского типа ресурсов. ОПЕРАЦИИ POST могут быть глобальными пользовательскими действиями или пользовательскими действиями в контексте пользовательского типа ресурсов.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
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

|Свойство|Обязательно|Описание|
|---------|---------|---------|
|displayName|Да|Отображено название представления. Название должно быть **уникальным** для каждого представления CustomResources в вашем **представленииDefinition.json**.|
|version|нет|Версия платформы, используемая для визуализации представления.|
|тип_ресурса|Да|Пользовательский тип ресурса. Должен быть **уникальный** пользовательский тип ресурсов вашего пользовательского поставщика.|
|icon|нет|Икона вида. Список примеров иконок определен в [JSON Schema](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|создатьUIDefinition|нет|Создайте схему определения пользовательского использования для создания пользовательской команды ресурсов. Для введения в создание определений uI [см.](create-uidefinition-overview.md)|
|команды|нет|Массив дополнительных кнопок панели инструментов представления CustomResources [см.](#commands)|
|столбцы|нет|Массив столбцов пользовательского ресурса. Если не `name` определено, столбец будет показан по умолчанию. Колонка должна `"key"` `"displayName"`иметь и . Для ключа предоставьте ключ отображения в представлении. Если вложено, используйте точку в `"key": "name"` качестве `"key": "properties.property1"`делимитера, например, или . Для имени отображения укажите имя отображения свойства для отображения в представлении. Вы также можете `"optional"` предоставить свойство. Когда настроен на истину, столбец по умолчанию скрыт в представлении.|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>Команды

Команды — это массив дополнительных кнопок панели инструментов, которые отображаются на странице. Каждая команда представляет действие POST от пользовательского провайдера Azure, определенное в **mainTemplate.json.** Для введения в пользовательские поставщики, см [Azure пользовательских провайдеров обзор](../custom-providers/overview.md).

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

|Свойство|Обязательно|Описание|
|---------|---------|---------|
|displayName|Да|Отображеное имя кнопки команды.|
|path|Да|Имя действия пользовательского поставщика. Действие должно быть определено в **mainTemplate.json**.|
|icon|нет|Значок кнопки команды. Список примеров иконок определен в [JSON Schema](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|создатьUIDefinition|нет|Создайте схему определения uI для команды. Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>Сопоставления

`"kind": "Associations"`

Можно определить несколько представлений этого типа. Это представление позволяет связать существующие ресурсы с управляемым приложением через пользовательского поставщика, которого вы определили в **mainTemplate.json.** Общие сведения о предварительной версии настраиваемых поставщиков Azure см. в [этой статье](../custom-providers/overview.md).

В этом представлении можно расширить существующие `targetResourceType`ресурсы Azure на основе . При выборе ресурса он создаст запрос на участие в борту **для публичного** пользовательского поставщика, который может применить побочный эффект к ресурсу. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|Свойство|Обязательно|Описание|
|---------|---------|---------|
|displayName|Да|Отображено название представления. Название должно быть **уникальным** для каждого представления ассоциаций в вашем **представленииDefinition.json**.|
|version|нет|Версия платформы, используемая для визуализации представления.|
|targetResourceType|Да|Тип целевого ресурса. Это тип ресурса, который будет отображаться для посадки ресурсов.|
|создатьUIDefinition|нет|Создайте схему определения uI для создания команды ресурсов ассоциации. Для введения в создание определений uI [см.](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>Требуется помощь?

Если у вас есть вопросы об Управляемых приложениях Azure, вы можете задать их на [этой странице](https://stackoverflow.com/questions/tagged/azure-managedapps). Подобный вопрос, возможно, уже был задан, поэтому перед его публикацией сначала проверьте наличие ответа. Чтобы быстрее получить ответ, добавьте к вопросу тег `azure-managedapps`!

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об управляемых приложениях Azure см. в [этой статье](overview.md).
- Для введения в пользовательские поставщики, см [Azure пользовательских провайдеров обзор](../custom-providers/overview.md).
- Для создания управляемого приложения Azure с помощью пользовательских провайдеров Azure [см.](tutorial-create-managed-app-with-custom-provider.md)
