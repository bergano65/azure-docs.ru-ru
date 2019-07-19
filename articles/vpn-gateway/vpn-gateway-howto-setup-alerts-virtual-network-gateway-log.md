---
title: Настройка оповещений о событиях журнала диагностики из VPN-шлюза Azure
description: Действия по настройке оповещений для событий журнала диагностики VPN-шлюза
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: c84d457c51f71bdf315bbbcec674ff1186dd905f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249015"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Настройка оповещений о событиях журнала диагностики из VPN-шлюза

Эта статья поможет вам настроить оповещения на основе событий журнала диагностики из VPN-шлюза Azure с помощью Azure Log Analytics. 

В Azure доступны следующие журналы:

|***Name*** | ***Описание*** |
|---        | ---               |
|GatewayDiagnosticLog | Содержит журналы диагностики для событий настройки шлюза, основные изменения и события обслуживания. |
|TunnelDiagnosticLog | Содержит события изменения состояния туннеля. События подключения и отключения туннеля имеют обобщенную причину изменения состояния, если применимо |
|RouteDiagnosticLog | Регистрирует изменения статических маршрутов и событий BGP, происходящих в шлюзе. |
|IKEDiagnosticLog | Регистрирует сообщения управления IKE и события в шлюзе |
|P2SDiagnosticLog | Регистрирует сообщения управления точка-сеть и события на шлюзе. |

## <a name="setup"></a>Настройка оповещений

В следующих примерах шагов будет создано оповещение для события отключения, включающего VPN типа "сеть — сеть".


1. В портал Azure найдите **log Analytics** в разделе **все службы** и выберите **log Analytics рабочие области**.

   ![Параметры для перехода в log Analytics рабочие области](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Создание")

2. На странице **log Analytics** выберите **создать** .

   ![Страница log Analytics с кнопкой "создать"](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Выберите")

3. Выберите **создать** и введите сведения.

   ![Сведения о создании рабочей области log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Выберите")

4. Найдите VPN-шлюз в колонке **мониторинг** > **параметров диагностики** .

   ![Варианты поиска VPN-шлюза в параметрах диагностики](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Выберите")

5. Чтобы включить диагностику, дважды щелкните шлюз, а затем выберите **включить диагностику**.

   ![Параметры для включения диагностики](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Выберите")

6. Введите сведения и убедитесь, что выбран параметр **Send to log Analytics** и **туннелдиагностиклог** . Выберите рабочую область Log Analytics, созданную на шаге 3.

   ![Выбранные флажки](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Выберите")

7. Перейдите к обзору ресурса шлюза виртуальной сети и выберите **оповещения** на вкладке **мониторинг** . Затем создайте новое правило генерации оповещений или измените существующее правило генерации оповещений.

   ![Варианты создания нового правила генерации оповещений](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Выберите")

   ![точка-сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Выберите")
8. Выберите рабочую область Log Analytics и ресурс.

   ![Параметры для рабочей области и ресурса](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Выберите")

9. Выберите **Пользовательский поиск** по журналу в качестве логики сигнала в разделе **Добавить условие**.

   ![Элементы, выделенные для пользовательского поиска по журналам](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Выберите")

10. Введите следующий запрос в текстовом поле **Поисковый запрос**. Замените значения в < > соответствующим образом.

    ```
    AzureDiagnostics |
      where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
      remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"
    ```

    Установите пороговое значение 0 и нажмите кнопку **Готово**.

    ![Ввод запроса и выбор порогового значения](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Выберите")

11. На странице **Создание правила** выберите **создать** в разделе **группы действий** . Введите сведения и нажмите кнопку **ОК**.

    ![Сведения о новой группе действий](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Выберите")

12. На странице **Создание правила** введите сведения о **действиях настройки** и убедитесь, что в разделе **имя группы действий** отображается правильное имя. Выберите **создать правило генерации оповещений** , чтобы создать правило.

    ![Параметры для создания правила](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Выберите")

## <a name="next-steps"></a>Следующие шаги

Сведения о настройке оповещений в метриках туннеля см. в разделе [Настройка оповещений в метриках VPN-шлюза](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
