---
title: Общие сведения об определении представления в управляемых приложениях Azure | Документация Майкрософт
description: Описание концепции создания определения представления для управляемых приложений Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: b23e844cb550a98328951bc6efae3c5039ff73bf
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607539"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Просмотр артефакта определения в управляемых приложениях Azure

View definition — это необязательный артефакт в управляемых приложениях Azure. Он позволяет настроить страницу обзора и добавить дополнительные представления, такие как метрики и настраиваемые ресурсы.

В этой статье приводятся общие сведения о артефакте определения представления и его возможностях.

## <a name="view-definition-artifact"></a>Просмотр артефакта определения

Артефакт определения представления должен называться **viewDefinition. JSON** и размещен на том же уровне, что и **createUiDefinition. JSON** , и **mainTemplate. JSON** в ZIP-пакете, который создает определение управляемого приложения. Сведения о том, как создать ZIP-пакет и опубликовать определение управляемого приложения, см. в статье [Публикация определения управляемого приложения Azure](publish-managed-app-definition-quickstart.md) .

## <a name="view-definition-schema"></a>Просмотр схемы определения

Файл **viewDefinition. JSON** имеет только одно свойство `views` верхнего уровня, которое является массивом представлений. Каждое представление отображается в пользовательском интерфейсе управляемого приложения как отдельный элемент меню в содержании. Каждое представление имеет свойство `kind`, которое задает тип представления. Необходимо задать одно из следующих значений: [Overview](#overview), [метрики](#metrics), [кустомресаурцес](#custom-resources), [Associations](#associations). Дополнительные сведения см. в разделе Текущая [схема JSON для viewDefinition. JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

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

При предоставлении этого представления в **viewDefinition. JSON**он переопределяет страницу обзора по умолчанию в управляемом приложении.

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

|Свойство|Обязательно|Description (Описание)|
|---------|---------|---------|
|Верхний колонтитул|Нет|Заголовок страницы обзора.|
|description|Нет|Описание управляемого приложения.|
|Меню|Нет|Массив дополнительных кнопок на панели инструментов на странице Обзор см. в разделе [команды](#commands).|

![Обзор](./media/view-definition/overview.png)

## <a name="metrics"></a>Метрики

`"kind": "Metrics"`

Представление метрик позволяет собирать и объединять данные из ресурсов управляемых приложений в [метриках Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

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

|Свойство|Обязательно|Description (Описание)|
|---------|---------|---------|
|displayName|Нет|Отображаемое название представления.|
|версия|Нет|Версия платформы, используемая для визуализации представления.|
|диаграммы|Да|Массив диаграмм страницы метрик.|

### <a name="chart"></a>Диаграмма

|Свойство|Обязательно|Description (Описание)|
|---------|---------|---------|
|displayName|Да|Отображаемое название диаграммы.|
|chartType|Нет|Визуализация, используемая для этой диаграммы. По умолчанию он использует график. Поддерживаемые типы диаграмм: `Bar, Line, Area, Scatter`.|
|Метрики|Да|Массив метрик для построения на этой диаграмме. Дополнительные сведения о метриках, поддерживаемых в портал Azure, см. в разделе [Поддерживаемые метрики с помощью Azure Monitor](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Метрика

|Свойство|Обязательно|Description (Описание)|
|---------|---------|---------|
|name|Да|Имя метрики.|
|aggregationType|Да|Тип статистической обработки, используемый для этой метрики. Поддерживаемые типы агрегирования: `none, sum, min, max, avg, unique, percentile, count`|
|пространство_имен|Нет|Дополнительные сведения, используемые при определении правильного поставщика метрик.|
|ресаурцетагфилтер|Нет|Массив тегов ресурсов (будет разделяться `or` словом), для которого будут отображаться метрики. Применяется поверх фильтра типов ресурсов.|
|тип_ресурса|Да|Тип ресурса, для которого будут отображаться метрики.|

![Метрики](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Настраиваемые ресурсы

`"kind": "CustomResources"`

Можно определить несколько представлений этого типа. Каждое представление представляет **уникальный** тип настраиваемого ресурса из настраиваемого поставщика, определенного в **mainTemplate. JSON**. Общие сведения о предварительной версии настраиваемых поставщиков Azure см. в [этой статье](custom-providers-overview.md).

В этом представлении можно выполнять операции GET, WHERE, DELETE и POST для типа настраиваемого ресурса. Операции POST могут быть глобальными настраиваемыми действиями или настраиваемыми действиями в контексте настраиваемого типа ресурса.

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

|Свойство|Обязательно|Description (Описание)|
|---------|---------|---------|
|displayName|Да|Отображаемое название представления. Заголовок должен быть **уникальным** для каждого представления Кустомресаурцес в **viewDefinition. JSON**.|
|версия|Нет|Версия платформы, используемая для визуализации представления.|
|тип_ресурса|Да|Тип настраиваемого ресурса. Должен быть **уникальным** настраиваемым типом ресурса настраиваемого поставщика.|
|icon|Нет|Значок представления. Список значков примеров определен в [схеме JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Нет|Создайте схему определения пользовательского интерфейса для команды создать настраиваемый ресурс. Общие сведения о создании определений пользовательского интерфейса см. в статье [Приступая к работе с CreateUiDefinition](create-uidefinition-overview.md)|
|Меню|Нет|Массив дополнительных кнопок панели инструментов в представлении Кустомресаурцес см. в разделе [команды](#commands).|
|columns|Нет|Массив столбцов настраиваемого ресурса. Если не определен, `name` столбец будет отображаться по умолчанию. Столбец должен иметь `"key"` и `"displayName"`. Для параметра ключ укажите ключ свойства, которое будет отображаться в представлении. Если вложен, используйте точку в качестве разделителя, например `"key": "name"` или `"key": "properties.property1"`. В поле Отображаемое имя укажите отображаемое имя свойства, которое будет отображаться в представлении. Можно также указать свойство `"optional"`. Если задано значение true, столбец по умолчанию скрыт в представлении.|

![кустомресаурцес](./media/view-definition/customresources.png)

## <a name="commands"></a>Команды

Команды — это массив дополнительных кнопок панели инструментов, отображаемых на странице. Каждая команда представляет действие POST из пользовательского поставщика Azure, определенного в **mainTemplate. JSON**. Общие сведения о настраиваемых поставщиках см. в статье [Обзор настраиваемых поставщиков Azure](custom-providers-overview.md).

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

|Свойство|Обязательно|Description (Описание)|
|---------|---------|---------|
|displayName|Да|Отображаемое имя кнопки команды.|
|path|Да|Имя настраиваемого действия поставщика. Действие должно быть определено в **mainTemplate. JSON**.|
|icon|Нет|Значок кнопки команды. Список значков примеров определен в [схеме JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Нет|Создайте схему определения пользовательского интерфейса для команды. Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>связи

`"kind": "Associations"`

Можно определить несколько представлений этого типа. Это представление позволяет связать существующие ресурсы с управляемым приложением с помощью настраиваемого поставщика, определенного в **mainTemplate. JSON**. Общие сведения о предварительной версии настраиваемых поставщиков Azure см. в [этой статье](custom-providers-overview.md).

В этом представлении можно расширить существующие ресурсы Azure на основе `targetResourceType`. При выборе ресурса он создает запрос на подключение к **открытому** настраиваемому поставщику, который может применить побочный результат к ресурсу. 

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

|Свойство|Обязательно|Description (Описание)|
|---------|---------|---------|
|displayName|Да|Отображаемое название представления. Заголовок должен быть **уникальным** для каждого представления ассоциаций в **viewDefinition. JSON**.|
|версия|Нет|Версия платформы, используемая для визуализации представления.|
|targetResourceType|Да|Тип целевого ресурса. Это тип ресурса, который будет отображаться для адаптации ресурсов.|
|createUIDefinition|Нет|Создайте схему определения пользовательского интерфейса для команды создать связь ресурса. Общие сведения о создании определений пользовательского интерфейса см. в статье [Приступая к работе с CreateUiDefinition](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>Требуется помощь?

Если у вас есть вопросы об Управляемых приложениях Azure, вы можете задать их на [этой странице](http://stackoverflow.com/questions/tagged/azure-managedapps). Подобный вопрос, возможно, уже был задан, поэтому перед его публикацией сначала проверьте наличие ответа. Чтобы быстрее получить ответ, добавьте к вопросу тег `azure-managedapps`!

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об управляемых приложениях Azure см. в [этой статье](overview.md).
- Общие сведения о настраиваемых поставщиках см. в статье [Обзор настраиваемых поставщиков Azure](custom-providers-overview.md).
- Сведения о создании управляемого приложения Azure с помощью настраиваемых поставщиков Azure см. в разделе [учебник. Создание управляемого приложения с помощью настраиваемых действий поставщика и типов ресурсов.](tutorial-create-managed-app-with-custom-provider.md)
