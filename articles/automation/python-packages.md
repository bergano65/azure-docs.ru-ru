---
title: Управление пакетами Python 2 в службе автоматизации Azure
description: В этой статье описывается управление пакетами Python 2 в службе автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.custom: tracking-python
ms.openlocfilehash: f3ba52c1396928d8c76fb85fda3f29c625e60919
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84561852"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Управление пакетами Python 2 в службе автоматизации Azure

Служба автоматизации Azure позволяет выполнять модули runbook Python 2 в Azure и в гибридных рабочих ролях Runbook Linux. Чтобы упростить модули runbook, можно использовать пакеты Python для импорта необходимых модулей. В этой статье описывается управление пакетами Python и их использование в службе автоматизации Azure.

## <a name="import-packages"></a>Импорт пакетов

В учетной записи службы автоматизации в области **Общие ресурсы** выберите **Пакеты Python 2**. Щелкните **+ Добавить пакет Python 2**.

![Добавление пакета Python](media/python-packages/add-python-package.png)

На странице "Добавление пакета Python 2" выберите локальный пакет для передачи. Пакет может иметь расширение файла **.whl** или **.tar.gz**. Выбрав пакет, щелкните **ОК**, чтобы передать его.

![Добавление пакета Python](media/python-packages/upload-package.png)

После импорта пакета он появится в списке на странице пакетов Python 2 в учетной записи службы автоматизации. Если вам нужно удалить пакет, выберите его и нажмите кнопку **Удалить**.

![Список пакетов](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>Импорт пакетов с зависимостями

Служба автоматизации Azure не разрешает зависимости для пакетов Python в процессе импорта. У вас есть два варианта, позволяющих импортировать пакет со всеми зависимостями. Для импорта пакетов в учетную запись службы автоматизации выполните только один из следующих шагов.

### <a name="manually-download"></a>Скачивание вручную

На компьютере под управлением 64-разрядной версии Windows и с установленными [Python 2.7](https://www.python.org/downloads/release/latest/python2) и [PIP](https://pip.pypa.io/en/stable/) выполните следующую команду, чтобы скачать пакет и все его зависимости:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Когда скачивание пакетов завершится, импортируйте их в учетную запись службы автоматизации.

### <a name="runbook"></a>Модуль Runbook

 Чтобы получить runbook, [импортируйте пакеты Python 2 из PyPi в учетную запись службы автоматизации Azure](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) из коллекции. Убедитесь, что в параметрах запуска задано значение **Azure** и запустите runbook с нужными параметрами. Для работы runbook требуется учетная запись запуска от имени, чтобы использовать учетную запись службы автоматизации. Следите за тем, чтобы в начале каждого параметра был правильный символ, как показано в следующем списке и на рисунке ниже.

* -s\<subscriptionId\>
* -g\<resourceGroup\>
* -a\<automationAccount\>
* -m\<modulePackage\>

![Список пакетов](media/python-packages/import-python-runbook.png)

Runbook позволяет указать нужный пакет для скачивания. Например, если задать параметр `Azure`, будут скачаны все модули Azure и все зависимости (около 105).

Когда последовательность runbook будет выполнена, проверьте список **Пакеты Python 2** в разделе **Общие ресурсы** для учетной записи службы автоматизации и убедитесь, что пакет был импортирован правильно.

## <a name="use-a-package-in-a-runbook"></a>Использование пакета в модуле runbook

Завершив импорт пакета, вы сможете использовать его в runbook. В приведенном ниже примере используется [служебный пакет службы автоматизации Azure](https://github.com/azureautomation/azure_automation_utility). Он упрощает использование Python со службой автоматизации Azure. Чтобы использовать этот пакет, следуйте инструкциям в репозитории GitHub и добавьте его в последовательность runbook. Например, с помощью `from azure_automation_utility import get_automation_runas_credential` можно импортировать функцию для извлечения учетной записи запуска от имени.

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

Для автономной разработки и тестирования runbook на основе Python 2 можно использовать модуль [эмулируемых ресурсов Python для службы автоматизации Azure](https://github.com/azureautomation/python_emulated_assets), размещенный в репозитории GitHub. Он позволяет ссылаться на общие ресурсы, такие как учетные данные, переменные, подключения и сертификаты.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в статье [Создание runbook для Python](learn/automation-tutorial-runbook-textual-python2.md).
