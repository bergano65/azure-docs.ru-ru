---
title: QnA Maker шифрование неактивных данных
titleSuffix: Azure Cognitive Services
description: QnA Maker шифрование неактивных данных.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 99c21ece202f8d9867045d506574dd7718bd455e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653664"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker шифрование неактивных данных

QnA Maker автоматически шифрует данные при сохранении в облаке, помогая обеспечить безопасность и соответствие требованиям Организации.

## <a name="about-encryption-key-management"></a>Сведения об управлении ключами шифрования

По умолчанию в подписке используются ключи шифрования, управляемые корпорацией Майкрософт. Существует также возможность управлять подпиской с помощью собственных ключей. Ключи, управляемые клиентом (CMK), обеспечивают большую гибкость при создании, повороте, отключении и отмене контроля доступа. Кроме того, можно выполнять аудит ключей шифрования, используемых для защиты данных.

QnA Maker использует поддержку CMK из службы поиска Azure. Вам нужно создать [CMK в поиске Azure с помощью Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Этот экземпляр Azure должен быть связан со службой QnA Maker, чтобы сделать ее CMK включенной.

> [!IMPORTANT]
> Ресурс службы поиска Azure должен быть создан после января 2019 и не может быть в бесплатном (общем) уровне. В портал Azure не поддерживается настройка ключей, управляемых клиентом.

## <a name="enable-customer-managed-keys"></a>Включить управляемые клиентом ключи

Служба QnA Maker использует CMK из службы поиска Azure. Чтобы включить ключей CMK, выполните следующие действия.

1. Создайте новый экземпляр службы поиска Azure и включите необходимые компоненты, указанные в [разделе Предварительные требования к управляемым клиентом ключам для Azure когнитивный Поиск](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Просмотр параметров шифрования](../media/cognitive-services-encryption/qna-encryption-1.png)

2. При создании QnA Maker ресурса он автоматически связывается с экземпляром службы поиска Azure. Этот параметр нельзя использовать с CMK. Чтобы использовать CMK, необходимо связать только что созданный экземпляр поиска Azure, созданный на шаге 1. В частности, необходимо обновить `AzureSearchAdminKey` и `AzureSearchName` в ресурсе QnA Maker.

   ![Просмотр параметров шифрования](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Затем создайте новый параметр приложения:
   * **Имя**: задайте значение.`CustomerManagedEncryptionKeyUrl`
   * **Значение**: это значение, полученное на шаге 1 при создании экземпляра службы поиска Azure.

   ![Просмотр параметров шифрования](../media/cognitive-services-encryption/qna-encryption-3.png)

4. По завершении перезапустите среду выполнения. Теперь служба QnA Maker включена CMK.

## <a name="regional-availability"></a>Доступность по регионам

Ключи, управляемые клиентом, доступны во всех регионах поиска Azure.

## <a name="encryption-of-data-in-transit"></a>Шифрование передаваемых данных

QnA Maker портал работает в браузере пользователя. Каждое действие активирует прямой вызов соответствующего API-интерфейса службы. Таким образом, QnA Maker соответствует данным при передаче.
Однако, поскольку служба QnA Maker Portal размещена в западной части США, она по-прежнему не идеально подходит для клиентов, не являющихся клиентами США. 

## <a name="next-steps"></a>Следующие шаги

* [Шифрование в поиске Azure с помощью ключей CMK в Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Шифрование неактивных данных](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Дополнительные сведения о Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
