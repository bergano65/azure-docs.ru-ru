---
title: Настройка оповещений на метриках Azure VPN Gateway
description: Шаги для настройки оповещений на метриках VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67605233"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Настройка оповещений на метриках VPN Gateway

Эта статья поможет настроить оповещения на метриках Azure VPN Gateway. Azure Monitor предоставляет возможность настраивать оповещения для ресурсов Azure. Вы можете настроить оповещения для виртуальных сетевых шлюзов типа "VPN".


|**Метрические**   | **Единица измерения** | **Степень детализации данных** | **Описание** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Байты/с  | 5 мин| Среднее комбинированное использование пропускной способности всех соединений сайта на шлюзе.     |
|**P2SBandwidth**| Байты/с  | 1 минута  | Среднее комбинированное использование пропускной способности всех соединений от точки к сайту на шлюзе.    |
|**P2SConnectionCount**| Count  | 1 минута  | Количество соединений от точки к сайту на шлюзе.   |
|**TunnelAverageBandwidth** | Байты/с    | 5 мин  | Средняя пропускная способность туннелей, созданных на шлюзе. |
|**TunnelEgressBytes** | Байты | 5 мин | Исходящие движения по туннелям, созданным на шлюзе.   |
|**TunnelEgressPackets** | Count | 5 мин | Количество исходящих пакетов на туннелях, созданных на шлюзе.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 мин | Количество исходящих пакетов, сбрасываемых на туннели, вызванное несоответствием селектора трафика. |
|**TunnelIngressBytes** | Байты | 5 мин | Входящий трафик по туннелям, созданным на шлюзе.   |
|**TunnelIngressPackets** | Count | 5 мин | Количество входящих пакетов на туннелях, созданных на шлюзе.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 мин | Количество входящих пакетов, сбрасываемых на туннели, вызванное несоответствием селектора трафика. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Настройка предупреждений Azure Monitor на основе метрик с помощью портала Azure

Следующие примеры будут создавать оповещение на шлюзе для:

- **Метрика:** TunnelAverageBandwidth
- **Состояние:** Пропускная способность > 10 байт в секунду
- **Окно:** 5 минут
- **Действия оповещения:** Отправить по электронной почте



1. Перейдите на ресурс виртуального сетевого шлюза и выберите **оповещения** из вкладки **Мониторинг.** Затем создайте новое правило оповещения или отодвините существующее правило оповещения.

   ![Выборы для создания правила оповещения](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Создание")

2. Выберите VPN шлюз в качестве ресурса.

   ![Кнопка «Выберите» и VPN шлюз в списке ресурсов](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Выберите пункт")

3. Выберите метрику для настройки для оповещения.

   ![Выбранная метрика в списке метрик](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Выберите пункт")
4. Наверстойная логика сигнала. Есть три компонента к нему:

    а. **Размеры**: Если метрика имеет размеры, можно выбрать определенные значения измерения, чтобы оповещение оценивало только данные этого измерения. Они не являются обязательными.

    b. **Условие**: Это операция для оценки метрики.

    c. **Время**: Укажите детализацию метрических данных и период времени для оценки оповещения.

   ![Детали для настройки логики сигнала](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Выберите пункт")

5. Для просмотра настроенных правил выберите **правила оповещения Управления.**

   ![Кнопка для управления правилами оповещения](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Выберите пункт")

## <a name="next-steps"></a>Дальнейшие действия

Для настройки оповещений в [Set up alerts on VPN Gateway diagnostic logs](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)диагностических журналах туннелей см.
