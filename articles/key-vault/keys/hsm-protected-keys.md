---
title: Создание ключей, защищенных с АППАРАТным модулем безопасности & — Azure Key Vault
description: Данная статья поможет вам подготовить и создать ваши собственные ключи, защищенные аппаратным модулем безопасности, а затем передать их в хранилище ключей Azure. Также известные как собственные ключи или BYOK.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: ce59fadfcdbb38327ce603197400e9317202d651
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87061090"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Импорт защищенных модулем HSM ключей в Key Vault

Чтобы обеспечить более высокий уровень защиты при работе с хранилищем ключей Azure, можно импортировать ключи или создать их в аппаратных модулях безопасности (ключи никогда не покидают их пределы). Такой сценарий с использованием собственного ключа часто называется *BYOK*. Azure Key Vault использует семейство nCipher nShield HSM (проверенный FIPS 140-2 уровня 2) для защиты ключей.

Эта функция недоступна для Azure для Китая в Китае.

> [!NOTE]
> Дополнительные сведения о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](../general/overview.md)  
> Указания по началу работы, включая создание хранилища для ключей, защищенных модулем HSM, см. в [этой статье](../general/overview.md).

## <a name="supported-hsms"></a>Поддерживаемые HSM

Передача ключей, защищенных АППАРАТным модулем безопасности, в Key Vault поддерживается с помощью двух различных методов в зависимости от используемого HSM. Используйте приведенную ниже таблицу, чтобы определить, какой метод следует использовать для создания HSM, а затем перенесите собственные ключи с защитой HSM для использования с Azure Key Vault. 

|Имя поставщика|Тип поставщика|Поддерживаемые модели HSM|Поддерживаемый метод обмена ключами HSM|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Производителя<br/>HSM как услуга|<ul><li>Семейство HSM nShield</li><li>nShield как услуга</ul>|**Метод 1.** [nCipher BYOK](hsm-protected-keys-ncipher.md) (с надежной аттестацией для импорта ключей и проверки HSM)<br/>**Метод 2.** [использование нового метода BYOK](hsm-protected-keys-byok.md) |
|Thales|Изготовитель|<ul><li>Семейство HSM 7 Luna с встроенным по версии 7,3 или более поздней</li></ul>| [Использовать новый метод BYOK](hsm-protected-keys-byok.md)|
|фортаникс|Производителя<br/>HSM как услуга|<ul><li>Служба управления ключами для самостоятельной защиты (СДКМС)</li><li>Equinix Смарткэй</li></ul>|[Использовать новый метод BYOK](hsm-protected-keys-byok.md)|
|Драйвер|Изготовитель|Все Ликуидсекурити HSM с<ul><li>Версия встроенного по 2.0.4 или более поздняя</li><li>Встроенное по версии 3,2 или более поздней</li></ul>|[Использовать новый метод BYOK](hsm-protected-keys-byok.md)|
|криптомасик|ISV (система управления ключами предприятия)|Несколько торговых марок и моделей HSM, включая<ul><li>nCipher</li><li>Thales</li><li>утимако</li></ul>Дополнительные [сведения см. на сайте криптомасик](https://www.cryptomathic.com/azurebyok)|[Использовать новый метод BYOK](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>Дальнейшие действия

* Следуйте [Key Vault](../general/best-practices.md) рекомендациям по обеспечению безопасности, устойчивости и мониторинга ключей.
* Полное описание нового метода BYOK см. в [спецификации BYOK](https://docs.microsoft.com/azure/key-vault/keys/byok-specification) .
