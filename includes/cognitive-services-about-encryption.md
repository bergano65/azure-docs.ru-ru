---
title: включить файл
description: включить файл
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372175"
---
## <a name="about-cognitive-services-encryption"></a>О шифровании когнитивных служб

Данные шифруются и расшифровываются с помощью [256-битного](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) шифрования [AES FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Шифрование и расшифровка прозрачны, то есть шифрование и доступ управляются для вас. Ваши данные защищены по умолчанию, и вам не нужно изменять код или приложения, чтобы воспользоваться преимуществами шифрования.

## <a name="about-encryption-key-management"></a>Об управлении ключами шифрования

По умолчанию в вашей подписке используются ключи шифрования, управляемые корпорацией Майкрософт. Если вы используете уровень ценообразования, поддерживающий клавиши, управляемые клиентами, вы можете увидеть параметры шифрования для вашего ресурса в разделе **Шифрование** [портала Azure,](https://portal.azure.com)как показано на следующем изображении.

![Просмотр настроек шифрования](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

Для подписок, которые поддерживают только управляемые корпорацией Майкрософт ключи шифрования, у вас не будет раздела **шифрования.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Ключи, управляемые клиентами с помощью Azure Key Vault

Существует также возможность управлять подпиской с вашими собственными ключами. Управляемые клиентами ключи (CMK), также известные как Bring your own key (BYOK), обеспечивают большую гибкость для создания, вращения, отключания и отзыва элементов доступа. Вы также можете провести аудит ключей шифрования, используемых для защиты ваших данных.
