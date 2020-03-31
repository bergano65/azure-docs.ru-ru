---
title: Запуск скриптов PowerShell в Windows VM в Azure
description: В этой теме описывается, как запускать скрипты PowerShell в виртуальной машине Azure Windows с помощью функции Run Command
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fa7f72989d47499127714eddfa6b5e98aa80178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749225"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Запуск скриптов PowerShell в Windows VM с помощью команды Run Command

Функция Run Command использует агент виртуальной машины (VM) для запуска скриптов PowerShell в VM Windows VM. Эти скрипты можно использовать для управления машиной или приложением. Они могут помочь вам быстро диагностировать и устраивать доступ к ВМ и сетевые проблемы и вернуть VM в хорошее состояние.

 

## <a name="benefits"></a>Преимущества

Вы можете получить доступ к вашим виртуальным машинам несколькими способами. Команда Run Может запускать скрипты на виртуальных машинах удаленно с помощью агента VM. Вы используете команду Run Через портал Azure, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)или [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) для Windows VMs.

Эта возможность полезна во всех сценариях, где требуется запустить сценарий в виртуальной машине. Это один из неуместных способов устранения неполадок и устранения виртуальной машины, которая не имеет порт RDP или SSH открыт из-за неправильной конфигурации сети или административного пользователя.

## <a name="restrictions"></a>Ограничения

При использовании команды Run Command применяются следующие ограничения:

* Выход ограничен последними 4096 байтами.
* Минимальное время для запуска скрипта составляет около 20 секунд.
* Сценарии запускаться как система на Windows.
* Один сценарий за раз может работать.
* Скрипты, которые запрашивают сведения (в интерактивном режиме), не поддерживаются.
* Нельзя отменять запущенный скрипт.
* Максимальное время выполнения скрипта составляет 90 минут. После этого, он будет тайм-аут.
* Чтобы вернуть результаты скрипта, требуется разрешить исходящие подключения из виртуальной машины.

> [!NOTE]
> Для правильного функционирования командованию Run требуется подключение (порт 443) к общедоступным IP-адресам Azure. Если расширение не имеет доступа к этим конечным точкам, скрипты могут работать успешно, но не возвращать результаты. Если вы блокируете трафик на виртуальной машине, вы можете использовать [теги служб,](../../virtual-network/security-overview.md#service-tags) чтобы разрешить трафик на общедоступные IP-адреса Azure с помощью тега. `AzureCloud`

## <a name="available-commands"></a>Доступные команды

В таблице ниже представлен список команд, доступных для виртуальных машин Windows. Вы можете использовать команду **RunPowerShellScript** для запуска любого пользовательского скрипта, который вы хотите. При использовании Azure CLI или PowerShell для выполнения команды значение, `--command-id` которое вы предоставляете для параметра, `-CommandId` должно быть одним из следующих перечисленных значений. Когда вы указываете значение, которое не является доступной командой, вы получаете эту ошибку:

```error
The entity was not found in this Azure location
```

|**Название**|**Описание**|
|---|---|
|**RunPowerShellScript**|Запускает скрипт PowerShell.|
|**EnableRemotePS**|Настраивает компьютер для удаленного подключения к PowerShell.|
|**EnableAdminAccount**|Проверите, отключена ли учетная запись локального администратора, и позволяет ли это.|
|**IPConfig**| Отображает подробную информацию для IP-адреса, маски подсети и шлюза по умолчанию для каждого адаптера, привязанного к TCP/IP.|
|**RDPSettings**|Проверяет параметры реестра и параметры политики домена. Предлагает действия политики, если машина является частью домена или изменяет настройки на значения по умолчанию.|
|**ResetRDPCert**|Удаляет сертификат SSL, привязанный к слушателю RDP, и восстанавливает безопасность слушателя RDP по умолчанию. Используйте этот сценарий при появлении проблем с сертификатом.|
|**SetRDPPort**|Устанавливает номер порта по умолчанию или указанный пользователем для удаленного соединения рабочего стола. Позволяет правилам брандмауэра для входящего доступа в порт.|

## <a name="azure-cli"></a>Azure CLI

В следующем примере используется команда управления [запуском az vm](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) для запуска скрипта оболочки на Azure Windows VM.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Портал Azure

Перейдите на VM на [портале Azure](https://portal.azure.com) и выберите **команду Run** в рамках **OPERATIONS.** Вы видите список доступных команд для выполнения на VM.

![Список команд](./media/run-command/run-command-list.png)

Выберите команду для запуска. Некоторые команды могут иметь дополнительные или необходимые параметры ввода. Для этих команд параметры представлены в виде текстовых полей для предоставления входных значений. Для каждой команды можно просмотреть сценарий, который запускается путем расширения **скрипта View.** **RunPowerShellScript** отличается от других команд, потому что он позволяет предоставить свой собственный пользовательский скрипт.

> [!NOTE]
> Встроенные команды не редактируются.

После выбора команды выберите **«Запуск»** для выполнения сценария. После завершения скрипта он возвращает выход и любые ошибки в окне вывода. На следующем рисунке показан пример сообщения после запуска команды **RDPSettings**.

![Вывод сценария команды запуска](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

В следующем примере используется [cmdlet Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) для запуска скрипта PowerShell на Azure VM. Этот командлет ожидает, что скрипт, указанный в параметре `-ScriptPath`, расположен в локальной среде, в которой выполняется командлет.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Ограничение доступа к команде запуска

Перечисление команд запуска или указание деталей `Microsoft.Compute/locations/runCommands/read` команды требует разрешения на уровне подписки. Встроенная роль [Reader](../../role-based-access-control/built-in-roles.md#reader) и более высокие уровни имеют это разрешение.

Запуск команды требует `Microsoft.Compute/virtualMachines/runCommand/action` разрешения на уровне подписки. Роль [вкладчика виртуальной машины](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) и более высокие уровни имеют это разрешение.

Можно использовать одну из [встроенных ролей](../../role-based-access-control/built-in-roles.md) или создать [пользовательскую роль](../../role-based-access-control/custom-roles.md) для использования Команды Выполнения.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать о других способах удаленного выполнения скриптов [Run scripts in your Windows VM](run-scripts-in-vm.md)и команд в VM, см.
