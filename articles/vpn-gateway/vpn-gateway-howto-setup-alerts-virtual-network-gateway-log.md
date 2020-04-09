---
title: 'Azure VPN Gateway: Настроить оповещения о событиях диагностического журнала'
description: Шаги для настройки оповещений на диагностических событиях журнала VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 49510b26e0b2a9c69dd65faf0f343e86d1a068db
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878907"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Настройка оповещений о событиях диагностического журнала с VPN Gateway

Эта статья поможет настроить оповещения на основе диагностических событий журнала из Azure VPN Gateway с помощью Azure Log Analytics. 

Следующие журналы доступны в Azure:

|***имя***; | ***Описание*** |
|---        | ---               |
|GatewayDiagnosticLog | Содержит диагностические журналы для событий конфигурации шлюза, первичных изменений и событий обслуживания |
|TunnelDiagnosticLog | Содержит события изменения состояния туннеля. События подключения/отключения туннеля имеют обобщенные причины изменения состояния, если это применимо |
|RouteDiagnosticLog | Изменения журналов в статических маршрутах и событиях BGP, которые происходят на шлюзе |
|IKEDiagnosticLog | Логи IKE контролируют сообщения и события на шлюзе |
|P2SDiagnosticLog | Входы в диспетчерские сообщения и события от точки к месту на шлюзе. Информация об источнике соединения предоставляется только для подключений IKEv2 |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Настройка оповещений на портале Azure

Следующие примеры создадут оповещение для события отключения, которое включает VPN-туннель от сайта к сайту:


1. На портале Azure ищите **аналитику журналов** во **всех службах** и выберите **рабочие области log Analytics.**

   ![Выбор для перехода к рабочим областям log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Создание")

2. Выберите **Создать** на странице **аналитики журнала.**

   ![Страница аналитики журнала с кнопкой «Создание»](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Выберите пункт")

3. Выберите **Создать новые** и заполнить детали.

   ![Подробная информация для создания рабочего пространства анализа журналов](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Выберите пункт")

4. Найдите свой VPN **Monitor** > шлюз на**лезвии настроек Monitor Diagnostics.**

   ![Выбор для поиска VPN шлюза в настройках диагностики](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Выберите пункт")

5. Чтобы включить диагностику, дважды щелкните шлюз, а затем выберите **Включить диагностику.**

   ![Выбор для включения диагностики](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Выберите пункт")

6. Заполните сведения и убедитесь, что **отправка в Log Analytics** и **TunnelDiagnosticLog** выбраны. Выберите рабочее пространство для аналитики журналов, созданное в шаге 3.

   ![Выбранные флажки](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Выберите пункт")

   > [!NOTE]
   > На первоначальном показе данных может потребоваться несколько часов.

7. Перейдите к обзору для виртуального сетевого шлюза и выберите **оповещения** из вкладки **Мониторинг.** Затем создайте новое правило оповещения или отодвините существующее правило оповещения.

   ![Выборы для создания нового правила оповещения](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Выберите пункт")

   ![от точки к месту](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Выберите пункт")
8. Выберите рабочее пространство журнала Analytics и ресурс.

   ![Выбор ы для рабочего пространства и ресурса](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Выберите пункт")

9. Выберите **пользовательский поиск журнала** в качестве логики сигнала в **условии добавить.**

   ![Выборы для пользовательского поиска журналов](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Выберите пункт")

10. Введите следующий запрос в текстовом поле **Поисковый запрос**. Замените значения в <> и TimeGenerated по мере необходимости.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Установите значение порога до 0 и выберите **Done**.

    ![Ввод запроса и выбор порога](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Выберите пункт")

11. На странице **правила «Создание»** выберите **«Создайте новое»** в разделе **ACTION GROUPS.** Заполните детали и выберите **OK**.

    ![Подробная информация о новой группе действий](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Выберите пункт")

12. На странице **правила «Создать»** заполните детали для **настраиваемых действий** и убедитесь, что правильное имя появится в разделе **ACTION GROUP NAME.** Для создания правила выберите **правило «Создание оповещения».**

    ![Выбор для создания правила](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Выберите пункт")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Настройка оповещений с помощью PowerShell

Следующие примеры создают оповещение для события отключения, которое включает VPN-туннель от сайта к сайту.

1. Создание рабочего пространства анализа журналов:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Включите диагностику для VPN шлюза:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Создайте группу действий.

   Этот код создает группу действий, которая отправляет уведомление по электронной почте при срабатывании оповещения:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Создайте правило оповещения на основе пользовательского поиска журналов:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Дальнейшие действия

Для настройки оповещений по показателям туннелей [см.](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)
