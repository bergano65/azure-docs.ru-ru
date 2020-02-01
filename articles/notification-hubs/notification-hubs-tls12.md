---
title: Обновления TLS концентраторов уведомлений
description: Узнайте о поддержке TLS в центрах уведомлений Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87309e20efd9d6f8bd1a659451e5a603e6b95bc8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908531"
---
# <a name="transport-layer-security-tls"></a>Протокол TLS

Чтобы обеспечить более высокий уровень безопасности, концентраторы уведомлений отключают поддержку TLS версий 1,0 и 1,1 на 30 апреля 2020 г. Эти старые протоколы обеспечивают слабую криптографию и уязвимы для атак МОНСТРУ и пудель. Эти изменения не затрагивают приложения, развернутые на устройствах под управлением Android версии 5 или более поздней версии или iOS с версией 5 или более поздней, так как эти операционные системы поддерживают TLS 1,2, и клиент и сервер будут согласовывать наиболее взаимоподдерживаемую версию протокол при подключении.

Рекомендуется просматривать все приложения, использующие центры уведомлений Azure, чтобы убедиться, что они используют наиболее подходящие библиотеки и стеки TLS, поддерживающие TLS 1,2.

## <a name="update-apps"></a>Обновить приложения

Вы можете убедиться, что приложения iOS используют TLS 1,2 с помощью функции безопасности сети Apple, именуемой безопасностью транспорта приложений (ATS). ATS нельзя использовать для пакетов SDK старше iOS 9,0 или macOS 10,11. Дополнительные сведения см. в [документации Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

Для приложений Android, использующих экземпляры Сслсоккет, установите включенные протоколы для каждого экземпляра Сслсоккет, как указано в [сетенабледпротоколс](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

Таблица на странице Поддержка [совместимости протокола TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) помогает сопоставлять операционные системы с совместимыми версиями TLS.

Дополнительные сведения см. в обзоре [поддержки протоколов TLS в Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Дальнейшие действия

- [Концентраторы уведомлений Azure](notification-hubs-push-notification-overview.md)