---
title: Включить имя файла
description: включить файл
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 3b975d9f0953c874c627e30b382ad8c7fa7cfc09
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84307837"
---
## <a name="about-cognitive-services-encryption"></a>Сведения о шифровании Cognitive Services

Данные шифруются и расшифровываются с помощью [fips 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) , совместимого с [256-БИТНЫМ](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) шифрованием AES. Шифрование и расшифровка прозрачны, то есть Управление шифрованием и доступом осуществляется за вас. Данные по умолчанию безопасны, и вам не нужно изменять код или приложения, чтобы воспользоваться преимуществами шифрования.

## <a name="about-encryption-key-management"></a>Сведения об управлении ключами шифрования

По умолчанию в подписке используются ключи шифрования, управляемые корпорацией Майкрософт. Однако вы также можете управлять подпиской с помощью собственных ключей шифрования. Ключи, управляемые клиентом (CMK), также известные как собственный ключ (BYOK), обеспечивают большую гибкость при создании, повороте, отключении и отмене контроля доступа. Кроме того, можно выполнять аудит ключей шифрования, используемых для защиты данных.