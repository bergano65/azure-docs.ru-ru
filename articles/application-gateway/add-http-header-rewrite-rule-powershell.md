---
title: Повторное создание заголовков HTTP в шлюзе приложений Azure
description: В этой статье приводятся сведения о том, как переписать заголовков HTTP в шлюзе приложений Azure с помощью Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 405bc9aed4605e9728e112595f33c879bf55ec7f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005627"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Перепишите заголовки запросов и ответов HTTP со шлюзом приложений Azure — Azure PowerShell

В этой статье показано, как использовать Azure PowerShell для настройки [SKU шлюза приложений версии 2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) переписывать заголовки HTTP в запросах и ответах.

> [!IMPORTANT]
> SKU автоматического масштабирования и шлюза приложений, избыточного между зонами, на данный момент доступен в общедоступной предварительной версии. Предварительная версия предоставляется без соглашения об уровне обслуживания. Не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

- Для работы с этим руководством требуется запустить Azure PowerShell в локальной среде. Необходим модуль Az 1.0.0 или более поздней версии. Чтобы узнать версию, выполните команду `Import-Module Az`, а затем команду `Get-Module Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). После проверки версии PowerShell выполните командлет `Login-AzAccount`, чтобы создать подключение к Azure.
- Необходимо иметь шлюз приложений версии 2, SKU, так как возможность перезаписи заголовок не поддерживается для v1 SKU. Если у вас нет v2 SKU, создать [SKU шлюза приложений версии 2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) перед началом работы.

## <a name="what-is-required-to-rewrite-a-header"></a>Что такое требуется переписать заголовок

Чтобы настроить перезапись заголовка HTTP, необходимо будет:

1. Создать новые объекты, необходимые для переработки заголовков HTTP:

   - **RequestHeaderConfiguration**: этот объект используется для указания полей заголовка запроса, которые необходимо повторно создать, и нового значения, в котором нужно повторно создать исходные заголовки.

   - **ResponseHeaderConfiguration**: этот объект используется для указания полей заголовка ответа, которые необходимо повторно создать, и нового значения, в котором нужно повторно создать исходные заголовки.

   - **ActionSet**: этот объект содержит конфигурации запросов и ответов заголовков, указанных выше.

   - **Условие**. Он является необязательной конфигурацией. Если добавляется условие перезаписи, выполняется оценка содержимого HTTP (S) запросов и ответов. Решение для выполнения определенного действия переопределения, связанный с условием перезаписи будет основываться ли HTTP (S) запроса или ответа соответствует условию перезаписи. 

     Если более одного условия связаны с действие, а затем действие выполняется только в том случае, если все условия выполняются, т. е., будет выполняться логическую операцию и.

   - **RewriteRule**: содержит несколько действие переопределения — сочетания условие перезаписи.

   - **RuleSequence**: Это является необязательной конфигурацией. Это помогает определить порядок, в котором выполняются различные правила. Это полезно при наличии нескольких правила подстановки в наборе перезаписи. Сначала выполняется с меньшим значением последовательности правило правила перезаписи. Если указать той же последовательности правила, чтобы два правила подстановки порядок выполнения будет недетерминированным.

     Если вы не укажете RuleSequence явным образом, устанавливается значение по умолчанию 100.

   - **RewriteRuleSet**: этот объект содержит несколько правила подстановки, которые будут связаны с правило маршрутизации запроса.

2. Вам потребуется присоединить rewriteRuleSet с правилом маршрутизации. Это обусловлено конфигурации перезаписи присоединяется к исходному прослушивателю посредством правила маршрутизации. При использовании базового правила маршрутизации конфигурация перезаписи связывается с прослушивателем источника и выполняется перезапись глобального заголовка. При использовании правила маршрутизации на основе пути конфигурация перезаписи заголовка определяется в сопоставлении URL-пути. Поэтому она применяется только к области конкретного пути на сайте.

Можно создать несколько наборов переопределения заголовка http, и каждый набор переопределения может применяться к несколько прослушивателей. Тем не менее можно применить только один переписать набора для конкретного прослушивателя.

## <a name="sign-in-to-azure"></a>Вход в Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Укажите конфигурацию правила перезаписи заголовка HTTP**

В этом примере мы будем изменять URL-адрес перенаправления, переписав под заголовком location в HTTP-ответа, каждый раз, когда заголовок location содержит ссылку на «azurewebsites.net». Чтобы сделать это, мы добавим условие для оценки под заголовком location в ответе содержит azurewebsites.net, используя шаблон ли `(https?):\/\/.*azurewebsites\.net(.*)$`. Мы будем использовать `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` как значение заголовка. Это приведет к замене *azurewebsites.net* с *contoso.com* в заголовке location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Получение сведений о конфигурации существующего шлюза приложения

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Получение сведений о конфигурации существующего правила маршрутизации запроса

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Обновление шлюза приложения с помощью конфигурации для перезаписи заголовков HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Удаление правила переопределения

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о настройке, необходимых для выполнения самых распространенных вариантов использования, см. в разделе [общая область переписывать сценарии](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).