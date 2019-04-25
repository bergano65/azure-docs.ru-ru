---
title: Управление пакетами Python 2 в службе автоматизации Azure
description: В этой статье описывается управление пакетами Python 2 в службе автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b53e07d6086f2a02fd1bbd158ffc09dc95b0c377
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60500146"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Управление пакетами Python 2 в службе автоматизации Azure

Служба автоматизации Azure позволяет выполнять модули runbook Python 2 в Azure и в гибридных рабочих ролях Runbook Linux. Чтобы упростить модули runbook, можно использовать пакеты Python для импорта необходимых модулей. В этой статье описывается управление пакетами Python и их использование в службе автоматизации Azure.

## <a name="import-packages"></a>Импорт пакетов

В учетной записи службы автоматизации в области **Общие ресурсы** выберите **Пакеты Python 2**. Щелкните **+ Добавить пакет Python 2**.

![Добавление пакета Python](media/python-packages/add-python-package.png)

На странице **Добавление пакета Python 2** выберите локальный пакет для передачи. Пакет может быть файлом `.whl` или `.tar.gz`. Выбрав пакет, нажмите кнопку **ОК**, чтобы передать его.

![Добавление пакета Python](media/python-packages/upload-package.png)

После импорта пакета, он отображается на **пакетов Python 2** страницы в учетной записи службы автоматизации. Если необходимо удалить пакет, выберите его и нажмите **Удалить** на странице пакета.

![Список пакетов](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Импорт пакетов с зависимостями

Служба автоматизации Azure не разрешается зависимости для пакетов python во время импорта. Существует два способа импорта пакета со всеми ее зависимостями. Только один из следующих действий необходимо использовать для импорта пакетов в учетной записи службы автоматизации.

### <a name="manually-download"></a>Скачивание вручную

На Windows 64-разрядной версией, машины с [Python версии 2.7](https://www.python.org/downloads/release/latest/python2) и [pip](https://pip.pypa.io/en/stable/) установлен, выполните следующую команду для загрузки пакета и всех его зависимостей:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

После загрузки пакетов, их можно импортировать в учетную запись службы автоматизации.

### <a name="runbook"></a>Модуль Runbook

Импорт модуля runbook python [Python 2 импорта пакетов из pypi в учетную запись службы автоматизации Azure](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) из коллекции в учетной записи службы автоматизации. Убедитесь, что заданы параметры запуска **Azure** и запустить модуль runbook с параметрами. Модулю runbook требуются как учетной записи запуска от учетной записи службы автоматизации для работы. Для каждого параметра убедитесь, что вы запустить его с параметром как показано в следующем списке и образа:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* - \<automationAccount\>
* -m \<modulePackage\>

![Список пакетов](media/python-packages/import-python-runbook.png)

Модуль runbook можно указать, что пакет для загрузки, например, `Azure` (четвертый параметр) будет загружать все модули Azure и всех его зависимостей, который является около 105.

После завершения runbook можно проверить **пакетов Python 2** странице в разделе **общие ресурсы** в учетной записи службы автоматизации для проверки их пакет был импортирован правильно.

## <a name="use-a-package-in-a-runbook"></a>Использование пакета в модуле runbook

После импорта пакета, теперь можно использовать его в модуле runbook. В приведенном ниже примере используется [служебный пакет службы автоматизации Azure](https://github.com/azureautomation/azure_automation_utility). Он упрощает использование Python со службой автоматизации Azure. Чтобы использовать этот пакет, следуйте инструкциям в репозитории GitHub и добавьте его в модуль runbook с помощью команды `from azure_automation_utility import get_automation_runas_credential`, например, чтобы импортировать функцию для извлечения учетной записи запуска от имени.

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

## <a name="next-steps"></a>Дальнейшие действия

Сведения о том, как начать работу с модулями runbook Python 2, см. в статье [My first Python 2 runbook](automation-first-runbook-textual-python2.md) (Мой первый модуль runbook Python 2).
