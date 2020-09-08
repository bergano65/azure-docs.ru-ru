---
title: Клиентские библиотеки для Azure Key Vault | Документация Майкрософт
description: Клиентские библиотеки для Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 34321ac9baf3d3cb43dace33b574cb582c2d72a0
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378956"
---
# <a name="client-libraries-for-azure-key-vault"></a>Клиентские библиотеки для Azure Key Vault

Клиентские библиотеки для Azure Key Vault обеспечивают программный доступ к функциям Key Vault из кода на различных языках, включая .NET, Python, Java и JavaScript.

## <a name="client-libraries-per-language-and-object"></a>Клиентские библиотеки для конкретных языков и объектов

Каждый пакет SDK включает отдельные клиентские библиотеки для секретов, ключей и сертификатов, как описано в таблице ниже.

| Язык | Секреты | Ключи | Сертификаты |
|--|--|--|--|
| .NET | - [Справочник по API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [Пакет NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Краткое руководство](../secrets/quick-create-net.md) | - [Справочник по API](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [Пакет NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [Справочник по API](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [Пакет NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) |
| Python| - [Справочник по API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [Пакет PyPi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Краткое руководство](../secrets/quick-create-python.md) |- [Справочник по API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [Пакет PyPi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Краткое руководство](../keys/quick-create-python.md) | - [Справочник по API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [Пакет PyPi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Краткое руководство](../certificates/quick-create-python.md) |
| Java | - [Справочник по API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Краткое руководство](../secrets/quick-create-java.md) |- [Справочник по API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [Справочник по API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |
| Node.js | - [Справочник по API](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [Пакет npm](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Краткое руководство](../secrets/quick-create-node.md) |- [Справочник по API](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [Пакет npm](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [Справочник по API](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [Пакет npm](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |

## <a name="next-steps"></a>Дальнейшие действия

- См. [руководство разработчика Azure Key Vault](developers-guide.md).
- См. дополнительные сведения о [проверке подлинности в хранилище ключей](authentication.md).
