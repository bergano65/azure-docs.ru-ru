---
title: Получение маркеров подписанного URL-адреса в коде | Azure Key Vault
description: Функция управляемой учетной записи хранения обеспечивает простую интеграцию между Azure Key Vault и учетной записью хранения Azure. В этом примере для управления маркерами SAS используется пакет Azure SDK для .NET.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: afe7d5ce3dd1756ddb9e33fe402fb2eb699ce8f7
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007423"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Получение маркеров подписанного URL-адреса в коде

Вы можете управлять учетной записью хранения с помощью маркеров подписанного URL-адреса (SAS), хранящихся в хранилище ключей. Дополнительные сведения см. в статье [Предоставление ограниченного доступа к ресурсам службы хранилища Azure с помощью подписанных URL-адресов (SAS)](../../storage/common/storage-sas-overview.md).

> [!NOTE]
> Мы рекомендуем защитить учетную запись хранения с помощью [управления доступом на основе ролей (RBAC)](../../storage/common/storage-auth-aad.md), чтобы усилить защиту и упростить авторизацию с использованием общего ключа.

В этой статье представлены примеры кода .NET, который создает определение SAS и извлекает маркеры SAS. Полные сведения, включая созданный клиент для учетных записей хранения, управляемых Key Vault, см. в нашем примере [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/). Сведения о создании и хранении маркеров SAS см. в статьях об управлении ключами учетной записи хранения с помощью [Key Vault и Azure CLI](overview-storage-keys.md) или с помощью [Key Vault и Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Примеры кода

В следующем примере показано, как создать шаблон SAS:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Используя этот шаблон, мы можем создать определение SAS с помощью 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

Создав определение SAS, вы сможете получать маркеры SAS, например секреты, с помощью `SecretClient`. Перед именем секрета необходимо указать имя учетной записи хранения и тире:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Если срок действия маркера подписанного URL-адреса истекает, можно снова получить тот же секрет, чтобы создать новый.

## <a name="next-steps"></a>Дальнейшие действия
- [Предоставление ограниченного доступа к ресурсам службы хранилища Azure с помощью подписанных URL-адресов (SAS)](../../storage/common/storage-sas-overview.md)
- Управление ключами учетной записи хранения с помощью [Key Vault и Azure CLI](overview-storage-keys.md) или с помощью [Key Vault и Azure PowerShell](overview-storage-keys-powershell.md)
- См. [полный пример ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/).
- См. [дополнительные примеры для Key Vault](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-key-vault).
- [Примеры ключей управляемой учетной записи хранения](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
