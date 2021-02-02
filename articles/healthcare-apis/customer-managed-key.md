---
title: Настройка ключей, управляемых клиентом, для Azure API для FHIR
description: Поддержка функции создания собственного ключа в Azure API для FHIR с помощью Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: ae78aa80594e46b02d77adcafed961e801780d4f
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430265"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Настройка хранимых ключей, управляемых клиентом

При создании учетной записи Azure API для FHIR данные шифруются по умолчанию с помощью ключей, управляемых Майкрософт. Теперь можно добавить второй уровень шифрования данных, используя собственный ключ, который вы выбираете и которым управляете.

В Azure это обычно выполняется с помощью ключа шифрования в Azure Key Vault клиента. SQL Azure, служба хранилища Azure и Cosmos DB — это несколько примеров, предоставляющих эти возможности уже сегодня. Azure API для FHIR использует эту поддержку через Cosmos DB. При создании учетной записи у вас будет возможность указать универсальный код ресурса (URI) ключа Azure Key Vault. Этот ключ будет передан Cosmos DB при подготовке учетной записи базы данных. Когда выполняется запрос FHIR, Cosmos DB получает ключ и использует его для шифрования или расшифровки данных. Чтобы приступить к работе, воспользуйтесь следующими ссылками:

- [Регистрация поставщика ресурсов Azure Cosmos DB в своей подписке Azure](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Настройка экземпляра Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Добавление политики доступа в экземпляр Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Создание нового ключа в Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="specify-the-azure-key-vault-key"></a>Указание ключа Azure Key Vault

При создании на портале Azure учетной записи Azure API для FHIR в разделе "Параметры базы данных" на вкладке "Дополнительные параметры" можно увидеть параметр конфигурации "Шифрование данных". По умолчанию выбран параметр "Ключ, управляемый службой". 

Вы также можете выбрать свой ключ из KeyPicker:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="KeyPicker":::

Или вы можете указать ключ Azure Key Vault, выбрав параметр "Ключ, управляемый клиентом". Здесь можно ввести URI ключа:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Создание Azure API для FHIR":::

Для имеющихся учетных записей FHIR можно просмотреть вариант шифрования ключей (ключ, управляемый службой или клиентом) в колонке "База данных", как показано ниже. Параметр конфигурации нельзя изменить после выбора. Однако можно изменить и обновить ключ.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="База данных":::

Кроме того, можно создать версию указанного ключа, после чего данные будут зашифрованы с помощью новой версии без прерывания работы служб. Вы также можете удалить доступ к ключу, чтобы удалить доступ к данным. Если ключ отключен, запросы будут вызывать ошибку. Если ключ снова включен, запросы будут выполняться успешно.

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как настроить хранимые ключи, управляемые клиентом. Далее вы можете изучить раздел часто задаваемых вопросов об Azure Cosmos DB: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: настройка ключей, управляемых клиентом](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#frequently-asked-questions)
