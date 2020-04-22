---
title: Управление пакетами Python 2 в службе автоматизации Azure
description: В этой статье описывается управление пакетами Python 2 в службе автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 9f52dfd92d430abffe5857d231898dd4b0e7745e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679919"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Управление пакетами Python 2 в службе автоматизации Azure

Служба автоматизации Azure позволяет выполнять модули runbook Python 2 в Azure и в гибридных рабочих ролях Runbook Linux. Чтобы упростить модули runbook, можно использовать пакеты Python для импорта необходимых модулей. В этой статье описывается, как управлять пакетами Python и использовать их в Azure Automation.

## <a name="import-packages"></a>Импорт пакетов

В учетной записи автоматизации выберите **пакеты Python 2** под **общими ресурсами.** Щелкните **+ Добавить пакет Python 2**.

![Добавление пакета Python](media/python-packages/add-python-package.png)

На странице Добавление пакета Python 2 выберите локальный пакет для передачи. Пакет может быть **файлом .whl** или **.tar.gz.** Когда пакет выбран, нажмите **OK,** чтобы загрузить его.

![Добавление пакета Python](media/python-packages/upload-package.png)

После импорта пакета он указан на странице пакетов Python 2 в вашей учетной записи Автоматизации. Если вам нужно удалить пакет, выберите пакет и нажмите **Удалить**.

![Список пакетов](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Импортные пакеты с зависимостями

Автоматизация Azure не разрешает зависимости пакетов python во время процесса импорта. Есть два способа импорта пакета со всеми его зависимостями. Для импорта пакетов в учетную запись Automation необходимо использовать только один из следующих шагов.

### <a name="manually-download"></a>Ручная загрузка

На 64-битной машине Windows с установленным [python2.7](https://www.python.org/downloads/release/latest/python2) и [пипсом](https://pip.pypa.io/en/stable/) запустите следующую команду, чтобы загрузить пакет и все его зависимости:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

После загрузки пакетов их можно импортировать в учетную запись автоматизации.

### <a name="runbook"></a>Модуль Runbook

Импортируйте пакеты Python [runbook Import Python 2 из pypi в учетную запись Azure Automation](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) из галереи в вашу учетную запись Automation. Убедитесь, что параметры выполнения настроены на **Azure** и запустите книгу с параметрами. В runbook требуется учетная запись Run As для работы учетной записи Автоматизации. Для каждого параметра убедитесь, что вы начинаете его с переключателем, как видно из следующего списка и изображения:

* - \<подпискаId\>
* -g \<ресурсГруппа\>
* -автоматизацияСчет \<\>
* -m \<модульПакет\>

![Список пакетов](media/python-packages/import-python-runbook.png)

В runbook позволяет указать, какой пакет скачать. Например, использование `Azure` параметра загружает все модули Azure и все зависимости (около 105).

После завершения запуска **пакеты Python 2** под **общими ресурсами** можно проверить в учетной записи Автоматизации, чтобы убедиться, что пакет был импортирован правильно.

## <a name="use-a-package-in-a-runbook"></a>Использование пакета в модуле runbook

С импортируемой упаковкой вы можете использовать его в сборнике. В следующем примере используется [пакет утилиты Azure Automation.](https://github.com/azureautomation/azure_automation_utility) Он упрощает использование Python со службой автоматизации Azure. Чтобы использовать пакет, следуйте инструкциям в репозитории GitHub и добавьте его в книгу. Например, можно `from azure_automation_utility import get_automation_runas_credential` импортировать функцию для извлечения учетной записи Run As.

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

## <a name="next-steps"></a>Следующие шаги

Чтобы начать работу с Python 2 runbooks, смотрите [мой первый Python 2 runbook](automation-first-runbook-textual-python2.md).
