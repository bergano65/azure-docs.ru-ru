---
title: Распространенные проблемы и исправления URI SAS — Azure Marketplace
description: Сведения о распространенных проблемах и рекомендациях по их устранению при работе с подписанными URL-адресами.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: 4eb3c3e893a276aed10807a13a0f2d6d3bc4e71d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87316844"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Распространенные проблемы с URI SAS и способы их устранения

Ниже перечислены некоторые общие проблемы, возникающие при работе с подписанными URL-адресами (которые используются для идентификации и совместного использования загруженных виртуальных жестких дисков для вашего решения) вместе с предлагаемыми разрешениями.

| **Проблема** | **Сообщение об ошибке** | **Исправление** |
| --------- | ------------------- | ------- |
| *Ошибка при копировании образов* |  |  |
| "?" не найден в URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URI SAS с помощью рекомендуемых средств. |
| Параметры "st" и "se" отсутствуют в URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URI SAS с соответствующими значениями **Дата начала** и **Дата окончания** в нем. |
| "sp=rl" отсутствует в URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URI SAS, указав разрешения `Read` и `List`. |
| URI SAS имеет пробелы в имени виртуального жесткого диска | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URI SAS, удалив пробелы. |
| Ошибка авторизации URI SAS | `Failure: Copying Images. Not able to download blob due to authorization error.` | Просмотрите и исправьте формат универсального кода ресурса SAS. При необходимости повторно создайте. |
| Параметры URI SAS "st" и "se" не имеют полной спецификации даты и времени | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | В URI SAS параметры **Дата начала** и **Дата окончания** (подстроки `st` и `se`) должны иметь полный формат даты и времени, например `11-02-2017T00:00:00Z`. Сокращенные версии не допускаются (некоторые команды в Azure CLI могут создать сокращенное значения по умолчанию). |
|  |  |  |

Дополнительные сведения см. в разделе [Использование подписанных URL-адресов (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
