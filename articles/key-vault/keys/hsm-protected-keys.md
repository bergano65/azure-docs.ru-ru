---
title: Создание ключей, защищенных модулем HSM, и их передача — Azure Key Vault
description: Узнайте, как подготовить и создать собственные ключи, защищенные модулем HSM, а затем передать их в Azure Key Vault. Также известные как собственные ключи или BYOK.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 33249da6772d146de3e5e7351bc82c203674ddff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532143"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Импорт защищенных модулем HSM ключей в Key Vault

Чтобы обеспечить более высокий уровень защиты при работе с хранилищем ключей Azure, можно импортировать ключи или создать их в аппаратных модулях безопасности (ключи никогда не покидают их пределы). Такой сценарий с использованием собственного ключа часто называется *BYOK*. Для защиты ключей Azure Key Vault использует семейство модулей HSM nCipher nShield (проверенных по стандарту FIPS 140-2 уровня 2).

Эта функция недоступна в Azure для Китая (21Vianet).

> [!NOTE]
> Дополнительные сведения о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](../general/overview.md)  
> Указания по началу работы, включая создание хранилища для ключей, защищенных модулем HSM, см. в [этой статье](../general/overview.md).

## <a name="supported-hsms"></a>Поддерживаемые модули HSM

В зависимости от используемого HSM поддерживается два различных метода передачи ключей, защищенных модулем HSM, в Key Vault. Используйте приведенную ниже таблицу, чтобы определить, какой метод создания следует применять в модулях HSM, а затем перенесите собственные ключи, защищенные модулем HSM, для использования с Azure Key Vault. 

|Имя поставщика|Тип поставщика|Поддерживаемые модели модуля HSM|Поддерживаемый метод переноса ключа HSM|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Производитель,<br/>HSM как услуга|<ul><li>Семейство модулей HSM nShield</li><li>nShield как услуга</ul>|**Метод 1.** [BYOK nCipher](hsm-protected-keys-ncipher.md) (с надежной аттестацией для импорта ключей и проверкой HSM)<br/>**Метод 2.** [Использование нового метода BYOK](hsm-protected-keys-byok.md) |
|Thales|Изготовитель|<ul><li>Семейство HSM 7 Luna со встроенным ПО версии 7.3 или более поздней</li></ul>| [Использование нового метода BYOK](hsm-protected-keys-byok.md)|
|Fortanix|Производитель,<br/>HSM как услуга|<ul><li>Служба управления ключами для самостоятельной защиты (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Использование нового метода BYOK](hsm-protected-keys-byok.md)|
|Marvell|Изготовитель|Все модули HSM LiquidSecurity со<ul><li>встроенным ПО версии 2.0.4 или более поздней</li><li>встроенным ПО версии 3.2 или более новой</li></ul>|[Использование нового метода BYOK](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Enterprise Key Management System)|Несколько торговых марок и моделей HSM, в том числе<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Дополнительные сведения см. на [сайте Cryptomathic](https://www.cryptomathic.com/azurebyok).|[Использование нового метода BYOK](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>Дальнейшие действия

* Следуйте [рекомендациям Key Vault](../general/best-practices.md) для обеспечения безопасности, устойчивости и мониторинга ключей.
* Полное описание нового метода BYOK см. в [спецификации BYOK](https://docs.microsoft.com/azure/key-vault/keys/byok-specification).
