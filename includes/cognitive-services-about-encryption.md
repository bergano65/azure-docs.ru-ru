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
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372175"
---
## <a name="about-cognitive-services-encryption"></a>Сведения о шифровании Cognitive Services

Данные шифруются и расшифровываются с помощью [fips 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) , совместимого с [256-БИТНЫМ](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) шифрованием AES. Шифрование и расшифровка прозрачны, то есть Управление шифрованием и доступом осуществляется за вас. Данные по умолчанию безопасны, и вам не нужно изменять код или приложения, чтобы воспользоваться преимуществами шифрования.

## <a name="about-encryption-key-management"></a>Сведения об управлении ключами шифрования

По умолчанию в подписке используются ключи шифрования, управляемые корпорацией Майкрософт. Если вы используете ценовую категорию, поддерживающую управляемые клиентом ключи, вы можете увидеть параметры шифрования для ресурса в разделе " **Шифрование** " [портал Azure](https://portal.azure.com), как показано на следующем рисунке.

![Просмотр параметров шифрования](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

Для подписок, которые поддерживают только ключи шифрования, управляемые корпорацией Майкрософт, раздел **ENCRYPTION** будет отсутствовать.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Ключи, управляемые клиентом, с Azure Key Vault

Существует также возможность управлять подпиской с помощью собственных ключей. Ключи, управляемые клиентом (CMK), также известные как собственный ключ (BYOK), обеспечивают большую гибкость при создании, повороте, отключении и отмене контроля доступа. Кроме того, можно выполнять аудит ключей шифрования, используемых для защиты данных.
