---
title: Настройка оповещений о событиях журнала диагностики из VPN-шлюза Azure
description: Этапы настройки оповещений о событиях журнала диагностики VPN-шлюза
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 48725ed8cdf3df30f8df31966aa632bfb2a4ef1f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066892"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Настраивать оповещения для событий журнала диагностики из VPN-шлюза

Эта статья поможет вам настроить оповещения на основе событий журнала диагностики из VPN-шлюза Azure. Доступны следующие журналы.

|***Имя*** | ***Описание*** |
|---        | ---               |
|GatewayDiagnosticLog | Содержит журналы диагностики для событий конфигурации шлюза, основные изменения и мероприятиях обслуживания |
|TunnelDiagnosticLog | Содержит события изменения состояния туннеля. Туннель подключение или отключение события имеют сводные причину для изменения состояния, если применимо |
|RouteDiagnosticLog | Журналы изменений статические маршруты и BGP событий, происходящих в шлюз |
|IKEDiagnosticLog | Журналы сообщений IKE элемента управления и события DHCP на шлюз |
|P2SDiagnosticLog | Записывает в журнал сообщения точка сеть управления и события DHCP на шлюз |

## <a name="setup"></a>Настройка оповещений

Следующий пример проходит создаст предупреждение для события отключения, который включает в себя сеть сеть VPN-туннеля:


1. На портале Azure, поиск **Log Analytics** под **все службы** и выберите **рабочих областей Log Analytics**.

   ![Выбранные параметры для перехода к рабочим областям Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "создать")

2. Выберите **создать** на **Log Analytics** страницы.

   ![Log Analytics страницы с помощью кнопки "Создать"](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "выберите")

3. Выберите **Create New** и введите необходимые сведения.

   ![Подробные сведения о создании рабочей области Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "выберите")

4. Найти VPN-шлюза на **монитор** > **параметры диагностики** колонке.

   ![Выбранные элементы для поиска VPN-шлюза в параметрах диагностики](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "выберите")

5. Чтобы включить диагностику, дважды щелкните шлюз, а затем выберите **включить диагностику**.

   ![Выбранные элементы для включения диагностики](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "выберите")

6. Введите необходимые сведения и убедитесь, что **отправить в Log Analytics** и **TunnelDiagnosticLog** выбраны. Выберите рабочую область Log Analytics, созданный на шаге 3.

   ![Флажки](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "выберите")

7. Перейдите к обзору для ресурса шлюза виртуальной сети и выберите **оповещения** из **мониторинг** вкладки. Затем создайте новое правило генерации оповещений, или изменить существующее правило генерации оповещений.

   ![Выбранные элементы для создания нового правила генерации оповещений](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "выберите")

   ![точка сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "выберите")
8. Выберите рабочую область Log Analytics и ресурса.

   ![Выбранные элементы для рабочей области и ресурсах](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "выберите")

9. Выберите **пользовательский поиск по журналам** как логики сигнала в разделе **добавить условие**.

   ![Выбранные элементы для поиска настраиваемого журнала](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "выберите")

10. Введите следующий запрос в текстовом поле **Поисковый запрос**. Замените значения в <> соответствующим образом.

     `AzureDiagnostics |
     where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
     remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"`

    Для порогового значения 0 и выбрать **сделать**.

    ![Введя запрос и выберите пороговое значение](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "выберите")

11. На **создать правило** выберите **Create New** под **группы ДЕЙСТВИЙ** раздел. Заполните сведения и нажмите кнопку **ОК**.

    ![Подробные сведения о новой группы действий](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "выберите")

12. На **создать правило** страницы «Заливка» в сведениях о **настроить действия** и убедитесь, что указано правильное имя отображается в **имя группы ДЕЙСТВИЙ** раздел. Выберите **создать правило генерации оповещений** для создания правила.

    ![Выбранные элементы для создания правила](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "выберите")

## <a name="next-steps"></a>Дальнейшие действия

Настройка оповещений о метриках туннель, см. в разделе [Настройка оповещений о метриках VPN-шлюз](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
