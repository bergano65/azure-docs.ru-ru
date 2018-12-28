---
title: Устранение неполадок c общими ресурсами службы автоматизации Azure
description: Узнайте, как устранять неполадки c общими ресурсами службы автоматизации Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ce78c86cdae9a06100fd17d00e0229805e42983b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848465"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Устранение неполадок c общими ресурсами

В этой статье рассматриваются способы устранения неполадок, которые могут возникнуть при использовании общих ресурсов службы автоматизации Azure.

## <a name="modules"></a>модули

### <a name="module-stuck-importing"></a>Сценарий. Задержка при импорте модуля

#### <a name="issue"></a>Проблема

При импорте или обновлении модуля в службе автоматизации Azure обнаружилось, что модуль застопорился в состоянии **Импорт**.

#### <a name="cause"></a>Причина:

Импорт модулей PowerShell — это сложный многоэтапный процесс. При этом существует вероятность, что модуль не будет импортирован правильно. В таком случае импортируемый модуль может застопориться в переходном состоянии. Дополнительные сведения об этом процессе см. в статье [Importing a PowerShell Module]( /powershell/developer/module/importing-a-powershell-module#the-importing-process) (Импорт модуля PowerShell).

#### <a name="resolution"></a>Способы устранения:

Чтобы устранить эту проблему, необходимо удалить модуль, застопорившийся в состоянии **Импорт**, с помощью командлета [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule). Затем можно повторить импорт модуля.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="run-as-accounts"></a>Учетная запись запуска от имени

### <a name="unable-create-update"></a>Сценарий. Не удается создать или обновить учетную запись запуска от имени

#### <a name="issue"></a>Проблема

При попытке создать или обновить учетную запись запуска от имени, появляется сообщение об ошибке, подобное приведенному ниже.

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Причина:

У вас нет разрешений, необходимых для создания или обновления учетной записи запуска от имени, или ресурс заблокирован на уровне группы ресурсов.

#### <a name="resolution"></a>Способы устранения:

Чтобы создать или обновить учетную запись запуска от имени, необходимо иметь соответствующие разрешения на различные ресурсы, используемые для запуска. Дополнительные сведения о разрешениях, необходимых для создания или обновления учетной записи запуска от имени, см. в разделе [Разрешения для запуска от имени учетной записи](../manage-runas-account.md#permissions).

Если проблема связана с блокировкой, убедитесь, что ее можно удалить, и перейдите к заблокированному ресурсу. Чтобы снять блокировку щелкните ее правой кнопкой мыши и выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.