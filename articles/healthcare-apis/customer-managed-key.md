---
title: Настройка ключей, управляемых клиентом, для Azure API для FHIR
description: Поддержка функции создания собственного ключа в Azure API для FHIR с помощью Cosmos DB
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 535bb5d21beafaabb50769a6c03478dbd1f942d4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92051344"
---
# <a name="configure-customer-managed-keys"></a>Настройка ключей, управляемых клиентом

При создании учетной записи Azure API для FHIR данные шифруются по умолчанию с помощью ключей, управляемых Майкрософт. Теперь можно добавить второй уровень шифрования данных, используя собственный ключ, который вы выбираете и которым управляете.

В Azure это обычно выполняется с помощью ключа шифрования в Azure Key Vault (AKV) клиента. SQL Azure, служба хранилища Azure и Cosmos DB — это несколько примеров, предоставляющих эти возможности уже сегодня. Azure API для FHIR использует эту поддержку через Cosmos DB. При создании учетной записи у вас будет возможность указать универсальный код ресурса (URI) ключа Azure Key Vault. Этот ключ будет передан Cosmos DB при подготовке учетной записи базы данных. Когда выполняется запрос FHIR, Cosmos DB получает ключ и использует его для шифрования или расшифровки данных. Чтобы приступить к работе, воспользуйтесь следующими ссылками:

- [Регистрация поставщика ресурсов Azure Cosmos DB в своей подписке Azure](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#register-resource-provider) 
- [Настройка экземпляра Azure Key Vault](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#configure-your-azure-key-vault-instance)
-  [Добавление политики доступа к экземпляру Azure Key Vault](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#add-an-access-policy-to-your-azure-key-vault-instance)
- [Создание ключа в Azure Key Vault](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#generate-a-key-in-azure-key-vault)

После создания на портале Azure учетной записи Azure API для FHIR в разделе "Параметры базы данных" на вкладке "Дополнительные параметры" можно увидеть параметр конфигурации "Шифрование данных". По умолчанию выбран параметр "Ключ, управляемый службой". Здесь вы можете указать ключ Key Vault, выбрав параметр "Ключ, управляемый клиентом". Здесь можно ввести скопированный URI ключа.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Создание Azure API для FHIR":::

Вы также можете выбрать свой ключ из KeyPicker:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Создание Azure API для FHIR":::

Для имеющихся учетных записей FHIR можно просмотреть вариант шифрования ключей (ключ, управляемый службой или клиентом) в колонке "База данных", как показано ниже. Параметр конфигурации нельзя изменить после выбора. Однако можно изменить и обновить ключ.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Создание Azure API для FHIR":::

Кроме того, можно создать версию указанного ключа, после чего данные будут зашифрованы с помощью новой версии без прерывания работы служб. Вы также можете удалить доступ к ключу, чтобы удалить доступ к данным.
