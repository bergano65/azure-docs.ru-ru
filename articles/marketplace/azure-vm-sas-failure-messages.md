---
title: Сообщения об ошибках SAS виртуальной машины — Azure Marketplace
description: Часто задаваемые вопросы при работе с подписанными URL-адресами (SAS).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 7b91cf3d49d88501fb908d157892ec3eb24bee6e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284535"
---
# <a name="virtual-machine-sas-failure-messages"></a>Сообщения об ошибках SAS виртуальной машины

Ниже перечислены некоторые общие проблемы, возникающие при работе с подписанными URL-адресами (которые используются для идентификации и совместного использования загруженных виртуальных жестких дисков для вашего решения) вместе с предлагаемыми разрешениями.

| Проблема | Сообщение об ошибке | Fix |
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

## <a name="next-steps"></a>Дальнейшие действия

- [Создать URI SAS](azure-vm-get-sas-uri.md)
