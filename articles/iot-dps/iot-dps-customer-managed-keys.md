---
title: Шифрование данных в службе подготовки устройств Azure с помощью ключей, управляемых клиентом | Документация Майкрософт
description: Шифрование неактивных данных с помощью управляемых клиентом ключей для службы подготовки устройств
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77675147"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Шифрование неактивных данных с помощью управляемых клиентом ключей для службы подготовки устройств

## <a name="overview"></a>Обзор

Служба подготовки устройств (DPS) поддерживает шифрование неактивных данных с помощью управляемых клиентом ключей (CMK), также называемого собственным ключом (BYOK). DPS обеспечивает шифрование неактивных и транзитных данных. По умолчанию служба DPS использует ключи, управляемые корпорацией Майкрософт, для шифрования данных. Благодаря поддержке CMK клиенты теперь могут шифровать неактивных данных с помощью ключа шифрования ключа, управляемого клиентами, используя [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Эта возможность требует создания новой политики DPS в одном из следующих регионов: Восточная часть США, Западная часть США 2 или Юго-Центральный регион США. Чтобы испытать эту возможность, свяжитесь с нами через [службу поддержки Майкрософт](https://azure.microsoft.com/support/create-ticket/). Поделитесь своим названием организации и ИДЕНТИФИКАТОРом подписки при обращении в службу поддержки Майкрософт.

## <a name="next-steps"></a>Следующие шаги

* [Дополнительные сведения о службе подготовки устройств](https://docs.microsoft.com/azure/iot-dps/)

* [Дополнительные сведения о Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)