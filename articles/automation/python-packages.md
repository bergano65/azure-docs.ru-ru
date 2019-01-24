---
title: Управление пакетами Python 2 в службе автоматизации Azure
description: В этой статье описывается управление пакетами Python 2 в службе автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: de0998dffeac54db5311bbcde1c9499488b23556
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434978"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Управление пакетами Python 2 в службе автоматизации Azure

Служба автоматизации Azure позволяет выполнять модули runbook Python 2 в Azure и в гибридных рабочих ролях Runbook Linux. Чтобы упростить модули runbook, можно использовать пакеты Python для импорта необходимых модулей. В этой статье описывается управление пакетами Python и их использование в службе автоматизации Azure.

## <a name="import-packages"></a>Импорт пакетов

В учетной записи службы автоматизации в области **Общие ресурсы** выберите **Пакеты Python 2**. Щелкните **+ Добавить пакет Python 2**.

![Добавление пакета Python](media/python-packages/add-python-package.png)

На странице **Добавление пакета Python 2** выберите локальный пакет для передачи. Пакет может быть файлом `.whl` или `.tar.gz`. Выбрав пакет, нажмите кнопку **ОК**, чтобы передать его.

![Добавление пакета Python](media/python-packages/upload-package.png)

После импорта пакета он приводится на странице **Пакеты Python 2** в учетной записи службы автоматизации. Если необходимо удалить пакет, выберите его и нажмите **Удалить** на странице пакета.

![Список пакетов](media/python-packages/package-list.png)

## <a name="use-a-package-in-a-runbook"></a>Использование пакета в модуле runbook

Импортировав пакет, вы можете использовать его в модуле runbook. В приведенном ниже примере используется [служебный пакет службы автоматизации Azure](https://github.com/azureautomation/azure_automation_utility). Он упрощает использование Python со службой автоматизации Azure. Чтобы использовать этот пакет, следуйте инструкциям в репозитории GitHub и добавьте его в модуль runbook с помощью команды `from azure_automation_utility import get_automation_runas_credential`, например, чтобы импортировать функцию для извлечения учетной записи запуска от имени.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Разработка и тестирование модулей runbook в автономном режиме

Для разработки и тестирования модулей runbook Python 2 в автономном режиме можно использовать модуль [эмулируемых ресурсов Python для службы автоматизации Azure](https://github.com/azureautomation/python_emulated_assets) в GitHub. Он позволяет ссылаться на общие ресурсы, такие как учетные данные, переменные, подключения и сертификаты.

## <a name="next-steps"></a>Дополнительная информация

Сведения о том, как начать работу с модулями runbook Python 2, см. в статье [My first Python 2 runbook](automation-first-runbook-textual-python2.md) (Мой первый модуль runbook Python 2).
