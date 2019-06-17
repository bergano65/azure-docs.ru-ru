---
title: Повторное создание заголовков HTTP в шлюзе приложений Azure
description: Статья содержит сведения о том, как переписать заголовков HTTP в шлюзе приложений Azure с помощью Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64947189"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Перепишите заголовки запросов и ответов HTTP со шлюзом приложений Azure — Azure PowerShell

В этой статье описывается, как использовать Azure PowerShell для настройки [SKU шлюза приложений версии 2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) экземпляр переписывать заголовки HTTP в запросах и ответах.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="before-you-begin"></a>Перед началом работы

- Необходимо запустить Azure PowerShell локально для выполнения шагов в этой статье. Вы также должны иметь Az модуль версии 1.0.0 или более поздней версии. Запустите `Import-Module Az` и затем `Get-Module Az` Определение установленной версии. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). После проверки версии PowerShell выполните командлет `Login-AzAccount`, чтобы создать подключение к Azure.
- Необходимо иметь экземпляр SKU шлюза приложений версии 2. Переопределения заголовков не поддерживается в v1 SKU. Если у вас нет v2 SKU, создать [SKU шлюза приложений версии 2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) экземпляра перед началом работы.

## <a name="create-required-objects"></a>Создание необходимых объектов

Чтобы настроить перезапись заголовка HTTP, вам потребуется для выполнения этих действий.

1. Создайте объекты, которые требуются для переопределения заголовка HTTP:

   - **RequestHeaderConfiguration**: Используется для указания полей заголовка запроса, которые вы собираетесь переписать и новое значение для заголовков.

   - **ResponseHeaderConfiguration**: Используется для указания поля заголовка ответа, которые вы собираетесь переписать и новое значение для заголовков.

   - **ActionSet**: Содержит конфигурации заголовков запроса и ответа, указанных выше.

   - **Условие**. Дополнительная настройка. Перезапись условия оценки содержимого HTTP (S) запросов и ответов. Действие переопределения произойдет в том случае, если запрос HTTP (S) или ответ соответствует условию перезаписи.

     Если более одного условия связывается с действием, действие происходит только в том случае, если выполнены все условия. Другими словами операция остается логическую операцию и.

   - **RewriteRule**: Содержит несколько действий перезаписи / переписать сочетания условий.

   - **RuleSequence**: Выполнение дополнительной конфигурации, помогает определить порядок, в котором правила переопределения. Эта конфигурация полезно при наличии нескольких правила подстановки в наборе перезаписи. Правило переопределения, которое имеет меньшее значение последовательности правило выполняется первой. Если два правила подстановки назначается то же значение последовательности правило, порядок выполнения является недетерминированным.

     Если вы не укажете RuleSequence явным образом, устанавливается значение по умолчанию 100.

   - **RewriteRuleSet**: Содержит несколько правил переопределения, которые будут связаны в правило маршрутизации запроса.

2. Присоедините RewriteRuleSet правила маршрутизации. Параметры перезаписи, присоединяется к исходному прослушивателю посредством правила маршрутизации. При использовании базового правила маршрутизации, конфигурация переопределения заголовка связывается с исходным прослушивателем и перезапись глобального заголовка. При использовании правила маршрутизации на основе пути конфигурация переопределения заголовка задается для сопоставления URL-пути. В этом случае он применяется только к области конкретного пути узла.

Можно создать несколько наборов переопределения заголовка HTTP и применить каждой перезаписи присвоено несколько прослушивателей. Но вы можете применить только один переписать набора для конкретного прослушивателя.

## <a name="sign-in-to-azure"></a>Вход в Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Укажите конфигурацию правила переопределения заголовка HTTP

В этом примере мы изменим URL-адрес перенаправления, переписав под заголовком location в HTTP-ответа, каждый раз, когда заголовок location содержит ссылку на azurewebsites.net. Чтобы сделать это, мы добавим условие для оценки ли под заголовком location в ответе содержит azurewebsites.net. Мы будем использовать шаблон `(https?):\/\/.*azurewebsites\.net(.*)$`. И мы будем использовать `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` как значение заголовка. Это значение заменит *azurewebsites.net* с *contoso.com* в заголовке location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Получить конфигурацию шлюза приложений

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Получить конфигурацию правило маршрутизации запроса

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Примените к шлюзу приложений с конфигурацией для переопределения заголовков HTTP

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

Дополнительные сведения о том, как настроить наиболее распространенные варианты использования, см. в разделе [общая область переписывать сценарии](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).