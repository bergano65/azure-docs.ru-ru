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
ms.openlocfilehash: 90d5aae559a317ed509d04c8db3310ff8a5de026
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89069922"
---
## <a name="about-cognitive-services-encryption"></a>Сведения о шифровании Cognitive Services

Данные шифруются и расшифровываются с помощью [fips 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) , совместимого с [256-БИТНЫМ](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) шифрованием AES. Шифрование и расшифровка прозрачны, то есть Управление шифрованием и доступом осуществляется за вас. Данные безопасны по умолчанию, и вам не нужно изменять код или приложения, чтобы воспользоваться преимуществами шифрования.

## <a name="about-encryption-key-management"></a>Об управлении ключами шифрования

По умолчанию в подписке используются ключи шифрования, управляемые корпорацией Майкрософт. Кроме того, существует возможность управления подпиской с помощью собственных ключей, которые называются ключами, управляемыми клиентом (CMK). CMK обеспечивают большую гибкость при создании, повороте, отключении и отмене контроля доступа. Они также дают возможность выполнять аудит ключей шифрования, используемых для защиты ваших данных. Если для вашей подписки настроен CMK, то предоставляется двойное шифрование, которое обеспечивает второй уровень защиты, позволяя управлять ключом шифрования с помощью Azure Key Vault.