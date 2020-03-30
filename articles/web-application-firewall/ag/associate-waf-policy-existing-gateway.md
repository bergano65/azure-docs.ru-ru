---
title: Связать политику веб-приложений с существующим шлюзом приложений Azure
description: Узнайте, как связать политику веб-приложений Firewall с существующим шлюзом приложений Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083904"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Связать политику WAF с существующим шлюзом приложений

Вы можете использовать Azure PowerShell для [создания политики WAF,](create-waf-policy-ag.md)но, возможно, у вас уже есть шлюз приложений, и вы просто хотите связать с ним политику WAF. В этой статье вы делаете именно это; вы создаете политику WAF и связываете ее с уже существующим шлюзом приложений. 

1. Получите свой шлюз приложения и политику брандмауэра. Если у вас нет существующей политики брандмауэра, см. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. (Необязательно) Создайте политику брандмауэра.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Если вы создаете эту политику WAF для перехода от WAF Config к политике WAF, то политика должна быть точной копией вашего старого Config. Это означает, что каждое исключение, пользовательское правило, группа правил отключений и т.д. должны быть такими же, как и в waF Config.
3. (Необязательно) Вы можете настроить политику WAF в соответствии с вашими потребностями. Это включает в себя пользовательские правила, отключение правил / групп правил, исключения, установление ограничений загрузки файлов и т.д. Если вы пропустите этот шаг, все по умолчанию будут выбраны. 
   
4. Сохраните политику и прикрепите ее к шлюзу приложения. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Дальнейшие действия
[Узнайте о пользовательских правилах.](configure-waf-custom-rules.md)
