---
title: Запуск скриптов оболочки в Linux VM на Azure
description: В этой теме описывается, как запускать скрипты в виртуальной машине Azure Linux с помощью функции Run Command
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 21787854590d3ca0be2cbd6e9d167de33482c787
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72597879"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Запуск скриптов оболочки в Linux VM с помощью команды Run Command

Функция Run Command использует агент виртуальной машины (VM) для запуска скриптов оболочки в Azure Linux VM. Эти скрипты можно использовать для управления машиной или приложением. Они могут помочь вам быстро диагностировать и устраивать доступ к ВМ и сетевые проблемы и вернуть VM в хорошее состояние.

## <a name="benefits"></a>Преимущества

Вы можете получить доступ к вашим виртуальным машинам несколькими способами. Команда Run Может запускать скрипты на виртуальных машинах удаленно с помощью агента VM. Вы используете команду Run Через портал Azure, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)или [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) для Linux VMs.

Эта возможность полезна во всех сценариях, где требуется запустить сценарий в виртуальной машине. Это один из неуместных способов устранения неполадок и устранения виртуальной машины, которая не имеет порт RDP или SSH открыт из-за неправильной конфигурации сети или административного пользователя.

## <a name="restrictions"></a>Ограничения

При использовании команды Run Command применяются следующие ограничения:

* Выход ограничен последними 4096 байтами.
* Минимальное время для запуска скрипта составляет около 20 секунд.
* Скрипты, запущенные по умолчанию в качестве повышенного пользователя на Linux.
* Вы можете запустить один скрипт за один раз.
* Скрипты, которые запрашивают сведения (в интерактивном режиме), не поддерживаются.
* Нельзя отменять запущенный скрипт.
* Максимальное время выполнения скрипта составляет 90 минут. После этого, сценарий будет тайм-аут.
* Чтобы вернуть результаты скрипта, требуется разрешить исходящие подключения из виртуальной машины.

> [!NOTE]
> Для правильного функционирования командованию Run требуется подключение (порт 443) к общедоступным IP-адресам Azure. Если расширение не имеет доступа к этим конечным точкам, скрипты могут работать успешно, но не возвращать результаты. Если вы блокируете трафик на виртуальной машине, вы можете использовать [теги служб,](../../virtual-network/security-overview.md#service-tags) чтобы разрешить трафик на общедоступные IP-адреса Azure с помощью тега. `AzureCloud`

## <a name="available-commands"></a>Доступные команды

В этой таблице представлен список команд, доступных для виртуальных машин Linux. Вы можете использовать команду **RunShellScript** для запуска любого пользовательского скрипта, который вы хотите. При использовании Azure CLI или PowerShell для выполнения команды значение, `--command-id` которое вы предоставляете для параметра, `-CommandId` должно быть одним из следующих перечисленных значений. Когда вы указываете значение, которое не является доступной командой, вы получаете эту ошибку:

```error
The entity was not found in this Azure location
```

|**Название**|**Описание**|
|---|---|
|**RunShellScript**|Запускает сценарий оболочки Linux.|
|**ifconfig**| Получает конфигурацию всех сетевых интерфейсов.|

## <a name="azure-cli"></a>Azure CLI

В следующем примере используется команда [управления запуском az vm](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) для запуска скрипта оболочки на Azure Linux VM.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Чтобы запустить команды как другой `sudo -u` пользователь, введите, чтобы указать учетную запись пользователя.

## <a name="azure-portal"></a>Портал Azure

Перейдите на VM на [портале Azure](https://portal.azure.com) и выберите **команду Run** в рамках **OPERATIONS.** Вы видите список доступных команд для выполнения на VM.

![Список команд](./media/run-command/run-command-list.png)

Выберите команду для запуска. Некоторые команды могут иметь дополнительные или необходимые параметры ввода. Для этих команд параметры представлены в виде текстовых полей для предоставления входных значений. Для каждой команды можно просмотреть сценарий, который запускается путем расширения **скрипта View.** **RunShellScript** отличается от других команд, потому что он позволяет предоставить свой собственный пользовательский скрипт.

> [!NOTE]
> Встроенные команды не редактируются.

После выбора команды выберите **«Запуск»** для выполнения сценария. После завершения скрипта он возвращает выход и любые ошибки в окне вывода. На следующем снимке экрана показан пример вывода команды запуска **ifconfig**.

![Вывод сценария команды запуска](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

В следующем примере используется [cmdlet Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) для запуска скрипта PowerShell на Azure VM. Этот командлет ожидает, что скрипт, указанный в параметре `-ScriptPath`, расположен в локальной среде, в которой выполняется командлет.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Ограничение доступа к команде запуска

Перечисление команд запуска или указание деталей `Microsoft.Compute/locations/runCommands/read` команды требует разрешения на уровне подписки. Встроенная роль [Reader](../../role-based-access-control/built-in-roles.md#reader) и более высокие уровни имеют это разрешение.

Запуск команды требует `Microsoft.Compute/virtualMachines/runCommand/action` разрешения на уровне подписки. Роль [вкладчика виртуальной машины](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) и более высокие уровни имеют это разрешение.

Можно использовать одну из [встроенных ролей](../../role-based-access-control/built-in-roles.md) или создать [пользовательскую роль](../../role-based-access-control/custom-roles.md) для использования Команды Выполнения.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать о других способах удаленного выполнения скриптов [Run scripts in your Linux VM](run-scripts-in-vm.md)и команд в VM, см.
