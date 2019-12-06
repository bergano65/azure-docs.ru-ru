---
title: Управление пакетами Python 2 в службе автоматизации Azure
description: В этой статье описывается управление пакетами Python 2 в службе автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f20865b92df2a197410f209cf921c5e573723286
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850199"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Управление пакетами Python 2 в службе автоматизации Azure

Служба автоматизации Azure позволяет выполнять модули runbook Python 2 в Azure и в гибридных рабочих ролях Runbook Linux. Чтобы упростить модули runbook, можно использовать пакеты Python для импорта необходимых модулей. В этой статье описывается управление пакетами Python и их использование в службе автоматизации Azure.

## <a name="import-packages"></a>Импорт пакетов

В учетной записи службы автоматизации в области **Общие ресурсы** выберите **Пакеты Python 2**. Щелкните **+ Добавить пакет Python 2**.

![Добавление пакета Python](media/python-packages/add-python-package.png)

На странице **Добавление пакета Python 2** выберите локальный пакет для передачи. Пакет может быть файлом `.whl` или `.tar.gz`. Выбрав пакет, нажмите кнопку **ОК**, чтобы передать его.

![Добавление пакета Python](media/python-packages/upload-package.png)

После импорта пакета он отображается на странице **пакеты Python 2** в учетной записи службы автоматизации. Если необходимо удалить пакет, выберите его и нажмите **Удалить** на странице пакета.

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

Импортировать модуль Runbook Python [Импорт пакетов Python 2 из PyPI в учетную запись службы автоматизации Azure](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) из коллекции в учетную запись службы автоматизации. Убедитесь, что для параметров запуска задано значение **Azure** , и запустите модуль Runbook с параметрами. Для работы модуля Runbook требуется учетная запись запуска от имени, чтобы учетная запись службы автоматизации работала. Для каждого параметра убедитесь, что вы запускаете его с помощью параметра, как показано в следующем списке и на рисунке:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<Аутоматионаккаунт\>
* -m \<Модулепаккаже\>

![Список пакетов](media/python-packages/import-python-runbook.png)

Модуль Runbook позволяет указать загружаемый пакет, например `Azure` (Четвертый параметр) будет скачивать все модули Azure и все их зависимости, что составляет примерно 105.

После завершения работы модуля Runbook можно проверить страницу **пакеты Python 2** в разделе **Общие ресурсы** в учетной записи службы автоматизации, чтобы убедиться, что они были импортированы правильно.

## <a name="use-a-package-in-a-runbook"></a>Использование пакета в модуле runbook

После импорта пакета его можно использовать в модуле Runbook. В приведенном ниже примере используется [служебный пакет службы автоматизации Azure](https://github.com/azureautomation/azure_automation_utility). Он упрощает использование Python со службой автоматизации Azure. Чтобы использовать этот пакет, следуйте инструкциям в репозитории GitHub и добавьте его в модуль runbook с помощью команды `from azure_automation_utility import get_automation_runas_credential`, например, чтобы импортировать функцию для извлечения учетной записи запуска от имени.

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
