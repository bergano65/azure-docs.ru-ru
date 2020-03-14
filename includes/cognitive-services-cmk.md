---
title: включить файл
description: включить файл
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372197"
---
### <a name="enable-customer-managed-keys"></a>Включить управляемые клиентом ключи

Для хранения ключей, управляемых клиентом, необходимо использовать Azure Key Vault. Можно либо создать собственные ключи и сохранить их в хранилище ключей, либо использовать Azure Key Vault API для создания ключей. Ресурс Cognitive Services и хранилище ключей должны находиться в одном и том же регионе и в одном клиенте Azure Active Directory (Azure AD), но могут находиться в разных подписках. Дополнительные сведения о Azure Key Vault см. в разделе [что такое Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Новый ресурс Cognitive Services всегда шифруется с помощью ключей, управляемых корпорацией Майкрософт. Невозможно включить управляемые клиентом ключи на момент создания ресурса. Ключи, управляемые клиентом, хранятся в Azure Key Vault, а хранилище ключей должно быть подготовлено с политиками доступа, которые предоставляют ключевые разрешения для управляемого удостоверения, связанного с ресурсом Cognitive Services. Управляемое удостоверение доступно только после создания ресурса с использованием ценовой категории для CMK.

Сведения об использовании управляемых клиентом ключей с Azure Key Vault для шифрования Cognitive Services см. в следующих статьях:

- [Настройте ключи, управляемые клиентом, с помощью Key Vault для Cognitive Servicesного шифрования из портал Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

Включение управляемых ключей клиентов также позволит назначить управляемое системой удостоверение, компонент Azure AD. После включения управляемого удостоверения, назначенного системой, этот ресурс будет зарегистрирован в Azure Active Directory. После регистрации управляемому удостоверению будет предоставлен доступ к Key Vault, выбранному во время настройки управляемого ключа клиента. Дополнительные сведения об [управляемых удостоверениях](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)см. здесь.

> [!IMPORTANT]
> При отключении управляемых удостоверений, назначенных системой, доступ к хранилищу ключей будет удален, а все данные, зашифрованные с помощью ключей клиента, станут недоступны. Все функции, зависящие от этих данных, перестанут работать.

> [!IMPORTANT]
> Сейчас управляемые удостоверения не поддерживаются в сценариях работы с разными каталогами. При настройке ключей, управляемых клиентом, в портал Azure управляемое удостоверение автоматически назначается в рамках. Если впоследствии вы перемещаете подписку, группу ресурсов или ресурс из одного каталога Azure AD в другой, управляемое удостоверение, связанное с ресурсом, не передается в новый клиент, поэтому управляемые клиентом ключи могут перестать работать. Дополнительные сведения см. в статье **Передача подписки между каталогами Azure AD** в [часто задаваемых вопросах и известных проблемах с управляемыми удостоверениями для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Хранение ключей, управляемых клиентом, в Azure Key Vault

Чтобы включить управляемые клиентом ключи, необходимо использовать Azure Key Vault для хранения ключей. Необходимо включить как **обратимое удаление** , так и **не очищать** свойства в хранилище ключей.

При шифровании Cognitive Services поддерживаются только ключи RSA размером 2048. Дополнительные сведения о ключах см. в разделе **Key Vault Keys** раздела [о Azure Key Vault ключах, секретах и сертификатах](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Смена ключей, управляемых клиентом

Вы можете поворачивать ключ, управляемый клиентом, в Azure Key Vault в соответствии с политиками соответствия требованиям. При вращении ключа необходимо обновить ресурс Cognitive Services для использования нового URI ключа. Сведения о том, как обновить ресурс для использования новой версии ключа в портал Azure, см. в разделе **обновление ключа версии** раздела [Настройка управляемых клиентом ключей для Cognitive Services с помощью портал Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md).

Вращение ключа не вызывает повторного шифрования данных в ресурсе. От пользователя не требуется предпринимать никаких действий.

### <a name="revoke-access-to-customer-managed-keys"></a>Отозвать доступ к ключам, управляемым клиентом

Чтобы отозвать доступ к ключам, управляемым клиентом, используйте PowerShell или Azure CLI. Дополнительные сведения см. в разделе [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) или [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Отзыв доступа позволяет блокировать доступ ко всем данным в Cognitive Services ресурсе, так как ключ шифрования недоступен для Cognitive Services.


