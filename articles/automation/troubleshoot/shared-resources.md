---
title: Устранение неполадок c общими ресурсами службы автоматизации Azure
description: Узнайте, как устранять неполадки c общими ресурсами службы автоматизации Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 385d2969e65647ab0b5c5e21c07b127104587e7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263419"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Устранение неполадок c общими ресурсами

В этой статье рассматриваются способы устранения неполадок, которые могут возникнуть при использовании общих ресурсов службы автоматизации Azure.

## <a name="modules"></a>модули

### <a name="module-stuck-importing"></a>Сценарий: задержка при импорте модуля

#### <a name="issue"></a>Проблема

При импорте или обновлении модуля в службе автоматизации Azure обнаружилось, что модуль застопорился в состоянии **Импорт**.

#### <a name="error"></a>Ошибка

Импорт модулей PowerShell — это сложный многоэтапный процесс. При этом существует вероятность, что модуль не будет импортирован правильно. В таком случае импортируемый модуль может застопориться в переходном состоянии. Дополнительные сведения об этом процессе см. в статье [Importing a PowerShell Module]( /powershell/developer/module/importing-a-powershell-module#the-importing-process) (Импорт модуля PowerShell).

#### <a name="resolution"></a>Способы устранения:

Чтобы устранить эту проблему, необходимо удалить модуль, застопорившийся в состоянии **Импорт**, с помощью командлета [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule). Затем можно повторить импорт модуля.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="next-steps"></a>Дополнительная информация

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.