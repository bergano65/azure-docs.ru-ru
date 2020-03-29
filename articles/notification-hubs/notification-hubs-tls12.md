---
title: Уведомления концентраторов TLS обновления
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908531"
---
# <a name="transport-layer-security-tls"></a>Протокол TLS

Чтобы обеспечить более высокий уровень безопасности, центры уведомлений отстранят поддержку версий TLS 1.0 и 1.1 30 апреля 2020 года. Эти старые протоколы обеспечивают слабую криптографию и уязвимы для атак BEAST и POODLE. Приложения, развернутые на устройствах под управлением Android-версии 5 или больше, или iOS-версии 5 или больше, не влияют на это изменение, поскольку эти операционные системы поддерживают TLS 1.2, а клиент и сервер будут вести переговоры о наивысшей взаимно поддерживаемой версии протокол при подключении.

Мы рекомендуем просмотреть все приложения, которые используют концентраторы уведомлений Azure, чтобы убедиться, что они используют наиболее применимые библиотеки и tLS стеки, которые поддерживают TLS 1.2.

## <a name="update-apps"></a>Обновление приложений

Вы можете убедиться, что ваши приложения для iOS используют TLS 1.2 с помощью функции сетевой безопасности Apple под названием App Transport Security (ATS). ATS не может быть использован для SDKs старше iOS 9.0 или macOS 10.11, и вы можете прочитать об этом далее из [документации Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

Для приложений Android, использующих экземпляры SSLSocket, набор включенных протоколов на каждом экземпляре SSLSocket, как указано в [setEnabledProtocols.](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))

Таблица на странице поддержки [совместимости протоколов TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) помогает сопоставить операционные системы с совместимыми версиями TLS.

Для получения дополнительной информации смотрите обзор [поддержки протоколов TLS на Windows.](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)

## <a name="next-steps"></a>Дальнейшие действия

- [Обзор концентратов уведомлений](notification-hubs-push-notification-overview.md)