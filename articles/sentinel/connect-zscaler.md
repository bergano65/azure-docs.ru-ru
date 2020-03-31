---
title: Подключите данные «Scaler» к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные «Scaler» к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587997"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Подключите доступ к Интернету в Azure Sentinel

> [!IMPORTANT]
> Разъем данных sscaler в Azure Sentinel в настоящее время находится в открытом доступе.
> Эта функция предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этой статье объясняется, как подключить ваше приложение доступа к Интернету с Azure Sentinel. Разъем данных «Scaler» позволяет легко подключать журналы доступа в Интернет с помощью Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследования. Использование sscaler на Azure Sentinel предоставит вам более подробную информацию об использовании Интернета в вашей организации и повысит возможности ее работы в области безопасности. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Напередите свой «Масштабр» для отправки сообщений CEF

1. На приборе «Шкалар» необходимо установить эти значения таким образом, чтобы прибор отправлял необходимые журналы в необходимом формате агенту Azure Sentinel Syslog на основе агента Log Analytics. Эти параметры можно изменять в приборе, если вы также измените их в daemon Syslog на агенте Azure Sentinel.
    - Протокол - TCP
    - Порт No 514
    - Формат - CEF
    - IP-адрес - не забудьте отправить сообщения CEF на IP-адрес виртуальной машины, которую вы посвятили для этой цели.
 Для получения дополнительной информации ознакомьтесь с [Руководством по развертыванию sscaler и Azure Sentinel.](https://aka.ms/ZscalerCEFInstructions)
 
   > [!NOTE]
   > Это решение поддерживает Syslog RFC 3164 или RFC 5424.


1. Чтобы использовать соответствующую схему в журнале Analytics для `CommonSecurityLog`событий CEF, ищите .
1. Продолжить [STEP 3: Проверка подключения](connect-cef-verify.md).


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить доступ к Интернету с Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.


