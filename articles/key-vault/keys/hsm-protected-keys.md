---
title: Создание ключей, защищенных аппаратным модулем безопасности, и их передача в Azure Key Vault | Документация Майкрософт
description: Данная статья поможет вам подготовить и создать ваши собственные ключи, защищенные аппаратным модулем безопасности, а затем передать их в хранилище ключей Azure. Также известные как собственные ключи или BYOK.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 58cf3358a9e908070ce9003d05dd0b576b1d2d3f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429698"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Импортные HSM-защищенные ключи к Key Vault

Чтобы обеспечить более высокий уровень защиты при работе с хранилищем ключей Azure, можно импортировать ключи или создать их в аппаратных модулях безопасности (ключи никогда не покидают их пределы). Сценарий с использованием собственного ключа клиента называется *BYOK*. Azure Key Vault использует семейство HSMs nCipher nShield (FIPS 140-2 Уровень 2 проверено) для защиты ваших ключей.

Эта функциональность недоступна для Azure China 21Vianet.

> [!NOTE]
> Для получения дополнительной информации о [What is Azure Key Vault?](../general/overview.md) Убежище ключей Azure см.  
> Указания по началу работы, включая создание хранилища для ключей, защищенных модулем HSM, см. в [этой статье](../general/overview.md).

## <a name="supported-hsms"></a>Поддерживаемые ГСМ

Передача защищенных HSM ключей в Key Vault поддерживается двумя различными методами в зависимости от используемых HSM. Используйте таблицу ниже, чтобы определить, какой метод следует использовать для генерации HSMs, а затем перенесите свои собственные hSM-защищенные ключи для использования с Azure Key Vault. 

|Имя поставщика|Тип поставщика|Поддерживаемые модели HSM|Поддерживаемый метод передачи HSM-ключ|
|---|---|---|---|
|nCipher|Производитель|<ul><li>nShield семейство HSMs</li></ul>|[Используйте устаревший метод BYOK](hsm-protected-keys-legacy.md)|
|Thales|Производитель|<ul><li>SafeNet Luna HSM 7 семейство с прошивкой версия 7.3 или более новая</li></ul>| [Используйте новый метод BYOK (предварительный просмотр)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Фортаникс|HSM как услуга|<ul><li>Служба самозащиты ключей управления (SDKMS)</li></ul>|[Используйте новый метод BYOK (предварительный просмотр)](hsm-protected-keys-vendor-agnostic-byok.md)|


## <a name="next-steps"></a>Дальнейшие действия

Следуйте [рекомендациям Key Vault,](../general/best-practices.md) чтобы обеспечить безопасность, долговечность и мониторинг ключей.
