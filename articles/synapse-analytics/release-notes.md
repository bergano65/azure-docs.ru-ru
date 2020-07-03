---
title: Заметки о выпуске
description: Заметки о выпуске Azure Synapse Analytics (рабочие области)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 514694dc2e3f06db2fb80f6b3ba0106343be11d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658506"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Заметки о выпуске Azure Synapse Analytics (предварительная версия)

В этой статье описаны ограничения Azure Synapse Analytics (рабочие области) и проблемы, которые могут возникать с этой службой. Дополнительные сведения см. в статье об [Azure Synapse Analytics (рабочие области)](overview-what-is.md).

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (рабочие области) 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- Проблема и последствия для клиентов: Рабочие области, созданные пакетом SDK, не могут запустить Synapse Studio

- Обходное решение. Необходимо выполнить следующие шаги. 
  1.    Создайте рабочую область, выполнив команду `az synapse workspace create`.
  2.    Извлеките идентификатор управляемого удостоверения, выполнив команду `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`.
  3.    Добавьте рабочую область в качестве роли в учетную запись хранения, выполнив команду ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`.
  4.    Добавьте правило брандмауэра, выполнив команду ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание рабочей области](quickstart-create-workspace.md)
* [Использование Synapse Studio](quickstart-synapse-studio.md)
* [Создание пула SQL](quickstart-create-sql-pool-portal.md)
* [Использование службы SQL по запросу](quickstart-sql-on-demand.md)
* [Создание пула Apache Spark](quickstart-create-apache-spark-pool-portal.md)