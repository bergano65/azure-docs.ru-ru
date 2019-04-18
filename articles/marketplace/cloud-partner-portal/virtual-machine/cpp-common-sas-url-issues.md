---
title: Распространенные проблемы с URL-адресом SAS и их решение для Azure Marketplace | Документация Майкрософт
description: Список распространенных проблем и возможных решений с использованием подписанного URL-адреса универсального кода ресурса (URI).
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/27/2018
ms.author: pbutlerm
ms.openlocfilehash: abb29cd0d31288ba7bfab7024cf7657ab6b9a3d3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58879222"
---
# <a name="common-sas-url-issues-and-fixes"></a>Распространенные проблемы с URL-адресом SAS и способы их устранения

В следующей таблице перечислены некоторые общие проблемы, возникающие при работе с сигнатурами общего доступа (которые используются для идентификации и совместного использования загруженных виртуальных жестких дисков для вашего решения) вместе с предлагаемыми разрешениями.

| **Проблема** | **Сообщение об ошибке** | **Исправление** | 
| --------- | ------------------- | ------- | 
| &emsp; *Ошибка при копировании образов* |  |  |
| «?» не найден в URL-адресе SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URL-адрес SAS с помощью рекомендуемых средств. |
| Параметры "st" и "se" не в URL-адресе SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URL-адрес SAS с соответствующими значениями **Дата начала** и **Дата окончания** в нем. | 
| "sp=rl" не в URL-адресе SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Обновите URL-адрес SAS, указав разрешения `Read` и `List`. | 
| URL-адрес SAS имеет пробелы в имени виртуального жесткого диска | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URL-адрес, удалив пробелы. |
| Ошибка авторизации URL-адреса SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Просмотрите и исправьте формат универсального кода ресурса SAS. При необходимости повторно создайте. |
| Параметры URL-адреса SAS "st" и "se" не имеют полной спецификации даты и времени | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | URL-адрес SAS **Дата начала** и **Дата окончания** параметров (`st` и `se` подстроки) должны иметь формат полной даты и времени, таких как `11-02-2017T00:00:00Z`. Сокращенные версии не допускаются. (Некоторые команды в Azure CLI могут создать сокращенное значения по умолчанию.) | 
|  |  |  |

Дополнительные сведения см. в статье [Использование подписанных URL-адресов (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
