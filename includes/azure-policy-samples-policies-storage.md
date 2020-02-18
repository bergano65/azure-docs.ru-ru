---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 7aa6abb1a5fa9c969ca5e6d0730bed3b461fe81b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170292"
---
|Имя |Описание |Действие |Версия |
|---|---|---|---|
|[Допустимые SKU учетной записи хранения](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) |Эта политика позволяет задать набор SKU учетных записей хранения, которые может развертывать ваша организация. |Запрет |1.0.0 |
|[Аудит неограниченного сетевого доступа к учетным записям хранения](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |Проведите аудит неограниченного сетевого доступа с применением параметров брандмауэра учетной записи хранения. Вместо этого можно настроить правила сети так, чтобы учетная запись хранения была доступна только приложениям из разрешенных сетей. Чтобы разрешить подключения от конкретных локальных и интернет-клиентов, вы можете открыть доступ для трафика из конкретных виртуальных сетей Azure или для определенных диапазонов общедоступных IP-адресов. |Audit, Disabled |1.0.0 |
|[Развертывание Расширенной защиты от угроз для учетных записей хранения](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |Эта политика позволяет включить Расширенную защиту от угроз для учетных записей хранения. |DeployIfNotExists, Disabled |1.0.0 |
|[Учетные записи хранения должны использовать геоизбыточное хранилище](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |Эта политика позволяет проверить наличие учетных записей хранения, для которых не включено геоизбыточное хранилище. |Audit, Disabled |1.0.0 |
|[Должно выполняться безопасное перемещение в учетные записи хранения](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |Настройка требования выполнять аудит для безопасного переноса в учетную запись хранения. Безопасная передача данных — это параметр, при включении которого ваша учетная запись хранения принимает запросы только с безопасных подключений (HTTPS). Протокол HTTPS обеспечивает проверку подлинности между сервером и службой, а также защищает перемещаемые данные от атак сетевого уровня, таких как "злоумышленник в середине", прослушивание трафика и перехват сеанса. |Audit, Deny, Disabled |1.0.0 |
|[Учетные записи хранения должны разрешать доступ из доверенных служб Майкрософт](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |Некоторые службы Майкрософт, взаимодействующие с учетными записями хранения, работают из сетей, которым нельзя предоставить доступ через сетевые правила. Чтобы помочь таким службам работать как нужно, разрешите доверенным службам Майкрософт обходить сетевые правила. Эти службы будут использовать строгую проверку подлинности для доступа к учетной записи хранения. |Audit, Deny, Disabled |1.0.0 |
|[Необходимо перенести учетные записи хранения в новые ресурсы Azure Resource Manager](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |Используйте для своих учетных записей хранения новый выпуск Azure Resource Manager версии 2 с усовершенствованными функциями безопасности, включая более строгое управление доступом (RBAC), улучшенный аудит, развертывание и управление на основе Resource Manager, доступ к управляемым удостоверениям и хранилищам ключей для секретов, проверку подлинности на основе Azure AD, а также поддержку тегов и групп ресурсов, упрощающих управление защитой. |Audit, Deny, Disabled |1.0.0 |
