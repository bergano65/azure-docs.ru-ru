---
title: Рабочие книги azure Monitor и шаблоны менеджера ресурсов Azure
description: Упрощение сложной отчетности с помощью предварительно построенных и пользовательских параметризированных рабочих книг Azure Monitor, развернутых через шаблоны управления ресурсами Azure
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658410"
---
# <a name="programmatically-manage-workbooks"></a>Программное управление трудовыми книжеками

Владельцы ресурсов имеют возможность создавать и управлять своими трудовыми книжками программно через шаблоны Resource Manager. 

Это может быть полезно в таких сценариях, как:
* Развертывание отчетов аналитики по оргилиям или конкретным доменотам наряду с развертыванием ресурсов. Например, можно развернуть рабочие книги о производительности и сбоях для новых приложений или виртуальных машин.
* Развертывание стандартных отчетов или панелей мониторинга с использованием трудовых книжек для существующих ресурсов.

Рабочая книга будет создана в нужной подгруппе ресурсов и с содержанием, указанным в шаблонах менеджера ресурсов.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Шаблон менеджера ресурсов Azure для развертывания трудовых книжек
1. Откройте рабочую книгу, которую вы хотите развернуть программно.
2. Переключите рабочую книгу на реритит режим, нажав на элемент панели инструментов _Edit._
3. Откройте _Расширенный_ _</>_ редактор с помощью кнопки на панели инструментов.
4. В редакторе переключите _шаблон типа_ в _шаблон ресурсного менеджера._
5. Шаблон «Менеджер ресурсов» для создания отображается в редакторе. Копировать содержимое и использовать как-это или объединить его с большим шаблоном, который также развертывает целевой ресурс.

    ![Изображение, показывающее, как получить шаблон менеджера ресурсов в рамках uI рабочей книги](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Пример шаблона менеджера ресурсов Azure
Этот шаблон показывает, как развернуть простую трудовую книгу, которая отображает "Привет мир!"
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

| Параметр | Объяснение |
| :------------- |:-------------|
| `workbookDisplayName` | Дружественное название для трудовой книжки, которая используется в галерее или сохраненном списке. Необходимо быть уникальным в области ресурсной группы и источника |
| `workbookType` | Галерея, что трудовая книжка будет показана под. Поддерживаемые значения включают `tsg`в себя трудовую книжку,, Azure Monitor и т.д. |
| `workbookSourceId` | Идентификатор экземпляра ресурса, к которому будет связана трудовая книжка. Новая трудовая книжка будет отображаться, связанные с этим экземпляром ресурса - например, в таблице ресурса содержимого в _справочнике._ Если вы хотите, чтобы ваша трудовая книга была отображаться в рабочей книге в Azure Monitor, используйте строку _Azure Monitor_ вместо идентификатора ресурсов. |
| `workbookId` | Уникальный гид для этого экземпляра книги. Используйте _«newGuid()»_ для автоматического создания нового гида. |
| `kind` | Используется для указания, является ли созданная трудовая книга общей или конфиденциальной. Используйте _совместное_ значение для общих трудовых книжек и _пользователей_ для частных. |
| `location` | Расположение Azure, где будет создана трудовая книжка. Используйте _«resourceGroup().location»_ для создания его в том же месте, что и группа ресурсов |
| `serializedData` | Содержит содержимое или полезную нагрузку, которая будет использоваться в трудовой книжке. Используйте шаблон менеджера ресурсов из uI,sui book, чтобы получить значение |

### <a name="workbook-types"></a>Типы трудовых книжки
Типы трудовых книжки указывают, какой тип галереи книги будет отображаться под. Доступные параметры: 

| Тип | Расположение галереи |
| :------------- |:-------------|
| `workbook` | По умолчанию используется в большинстве отчетов, включая галерею справочников приложений, Azure Monitor и т.д.  |
| `tsg` | Галерея Гидов по устранению неполадок в application Insights |
| `usage` | _Больше_ галерея под _использованием_ в приложенияХ |

### <a name="limitations"></a>Ограничения
По техническим причинам этот механизм не может использоваться для создания экземпляров трудовых книжек в галерее «Исследования приложений» в _справочниках._ Мы работаем над устранением этого ограничения. В то же время, мы рекомендуем вам использовать troubleshooting `tsg`Руководство галерея (workbookType: ) для развертывания приложений Исследования связанных трудовых книжек.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как используются трудовые книжки для питания нового [монитора Azure для хранения данных.](../insights/storage-insights-overview.md)

