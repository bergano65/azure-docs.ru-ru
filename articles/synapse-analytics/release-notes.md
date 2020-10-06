---
title: 'Заметки о выпуске: Azure Synapse Analytics (предварительная версия рабочих областей)'
description: Заметки о выпуске Azure Synapse Analytics (предварительная версия рабочих областей).
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f2050bf671af35df4faec8b0b1d53f4c10a9075e
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031676"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Заметки о выпуске Azure Synapse Analytics (предварительная версия рабочих областей)

В этой статье описаны ограничения Azure Synapse Analytics (рабочие области) и проблемы, которые могут возникать с этой службой. Дополнительные сведения см. в статье об [Azure Synapse Analytics (рабочие области)](overview-what-is.md).

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>Azure CLI

- Проблема и последствия для клиентов: Рабочие области, созданные пакетом SDK, не могут запустить Synapse Studio

- Обходное решение. Необходимо выполнить следующие шаги. 
  1.    Создайте рабочую область, выполнив команду `az synapse workspace create`.
  2.    Извлеките идентификатор управляемого удостоверения, выполнив команду `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`.
  3.    Добавьте рабочую область в качестве роли в учетную запись хранения, выполнив команду ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`.
  4.    Добавьте правило брандмауэра, выполнив команду ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `.

## <a name="next-steps"></a>Дальнейшие действия

* [Что представляет собой Azure Synapse?](overview-what-is.md)
* [Приступая к работе](get-started.md)
* [Часто задаваемые вопросы](overview-faq.md)
