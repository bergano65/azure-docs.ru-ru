---
title: Распространенные проблемы и исправления URL-адресов SAS для Azure Marketplace
description: Список распространенных проблем и возможных решений с использованием подписанного URL-адреса универсального кода ресурса (URI).
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 502ba1a65f9b0740a51c7a4da219cc87af494f27
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813313"
---
# <a name="common-sas-url-issues-and-fixes"></a>Распространенные проблемы с URL-адресом SAS и способы их устранения

В следующей таблице перечислены некоторые общие проблемы, возникающие при работе с сигнатурами общего доступа (которые используются для идентификации и совместного использования загруженных виртуальных жестких дисков для вашего решения) вместе с предлагаемыми разрешениями.

| **Проблема** | **Сообщение об ошибке** | **Исправление** | 
| --------- | ------------------- | ------- | 
| &emsp; *Ошибка при копировании образов* |  |  |
| «?» не найден в URL-адресе SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URL-адрес SAS с помощью рекомендуемых средств. |
| Параметры "St" и "SE" отсутствуют в URL-адресе SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URL-адрес SAS с соответствующими значениями **Дата начала** и **Дата окончания** в нем. | 
| "SP = RL" не в URL-адресе SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Обновите URL-адрес SAS, указав разрешения `Read` и `List`. | 
| URL-адрес SAS имеет пробелы в имени виртуального жесткого диска | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URL-адрес, удалив пробелы. |
| Ошибка авторизации URL-адреса SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Просмотрите и исправьте формат универсального кода ресурса SAS. При необходимости повторно создайте. |
| Параметры URL-адреса SAS "st" и "se" не имеют полной спецификации даты и времени | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Параметры **даты начала** и **окончания** URL-адреса SAS (`st` и `se` подстрок) должны иметь полный формат даты и времени, например `11-02-2017T00:00:00Z`. Сокращенные версии не допускаются. (Некоторые команды в Azure CLI могут создать сокращенное значения по умолчанию.) | 
|  |  |  |

Дополнительные сведения см. в статье [Использование подписанных URL-адресов (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
