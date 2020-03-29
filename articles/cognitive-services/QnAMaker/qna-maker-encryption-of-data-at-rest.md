---
title: Шифрование данных в состоянии покоя
titleSuffix: Azure Cognitive Services
description: Шифрование данных в состоянии покоя.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372098"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>Шифрование данных в состоянии покоя

Создатель nA автоматически шифрует ваши данные, когда они сохраняются в облаке, помогая достичь ваших целей организационной безопасности и соответствия требованиям.

## <a name="about-encryption-key-management"></a>Об управлении ключами шифрования

По умолчанию в вашей подписке используются ключи шифрования, управляемые корпорацией Майкрософт. Существует также возможность управлять подпиской с вашими собственными ключами. Управляемые клиентами ключи (CMK) обеспечивают большую гибкость для создания, вращения, отключания и отзыва элементов управления доступом. Вы также можете провести аудит ключей шифрования, используемых для защиты ваших данных.

Создатель CMK использует поддержку CMK из поиска Azure. Необходимо создать [CMK в azure Search с помощью Azure Key Vault.](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys) Этот экземпляр Azure должен быть связан с службой создателя NA, чтобы сделать его включенным CMK.

> [!IMPORTANT]
> Ресурс службы поиска Azure должен быть создан после января 2019 года и не может находиться в свободном (общем) уровне. На портале Azure отсутствует поддержка настройки ключей, управляемых клиентами.

## <a name="enable-customer-managed-keys"></a>Включить ключи, управляемые клиентом

Служба «Создатель» использует CMK из службы поиска Azure. Выполните следующие действия, чтобы включить CMK:

1. Создайте новый экземпляр поиска Azure и включите предпосылки, указанные в [ключевых предпосылках, управляемых клиентом, для когнитивного поиска Azure.](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)

   ![Просмотр настроек шифрования](../media/cognitive-services-encryption/qna-encryption-1.png)

2. При создании ресурса «NA Maker» он автоматически связан с экземпляром поиска Azure. Это не может быть использовано с CMK. Чтобы использовать CMK, необходимо связать недавно созданный экземпляр поиска Azure, который был создан в шаге 1. В частности, вам нужно `AzureSearchAdminKey` `AzureSearchName` будет обновить и в вашем ресурсе nA Maker.

   ![Просмотр настроек шифрования](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Затем создайте новую настройку приложения:
   * **Имя**: Установите это`CustomerManagedEncryptionKeyUrl`
   * **Значение**: Это значение, которое вы получили в шаге 1 при создании экземпляра поиска Azure.

   ![Просмотр настроек шифрования](../media/cognitive-services-encryption/qna-encryption-3.png)

4. После завершения перезапуска времени выполнения. Теперь ваша услуга «NA Maker» включена в CMK.

## <a name="regional-availability"></a>Доступность по регионам

Ключи, управляемые клиентами, доступны во всех регионах поиска Azure.

## <a name="next-steps"></a>Дальнейшие действия

* [Шифрование в поиске Azure с помощью CMK в Хранилище ключей Azure](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Шифрование данных в состоянии покоя](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Узнайте больше о Убежище ключей Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
