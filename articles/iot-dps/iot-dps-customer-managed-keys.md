---
title: Шифрование данных службы обеспечения устройств Azure в состоянии покоя через ключи, управляемые клиентом Документы Майкрософт
description: Шифрование данных в состоянии покоя с помощью ключей, управляемых клиентом для службы обеспечения устройств
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77675147"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Шифрование данных в состоянии покоя с помощью ключей, управляемых клиентом для службы обеспечения устройств

## <a name="overview"></a>Обзор

Служба устройства (DPS) поддерживает шифрование данных в состоянии покоя с помощью ключей, управляемых клиентами (CMK), также известных как Bring your own key (BYOK). DPS обеспечивает шифрование данных в состоянии покоя и в пути. По умолчанию DPS использует управляемые корпорацией Майкрософт ключи для шифрования данных. При поддержке CMK клиенты теперь имеют возможность шифрования данных в состоянии покоя с помощью ключа шифрования, управляемого клиентами, используя [Azure Key Vault.](https://azure.microsoft.com/services/key-vault/)

Эта возможность требует создания нового DPS, в одном из следующих регионов: Восточная США, Западная США 2, или южная центральная часть США. Чтобы попробовать эту возможность, свяжитесь с нами через [службу поддержки Майкрософт](https://azure.microsoft.com/support/create-ticket/). Поделитесь именем компании и идентификатором подписки при обращении в службу поддержки Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия

* [Подробнее об услуге обеспечения устройств](https://docs.microsoft.com/azure/iot-dps/)

* [Узнайте больше о Убежище ключей Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)