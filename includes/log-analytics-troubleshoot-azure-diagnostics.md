---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 6890c71ac7c265d46cc77751786fea4d0b228588
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67185680"
---
### <a name="troubleshoot-azure-diagnostics"></a>Устранение неполадок системы диагностики Azure

Если появляется следующее сообщение об ошибке, это значит, что поставщик ресурсов Microsoft.insights не зарегистрирован:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Чтобы зарегистрировать поставщик ресурсов, сделайте следующее на портале Azure:

1.  Слева в области навигации щелкните *Подписки*.
2.  Выберите подписку, указанную в сообщении об ошибке.
3.  Щелкните *Поставщики ресурсов*.
4.  Найдите поставщика *Microsoft.insights*.
5.  Щелкните ссылку *Зарегистрировать*.

![Регистрация поставщика ресурсов microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Зарегистрировав поставщик ресурсов *Microsoft.insights*, повторите настройку диагностики.


Если появляется следующее сообщение об ошибке, необходимо обновить версию PowerShell:

`Set-AzDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Обновите версию Azure PowerShell, следуя инструкциям в статье [Установка Azure PowerShell](/powershell/azure/install-az-ps).
