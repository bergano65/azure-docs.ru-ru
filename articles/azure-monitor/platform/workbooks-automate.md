---
title: Программное управление книгами Azure Monitor с помощью шаблонов Azure Resource Manager | Документация Майкрософт
description: Упрощение сложных отчетов с помощью предварительно созданных и настраиваемых параметризованных Azure Monitor книг, развернутых с помощью шаблонов Azure Resource Manager
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d5e22093fa796a9fbd60dc2bc242f37a6cac7cf0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166113"
---
# <a name="programmatically-manage-workbooks"></a>Программное управление книгами

Владельцы ресурсов имеют возможность программно создавать книги и управлять ими с помощью шаблонов диспетчер ресурсов. 

Это может быть полезно в таких сценариях, как:
* Развертывание отчетов по анализу организационной или доменной аналитики вместе с развертываниями ресурсов. Например, вы можете развертывать связанные с конкретной организацией книги производительности и сбоев для новых приложений или виртуальных машин.
* Развертывание стандартных отчетов или панелей мониторинга с помощью книг для существующих ресурсов.

Книга будет создана в нужной подгруппе или группе ресурсов с содержимым, указанным в шаблонах диспетчер ресурсов.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Шаблон Azure Resource Manager для развертывания книг
1. Откройте книгу, которую требуется развернуть программно.
2. Переключите книгу в режим редактирования, щелкнув элемент панели инструментов _изменить_ .
3. Откройте _Расширенный редактор_ с помощью кнопки _</>_ на панели инструментов.
4. В редакторе переключите _тип шаблона_ в _Диспетчер ресурсов шаблон_.
5. Шаблон диспетчер ресурсов для создания отображается в редакторе. Скопируйте содержимое и используйте "как есть" или объедините его с большим шаблоном, который также развертывает целевой ресурс.

    ![Изображение, показывающее, как получить шаблон диспетчер ресурсов в пользовательском интерфейсе книги](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Пример шаблона Azure Resource Manager
В этом шаблоне показано, как развернуть простую книгу, в которой отображается "Hello World!"
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Параметры шаблона

| Параметр | Пояснение |
| :------------- |:-------------|
| `workbookDisplayName` | Понятное имя для книги, используемой в коллекции или сохраненном списке. Должно быть уникальным в области группы ресурсов и источника |
| `workbookType` | Коллекция, в которой будет отображаться книга. Поддерживаются следующие значения: книга, `tsg`, Azure Monitor и т. д. |
| `workbookSourceId` | Идентификатор экземпляра ресурса, с которым будет связана книга. Новая книга будет отображена, связанная с этим экземпляром ресурса, например, в таблице ресурсов в содержимом _книги_. Если требуется, чтобы книга отображалась в коллекции книг в Azure Monitor, используйте строку _Azure Monitor_ вместо идентификатора ресурса. |
| `workbookId` | Уникальный идентификатор GUID для этого экземпляра книги. Используйте _[newGuid ()]_ для автоматического создания нового идентификатора GUID. |
| `kind` | Используется для указания, является ли созданная книга общей или частной. Используйте значение _Shared_ для общих книг и _пользователя_ для частных. |
| `location` | Расположение Azure, в котором будет создана книга. Используйте _[resourceGroup (). Location]_ , чтобы создать его в том же расположении, что и группа ресурсов. |
| `serializedData` | Содержит содержимое или полезные данные, используемые в книге. Использование шаблона диспетчер ресурсов из пользовательского интерфейса книг для получения значения |

### <a name="workbook-types"></a>Типы книг
Типы книг укажите тип коллекции книг, в которой будет отображаться новый экземпляр книги. Доступные параметры:

| Тип | Расположение коллекции |
| :------------- |:-------------|
| `workbook` | Значение по умолчанию, используемое в большинстве отчетов, включая коллекцию книг Application Insights, Azure Monitor и т. д.  |
| `tsg` | Коллекция руководств по устранению неполадок в Application Insights |
| `usage` | _Больше_ коллекций в условиях _использования_ в Application Insights |

### <a name="limitations"></a>Ограничения
По техническим причинам этот механизм нельзя использовать для создания экземпляров книг в коллекции _книг_ Application Insights. Мы работаем над устранением этого ограничения. В то же время для развертывания Application Insights связанных книг рекомендуется использовать коллекцию руководств по устранению неполадок (Воркбуктипе: `tsg`).

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как используются книги для создания новых [Azure Monitor для работы с хранилищем](../insights/storage-insights-overview.md).

