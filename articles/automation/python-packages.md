---
title: Управление пакетами Python 2 в службе автоматизации Azure
description: В этой статье описывается управление пакетами Python 2 в службе автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 701a5aab7a0061f8b5abfaac1b699034db2671b9
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508995"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Управление пакетами Python 2 в службе автоматизации Azure

Служба автоматизации Azure позволяет выполнять модули runbook Python 2 в Azure и в гибридных рабочих ролях Runbook Linux. Чтобы упростить модули runbook, можно использовать пакеты Python для импорта необходимых модулей. В этой статье описывается, как управлять пакетами Python и использовать их в службе автоматизации Azure.

## <a name="import-packages"></a>Импорт пакетов

В учетной записи службы автоматизации выберите **пакеты Python 2** в разделе **Общие ресурсы**. Щелкните **+ Добавить пакет Python 2**.

![Добавление пакета Python](media/python-packages/add-python-package.png)

На странице Добавление пакета Python 2 выберите локальный пакет для передачи. Пакет может быть файлом **. WHL** или **. tar. gz** . После выбора пакета нажмите кнопку **ОК** , чтобы передать его.

![Добавление пакета Python](media/python-packages/upload-package.png)

После импорта пакета он отображается на странице пакеты Python 2 в учетной записи службы автоматизации. Если необходимо удалить пакет, выберите пакет и нажмите кнопку **Удалить**.

![Список пакетов](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Импорт пакетов с зависимостями

Служба автоматизации Azure не разрешает зависимости для пакетов Python в процессе импорта. Существует два способа импортировать пакет со всеми его зависимостями. Для импорта пакетов в учетную запись службы автоматизации необходимо использовать только один из следующих шагов.

### <a name="manually-download"></a>Загрузка вручную

На компьютере с Windows 64, на котором установлена версия [Python 2.7](https://www.python.org/downloads/release/latest/python2) и [PIP](https://pip.pypa.io/en/stable/) , выполните следующую команду, чтобы скачать пакет и все его зависимости:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

После загрузки пакетов их можно импортировать в учетную запись службы автоматизации.

### <a name="runbook"></a>Модуль Runbook

 Чтобы получить модуль Runbook, [импортируйте пакеты Python 2 из PyPI в учетную запись службы автоматизации Azure](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) из коллекции в учетную запись службы автоматизации. Убедитесь, что для параметров запуска задано значение **Azure** , и запустите модуль Runbook с параметрами. Для работы модуля Runbook требуется учетная запись запуска от имени, чтобы учетная запись службы автоматизации работала. Для каждого параметра убедитесь, что вы запускаете его с помощью параметра, как показано в следующем списке и на рисунке:

* -s \<SubscriptionId\>
* -g \<resourceGroup\>
* -a \<аутоматионаккаунт\>
* -m \<модулепаккаже\>

![Список пакетов](media/python-packages/import-python-runbook.png)

Модуль Runbook позволяет указать загружаемый пакет. Например, при использовании `Azure` параметра загружаются все модули Azure и все зависимости (около 105).

После завершения работы модуля Runbook можно проверить **пакеты Python 2** в разделе **Общие ресурсы** в учетной записи службы автоматизации, чтобы убедиться, что пакет был импортирован правильно.

## <a name="use-a-package-in-a-runbook"></a>Использование пакета в модуле runbook

Импортируемый пакет можно использовать в модуле Runbook. В следующем примере используется [пакет служебной программы автоматизации Azure](https://github.com/azureautomation/azure_automation_utility). Он упрощает использование Python со службой автоматизации Azure. Чтобы использовать пакет, следуйте инструкциям в репозитории GitHub и добавьте его в модуль Runbook. Например, можно использовать `from azure_automation_utility import get_automation_runas_credential` для импорта функции для получения учетной записи запуска от имени.

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

Для разработки и тестирования модулей Runbook Python 2 в автономном режиме можно использовать модуль [эмуляции ресурсов Python в службе автоматизации Azure](https://github.com/azureautomation/python_emulated_assets) на сайте GitHub. Он позволяет ссылаться на общие ресурсы, такие как учетные данные, переменные, подключения и сертификаты.

## <a name="next-steps"></a>Следующие шаги

Чтобы приступить к работе с модулями Runbook Python 2, ознакомьтесь с [моим первым модулем Runbook Python 2](automation-first-runbook-textual-python2.md).
