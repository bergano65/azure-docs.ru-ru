---
title: Создание графического модуля Python в службе автоматизации Azure
description: Руководство, в котором описывается создание, тестирование и публикация простого модуля Runbook Python.
services: automation
ms.subservice: process-automation
ms.date: 03/19/2019
ms.topic: conceptual
ms.openlocfilehash: b788e5bda65702305ed6f6b001b57c28939aa875
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405915"
---
# <a name="my-first-python-runbook"></a>Мой первый модуль Runbook Python

> [!div class="op_single_selector"]
> - [Графический](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [Рабочий процесс PowerShell](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

В этом руководстве описана процедура создания [модуля Runbook Python](automation-runbook-types.md#python-runbooks) в службе автоматизации Azure. Для начала вы протестируете и опубликуете простой модуль runbook. Затем мы изменим модуль runbook, настроив его для фактического управления ресурсами Azure (в нашем примере это запуск виртуальной машины Azure). Затем вы сделаете этот модуль runbook еще надежнее, добавив параметры runbook.

> [!NOTE]
> Использование веб-крючка для запуска runbook Python не поддерживается.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуется:

- Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Учетная запись службы автоматизации](automation-offering-get-started.md) , чтобы хранить модуль Runbook и выполнять проверку подлинности ресурсов Azure. Эта учетная запись должна иметь разрешение на запуск и остановку виртуальной машины.
- Виртуальная машина Azure. Это не должна быть рабочая виртуальная машина, так как в процессе изучения этого руководства ее нужно будет остановить и запустить заново.

## <a name="create-a-new-runbook"></a>Создание модуля Runbook

Для начала создайте простой модуль runbook, выводящий на экран текст *Hello World*.

1. На портале Azure выберите свою учетную запись службы автоматизации.

    Страница учетной записи в службе автоматизации позволяет быстро получить представление о ресурсах, доступных в этой учетной записи. Некоторые ресурсы уже должны быть доступны. Большинство из них — это модули, которые добавляются в каждую новую учетную запись службы автоматизации по умолчанию. Кроме того, вам потребуется ресурс учетных данных, упомянутый в [предварительных требованиях](#prerequisites).<br>

1. Выберите **Runbooks** под **автоматизацией процессов,** чтобы открыть список runbooks.
1. Выберите **Добавить книгу для** создания нового runbook.
1. Присвойте модулю Runbook имя **MyFirstRunbook-Python**.
1. Выберите **Python 2** для **типа Runbook**.
1. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

## <a name="add-code-to-the-runbook"></a>Добавление кода в модуль Runbook

Теперь вы добавляете простую `Hello World`команду для печати текста.

```python
print("Hello World!")
```

Нажмите кнопку **Сохранить**, чтобы сохранить модуль runbook.

## <a name="test-the-runbook"></a>Тестирование модуля Runbook

Прежде чем опубликовать модуль runbook и, таким образом, сделать его доступным для рабочей среды, необходимо проверить, правильно ли он работает. Чтобы протестировать модуль Runbook, нужно запустить его черновую версию и проверить его работу в интерактивном режиме.

1. Щелкните **Область тестирования**.
1. Щелкните **Пуск** , чтобы начать тестирование. Активным должен быть только этот параметр.
1. При этом создается [задание Runbook](automation-runbook-execution.md) и отображается его состояние.
   Статус задания начинается как очередь, что указывает на то, что он ждет появления сотрудника runbook в облаке. Как только рабочая роль запросит задание, оно получит состояние Запущено, а после того как модуль runbook начнет выполняться, состояние изменится на Выполняется.
1. Когда задание модуля Runbook будет выполнено, на экране появится результат. В этом случае, `Hello World`вы должны увидеть .
1. Закройте область тестирования, чтобы вернуться на холст.

## <a name="publish-and-start-the-runbook"></a>Публикация и запуск модуля Runbook

Модуль runbook, который вы только что создали, все еще находится в режиме черновика. Прежде чем запустить модуль в рабочей среде, его нужно опубликовать.
Когда вы публикуете книгу, вы перезаписываете существующую опубликованную версию с черновой версией.
В нашем случае опубликованной версии не существует, так как модуль runbook создан только что.

1. Щелкните **Опубликовать**, чтобы опубликовать модуль Runbook, а затем нажмите кнопку **Да** в появившемся запросе.
1. Если вы прокрутите влево, чтобы просмотреть книгу на странице **Runbooks,** вы должны увидеть **Статус автора** **опубликованы**.
1. Прокрутите экран вправо до области **MyFirstRunbook-Python**.
   Варианты в верхней части позволяют начать runbook, просмотреть runbook, или запланировать его, чтобы начать в какой-то момент в будущем.
2. Нажмите **"Старт",** а затем нажмите **OK,** когда открывается лезвие Start Runbook.
3. Панель вакансий вакансии открыта для созданного вами задания runbook. Вы можете закрыть это стекло, но давайте оставим его открытым, так что вы можете наблюдать за ходом работы.
1. Состояние задания отображается в поле **Сводка по заданию** и отражает состояния, которые вы наблюдали при тестировании модуля runbook.
2. Как только статус runbook показывает Завершено, нажмите **выход**. Открывается выходное стекло, где `Hello World`вы можете видеть .
3. Закройте область выходных данных.
4. Щелкните **Все журналы** , чтобы открыть область "Потоки" для задания Runbook. Вы должны `Hello World` видеть только в потоке вывода. Тем не менее, эта панель может отображать другие потоки для выполнения задания, такие как Verbose и ошибка, если runbook пишет им.
5. Закройте область потоков и область заданий, чтобы вернуться в область MyFirstRunbook-Python.
6. Нажмите **Вакансии,** чтобы открыть страницу Вакансии для этого runbook. На этой странице перечислены все задания, созданные этим runbook. В нем должно быть только одно задание, так как вы запустили задание только один раз.
7. Если щелкнуть это задание, откроется та же область заданий, что и при запуске модуля runbook. Эта панель позволяет вернуться назад во времени и просмотреть детали любого задания, которое было создано для конкретного runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Добавление аутентификации для управления ресурсами Azure

Вы протестировали и опубликовали свой модуль runbook, но пока он не выполняет никаких полезных действий. Нужно, чтобы он управлял ресурсами Azure.
Для этого скрипт должен проверить подлинность с помощью учетных данных учетной записи Automation. Вы можете использовать [пакет служебной программы службы автоматизации Azure](https://github.com/azureautomation/azure_automation_utility) для упрощения проверки подлинности и взаимодействия с ресурсами Azure.

> [!NOTE]
> Учетная запись автоматизации должна быть создана с помощью основной функции службы, чтобы был сертификат Run As.
> Если ваша учетная запись Automation не была создана с помощью принципала службы, вы можете проверить подлинность, описанную в [Authenticate, с помощью библиотек управления Azure для Python.](/azure/python/python-sdk-azure-authenticate)

1. Откройте текстовый редактор, щелкнув **Изменить** в области MyFirstRunbook-Python.

2. Добавьте следующий код для аутентификации в Azure:

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Добавление кода для создания вычислительного клиента Python и запуска виртуальной машины

Для работы с виртуальными машинами Azure создайте экземпляр [вычислительного клиента Azure для Python](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Используйте вычислительный клиент для запуска VM. Добавьте в модуль Runbook следующий код:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Где `MyResourceGroup` название группы ресурсов, содержащей VM, и `TestVM` это имя VM, который вы хотите начать.

Протестируйте и выполните модуль Runbook еще раз, чтобы убедиться, что он запускает виртуальную машину.

## <a name="use-input-parameters"></a>Использование входных параметров

В runbook в настоящее время используются жесткие значения для названий группы ресурсов и VM.
Теперь давайте добавим код, который извлекает эти значения из входных параметров.

Используйте переменную `sys.argv`, чтобы получить значения параметров.
Добавьте следующий код в модуль Runbook сразу после других инструкций `import`:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Он импортирует модуль `sys` и создает две переменные для хранения имен группы ресурсов и виртуальной машины.
Обратите внимание, что элемент списка аргументов (`sys.argv[0]`) является именем сценария, а не вводится пользователем.

Теперь можно изменить две последние строки модуля runbook, чтобы вместо жестко заданных значений использовались значения входных параметров:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

При запуске runbook Python (либо на тестовом панели или в качестве опубликованной runbook), вы можете ввести значения параметров на странице Start Runbook в соответствии с **параметрами.**

После начала ввода значения в первой коробке появляется вторая и так далее, так что вы можете ввести столько значений параметров по мере необходимости.

Значения доступны скрипту в массиве, `sys.argv` как в только что добавленном коде.

В качестве значения первого параметра введите имя своей группы ресурсов, а в качестве значения второго параметра — имя виртуальной машины, которую необходимо запустить.

![Введение значений параметров](media/automation-first-runbook-textual-python/runbook-python-params.png)

Нажмите кнопку **ОК**, чтобы запустить модуль Runbook. Будет выполнен модуль Runbook, который запустит указанную виртуальную машину.

## <a name="error-handling-in-python"></a>Обработка ошибок в Python

Вы также можете использовать следующие конвенции для извлечения различных потоков из своих runbooks Python, включая ВНИМАНИЕ, ERROR и потоки DEBUG.

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

Ниже приводится следующий `try...except` пример, в каком-то блоке показана эта конвенция.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> Azure Automation не `sys.stderr`поддерживает.

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы начать работу с PowerShell runbooks, [см.](automation-first-runbook-textual-powershell.md)
- Чтобы начать работу с графическими runbooks, [см.](automation-first-runbook-graphical.md)
- Чтобы начать работу с runbooks рабочего процесса PowerShell, [см.](automation-first-runbook-textual.md)
- Чтобы узнать больше о типах runbook, [Azure Automation runbook types](automation-runbook-types.md)их преимуществах и ограничениях, см.
- Дополнительные сведения о разработке для Azure с использованием Python см. в статье [Azure для разработчиков Python](/azure/python/).
- Примеры модулей Runbook Python 2 см. на странице [Azure для разработчиков Python](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).
