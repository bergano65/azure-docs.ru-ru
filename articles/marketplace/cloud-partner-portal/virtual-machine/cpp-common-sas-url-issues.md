---
title: Распространенные проблемы и исправления URL-адресов SAS для Azure Marketplace
description: Список распространенных проблем и возможных решений с использованием подписанного URL-адреса универсального кода ресурса (URI).
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 723762695d34380b7f237fa9082dc470dafcc8df
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147046"
---
# <a name="common-sas-url-issues-and-fixes"></a>Распространенные проблемы с URL-адресом SAS и способы их устранения

> [!IMPORTANT]
> Начиная с 13 апреля 2020 г. Мы начинаем перемещать Управление предложениями виртуальных машин Azure в центр партнеров. После миграции вы создадите предложения в центре партнеров и будете управлять ими. Следуйте инструкциям в разделе [Общие проблемы и исправления URL-адресов SAS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues) для управления перенесенными предложениями.

В следующей таблице перечислены некоторые общие проблемы, возникающие при работе с сигнатурами общего доступа (которые используются для идентификации и совместного использования загруженных виртуальных жестких дисков для вашего решения) вместе с предлагаемыми разрешениями.

| **Проблема** | **Сообщение об ошибке** | **Исправно** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Ошибка при копировании образов* |  |  |
| «?» не найден в URL-адресе SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URL-адрес SAS с помощью рекомендуемых средств. |
| Параметры "St" и "SE" отсутствуют в URL-адресе SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URL-адрес SAS с соответствующими значениями **Дата начала** и **Дата окончания** в нем. | 
| "SP = RL" не в URL-адресе SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Обновите URL-адрес SAS, указав разрешения `Read` и `List`. | 
| URL-адрес SAS имеет пробелы в имени виртуального жесткого диска | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URL-адрес, удалив пробелы. |
| Ошибка авторизации URL-адреса SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Просмотрите и исправьте формат универсального кода ресурса SAS. При необходимости повторно создайте. |
| Параметры URL-адреса SAS "st" и "se" не имеют полной спецификации даты и времени | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Параметры **даты начала** и **окончания** URL-адреса SAS`st` ( `se` и подстрок) должны иметь полный формат даты и времени, например `11-02-2017T00:00:00Z`. Сокращенные версии не допускаются. (Некоторые команды в Azure CLI могут создать сокращенное значения по умолчанию.) | 
|  |  |  |

Дополнительные сведения см. в статье [Использование подписанных URL-адресов (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
