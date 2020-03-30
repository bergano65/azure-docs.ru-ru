---
title: Шифрование данных Azure IoT Hub в состоянии покоя через ключи, управляемые клиентами Документы Майкрософт
description: Шифрование данных в состоянии покоя с помощью ключей, управляемых клиентами для IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370582"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Шифрование данных в состоянии покоя с помощью ключей, управляемых клиентами для IoT Hub

IoT Концентратор поддерживает шифрование данных в состоянии покоя с помощью ключей, управляемых клиентами (CMK), также известных как Bring your own key (BYOK), поддержка Azure IoT Hub. Azure IoT Hub обеспечивает шифрование данных в состоянии покоя и в пути. По умолчанию IoT Hub использует управляемые корпорацией Майкрософт ключи для шифрования данных. При поддержке CMK клиенты теперь имеют возможность шифрования данных в состоянии покоя с помощью ключа шифрования, управляемого клиентами, используя [Azure Key Vault.](https://azure.microsoft.com/services/key-vault/)

Эта возможность требует создания нового Концентратора IoT (базовый или стандартный уровень) в одном из следующих регионов: Восточная ЧАСТЬ США, Западная ЧАСТЬ США, 2, южная центральная часть США или правительство США. Чтобы попробовать эту возможность, свяжитесь с нами через [службу поддержки Майкрософт](https://azure.microsoft.com/support/create-ticket/). Поделитесь именем компании и идентификатором подписки при обращении в службу поддержки Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия

* [Узнайте больше о концентраторе IoT](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Узнайте больше о Убежище ключей Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
