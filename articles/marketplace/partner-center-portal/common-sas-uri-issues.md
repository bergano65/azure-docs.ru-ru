---
title: Распространенные проблемы и исправления URI SAS — Azure Marketplace
description: Обнаружены распространенные проблемы и рекомендации по их устранению при работе с подписями общего доступа.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266244"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Распространенные проблемы и исправления URI SAS

> [!IMPORTANT]
> Мы переносим Управление предложениями виртуальной машины Azure из Портал Cloud Partner в центр партнеров. Пока ваши предложения не будут перенесены, следуйте инструкциям в разделе [распространенные проблемы и исправления URI SAS](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) для портал Cloud Partner управления вашими предложениями.

Ниже приведены распространенные проблемы, возникающие при работе с подписанными URL-адресами (которые используются для обнаружения и совместного использования передаваемых виртуальных жестких дисков в решении), а также предлагаемые решения.

| **Проблема** | **Сообщение об ошибке** | **Исправно** |
| --------- | ------------------- | ------- |
| *Ошибка при копировании образов* |  |  |
| "?" не найден в URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URI SAS с помощью рекомендуемых средств. |
| Параметры "St" и "SE" отсутствуют в URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URI SAS, указав правильные значения **даты начала** и **окончания** . |
| "SP = RL" не в URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URI SAS с разрешениями `Read` , `List`заданными как и. |
| URI SAS содержит пробелы в имени виртуального жесткого диска | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Обновите URI SAS, чтобы удалить пробелы. |
| Ошибка авторизации URI SAS | `Failure: Copying Images. Not able to download blob due to authorization error.` | Просмотрите и исправьте формат универсального кода ресурса SAS. При необходимости повторно создайте. |
| Параметры URI SAS "St" и "SE" не имеют полной спецификации даты и времени | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | Параметры **даты начала** и **окончания** URI SAS (`st` и `se` подстрок) должны иметь полный формат даты и времени, например. `11-02-2017T00:00:00Z` Недопустимые сокращенные версии (некоторые команды в Azure CLI могут создавать сокращенные значения по умолчанию). |
|  |  |  |

Дополнительные сведения см. [в разделе использование подписанных URL-адресов (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
