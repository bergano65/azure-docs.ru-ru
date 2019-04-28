---
title: Как настроить оповещения о событиях журнала диагностики VPN-шлюза Azure
description: Этапы настройки оповещений о событиях журнала диагностики VPN-шлюза
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 39a6d2e6201dd0635149159cb3727fd3c40f710a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769741"
---
# <a name="setting-up-alerts-on-vpn-gateway-diagnostic-log-events"></a>Настройка оповещений о событиях журнала диагностики VPN-шлюза

Эта статья поможет вам настроить оповещения на основе событий журнала диагностики VPN-шлюза.


## <a name="setup"></a>Настройка оповещений Azure Monitor, на основе событий журнала диагностики с помощью портала

Приведенные ниже примере создается предупреждение для события отключения туннеля VPN-сеть



1. В группе всех служб поиска «Log Analytics» и выберите «Рабочие области Log Analytics» ![точка сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "создать")

2. Нажмите кнопку «Создать» на странице Log Analytics.
![точка сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "выберите")

3. Проверьте рабочую область «Создать» и введите необходимые сведения.
![точка сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "выберите")

4. Найти VPN-шлюза в разделе «Монитор» > колонка «Параметры диагностики» ![точка сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "выберите")

5. Чтобы включить диагностику, дважды щелкните на шлюзе и выберите пункт «Включить диагностику» ![точка сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "выберите")

6. Введите необходимые сведения и убедитесь, что проверяются «Отправить в Log Analytics» и «TunnelDiagnosticLog». Выберите рабочую область Log Analytics, созданный на шаге 3.
![точка сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "выберите")

7. Перейти к Обзор ресурсов шлюза виртуальной сети и выберите «Предупреждения» на вкладке наблюдения, а затем создать новое правило генерации оповещений или изменить существующее правило генерации оповещений.
![точка сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "выберите")

8. Выберите рабочую область Log Analytics и ресурса.
![точка сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "выберите")

9. Выберите «пользовательский поиск по журналам» как логики сигнала в разделе «Добавить условие» ![точка сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "выберите")

10. Введите следующий запрос в текстовом поле «Поисковый запрос», заменив значения в <> соответствующим образом.

    AzureDiagnostics | где категории == «TunnelDiagnosticLog» и ResourceId == toupper («<RESOURCEID OF GATEWAY>") и TimeGenerated > ago(5m) и remoteIP_s =="<REMOTE IP OF TUNNEL>"и status_s == «Отключено»

    Пороговое значение равным 0 и нажмите кнопку «Готово»

    ![точка сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "выберите")

11. На странице «Создать правило» щелкните «Создать» в разделе "группы ДЕЙСТВИЙ". Введите необходимые сведения и нажмите кнопку ОК

![точка сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "выберите")

12. На странице «Создать правило» введите необходимые сведения для «Настройка действия» и убедитесь, что правильное имя группы действий отображается в разделе «Имя группы действий». Нажмите кнопку «Создать правило генерации оповещений» для создания правила.
![точка сеть](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "выберите")

## <a name="next-steps"></a>Дальнейшие действия

Настройка оповещений о метриках туннель, см. в разделе [Настройка оповещений о метриках VPN-шлюз](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
