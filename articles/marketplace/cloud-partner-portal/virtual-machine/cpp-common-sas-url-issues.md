---
title: Общие проблемы и исправления URL-адресов SAS для Azure Marketplace
description: Список распространенных проблем и возможных решений с использованием подписанного URL-адреса универсального кода ресурса (URI).
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: c575389538230218e1e6e4f172ebcfbee8ee51dc
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273466"
---
# <a name="common-sas-url-issues-and-fixes"></a>Распространенные проблемы с URL-адресом SAS и способы их устранения

> [!IMPORTANT]
> С 13 апреля 2020 года мы начнем перемещение управления вашими предложениями Azure Virtual Machine в Партнерский центр. После миграции вы будете создавать и управлять своими предложениями в Partner Center. Следуйте инструкциям в [общих проблемах SAS URL и исправлениям](https://aka.ms/AzureSAS_URL_FAQ) для управления мигрированными предложениями.

В следующей таблице перечислены некоторые общие проблемы, возникающие при работе с сигнатурами общего доступа (которые используются для идентификации и совместного использования загруженных виртуальных жестких дисков для вашего решения) вместе с предлагаемыми разрешениями.

| **Проблема** | **Сообщение об ошибке** | **Исправить** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Ошибка при копировании образов* |  |  |
| «?» не найден в URL-адресе SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URL-адрес SAS с помощью рекомендуемых средств. |
| Параметры "st" и "se" не в URL-адресе SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URL-адрес SAS с соответствующими значениями **Дата начала** и **Дата окончания** в нем. | 
| "sp'rl" не в URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Обновите URL-адрес SAS, указав разрешения `Read` и `List`. | 
| URL-адрес SAS имеет пробелы в имени виртуального жесткого диска | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URL-адрес, удалив пробелы. |
| Ошибка авторизации URL-адреса SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Просмотрите и исправьте формат универсального кода ресурса SAS. При необходимости повторно создайте. |
| Параметры URL-адреса SAS "st" и "se" не имеют полной спецификации даты и времени | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Параметры **даты начала** и **даты** начала`st` `se` SAS URL должны иметь полный формат дата `11-02-2017T00:00:00Z`времени, например. Сокращенные версии не допускаются. (Некоторые команды в Azure CLI могут создать сокращенное значения по умолчанию.) | 
|  |  |  |

Дополнительные сведения см. в статье [Использование подписанных URL-адресов (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
