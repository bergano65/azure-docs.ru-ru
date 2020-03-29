---
title: Повторное создание заголовков HTTP в шлюзе приложений Azure
description: В этой статье приводится информация о том, как переписать заголовки HTTP в azure Application Gateway с помощью Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64947189"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Переписать заголовки запросов и ответов HTTP с помощью шлюза приложения Azure - Azure PowerShell

В этой статье описывается, как использовать Azure PowerShell для настройки экземпляра [Application Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) для переписывания заголовков HTTP в запросах и ответах.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="before-you-begin"></a>Перед началом

- Для выполнения шагов в этой статье необходимо локально запустить Azure PowerShell. Вам также нужно иметь версию модуля Az 1.0.0 или более позднюю установку. `Import-Module Az` Выполнить, `Get-Module Az` а затем определить версию, которую вы установили. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). После проверки версии PowerShell выполните командлет `Login-AzAccount`, чтобы создать подключение к Azure.
- Вы должны иметь экземпляр Прикладного шлюза v2 SKU. Переписывание заголовков не поддерживается в v1 SKU. Если у вас нет v2 SKU, создайте экземпляр [Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) перед началом.

## <a name="create-required-objects"></a>Создание требуемых объектов

Чтобы настроить перезапись заголовка HTTP, необходимо выполнить эти шаги.

1. Создайте объекты, необходимые для перезаписи заголовка HTTP:

   - **RequestHeaderConfiguration:** Используется для указания полей заголовка запроса, которые вы собираетесь переписать, и нового значения для заголовков.

   - **ResponseHeaderConfiguration**: Используется для указания полей заголовка ответа, которые вы собираетесь переписать, и нового значения для заголовков.

   - **ActionSet**: Содержит конфигурации заголовков запросов и ответов, указанных ранее.

   - **Условие**: Дополнительная конфигурация. Переписать условия оценки содержания запросов и ответов HTTP (S). Действие перезаписи будет происходить, если запрос или ответ HTTP(S соответствует состоянию перезаписи.

     Если вы связываете более одного состояния с действием, действие происходит только тогда, когда все условия выполнены. Другими словами, операция является логичной и операцией.

   - **ПерепишитеRule**: Содержит несколько переписать действие / переписать комбинации условий.

   - **RuleSequence**: Дополнительная конфигурация, которая помогает определить порядок, в котором выполняются правила переписывания. Эта конфигурация полезна, когда у вас есть несколько правил перезаписи в наборе перезаписи. Правило перезаписи с более низким значением последовательности правил выполняется первым. Присваивайте одно и то же значение последовательности правил двум правилам переписывания, порядок выполнения не является детерминированным.

     Если вы не указали правилоSequence явно, значение по умолчанию 100 устанавливается.

   - **ПерепишитеRuleSet**: Содержит несколько правил переписывания, которые будут связаны с правилом разгрома запроса.

2. Прикрепите RewriteRuleSet к правилу разгрома. Конфигурация перезаписи прикрепляется к исходному слушателю через правило разгрома. При использовании базового правила заголовка конфигурация перезаписи заголовка связана с слушателем исходного кода и является глобальным перезаписьм заголовка. При использовании правила маршрутирования на основе путей конфигурация перезаписи заголовка определяется на карте траектории URL. В этом случае это относится только к конкретной области пути сайта.

Вы можете создать несколько наборов заголовок HTTP и применить каждый набор перезаписи к нескольким слушателям. Но вы можете применить только один набор переписать к конкретному слушателю.

## <a name="sign-in-to-azure"></a>Вход в Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Указать конфигурацию правила зазаголовка HTTP

В этом примере мы изменим URL-адрес перенаправления, переписав заголовок местоположения в ответе HTTP всякий раз, когда заголовок местоположения содержит ссылку на azurewebsites.net. Для этого мы добавим условие для оценки того, содержит ли заголовок местоположения в ответе azurewebsites.net. Мы будем использовать `(https?):\/\/.*azurewebsites\.net(.*)$`шаблон . И мы будем `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` использовать в качестве значения заголовка. Это значение заменит *azurewebsites.net* *contoso.com* в заголовке местоположения.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Извлеките конфигурацию шлюза приложения

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Извлеките конфигурацию правила разгрома запроса

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Обновление шлюза приложения с конфигурацией для переписывания заголовков HTTP

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

Чтобы узнать больше о том, как настроить некоторые общие случаи использования, [см.](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)