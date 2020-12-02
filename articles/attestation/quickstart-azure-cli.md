---
title: Настройка службы "Аттестация Azure" с помощью Azure CLI
description: Сведения о том, как установить и настроить поставщик аттестации с помощью Azure CLI.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: dee9e7596c0a30301d9e0453ef22a6dfe9541522
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "96020948"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Краткое руководство. Настройка службы "Аттестация Azure" с помощью Azure CLI

Приступайте к работе с Аттестацией Azure, настроив аттестацию с помощью Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>Начало работы

1. Примените следующую команду, чтобы войти в Azure:

   ```azurecli
   az login
   ```

1. Если нужно, перейдите к подписке для службы "Аттестация Azure":

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Зарегистрируйте в этой подписке поставщик ресурсов Microsoft.Attestation с помощью команды [az provider register](/cli/azure/provider#az_provider_register):

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Дополнительные сведения о поставщиках ресурсов Azure, настройке этих поставщиков и управлении ими см. в статье [Поставщики и типы ресурсов Azure](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > Для каждой подписки поставщик ресурсов регистрируется однократно.

1. Создайте группу ресурсов для поставщика аттестации. В эту же группу ресурсов можно поместить и другие ресурсы Azure, в том числе виртуальные машины с экземплярами клиентского приложения. Выполните команду [az group create](/cli/azure/group#az_group_create), чтобы создать группу ресурсов, или примените существующую группу ресурсов:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Создание поставщика аттестации и управление им

Ниже приведены команды, которые можно использовать для создания поставщика аттестации и управления им.

1. Выполните команду [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create), чтобы создать поставщик аттестации:

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   Параметр **--certs-input-path** указывает набор доверенных ключей подписывания. Если для этого параметра указано имя файла, то поставщик аттестации нужно настраивать только с помощью политик в формате подписанного JWT. В остальных случаях для политик можно использовать форматы простого текста или неподписанного JWT. Сведения о JWT см. в разделе [Основные понятия](basic-concepts.md). Примеры сертификатов для подписывания политик аттестации см. [здесь](policy-signer-examples.md).

1. Выполните команду [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show), чтобы получить сведения о свойствах поставщика аттестации, включая сведения о состоянии и URI аттестации:

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
   ```

   С помощью этой команды отображаются значения, аналогичные приведенным ниже:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

Поставщик аттестации можно удалить с помощью команды [az attestation delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete):

```azurecli
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>Управление политикой

Для управления политиками пользователю Azure AD требуются следующие разрешения для `Actions`:

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

Эти разрешения могут назначаться пользователю AD с помощью роли `Owner` (разрешения с подстановочными знаками), `Contributor` (разрешения с подстановочными знаками) или `Attestation Contributor` (только определенные разрешения для Аттестации Azure).  

Для чтения политик пользователю Azure AD требуются следующие разрешения для `Actions`:

- `Microsoft.Attestation/attestationProviders/attestation/read`

Эти разрешения можно назначать пользователю AD с помощью роли `Reader` (разрешения с подстановочными знаками) или `Attestation Reader` (только определенные разрешения для Аттестации Azure).

Управлять политиками для поставщика аттестации можно с помощью описанных здесь команд (одна среда TEE за раз).

Команда [az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) возвращает текущую политику для указанной среды TEE:

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> С помощью этой команды политика отображается в форматах текста и JWT.

Поддерживаются следующие типы TEE:

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

Используйте команду [az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set), чтобы задать новую политику для указанной среды TEE.

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

Политика аттестации в формате JWT должна содержать утверждение с именем `AttestationPolicy`. Для подписывания политики нужно применять ключ, соответствующий любому из существующих сертификатов для подписывания политик.

Примеры политик см. [здесь](policy-examples.md).

Используйте команду [az attestation policy reset](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset), чтобы задать новую политику для указанной среды TEE.

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>Управление сертификатами для подписывания политик

Управлять сертификатами для подписывания политик для поставщика аттестации можно с помощью следующих команд:

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

Сертификат для подписывания политик — это подписанный JWT, в котором есть утверждение с именем `maa-policyCertificate`. Значением этого утверждения является объект JWK, содержащий доверенный ключ для подписывания, который нужно добавить. Для подписывания JWT нужно применять закрытый ключ, соответствующий любому из существующих сертификатов для подписывания политик. Сведения о JWT и JWK см. в разделе [Основные понятия](basic-concepts.md).

Все семантические операции с сертификатом для подписывания политик следует выполнять за пределами Azure CLI. Сама операция в Azure CLI представляет собой простую строку.

Примеры сертификатов для подписывания политик аттестации см. [здесь](policy-signer-examples.md).

## <a name="next-steps"></a>Дальнейшие действия

- [Как создать и подписать файл политики аттестации](author-sign-policy.md)
- [Статья о реализации аттестации с анклавом SGX с примерами кода](/samples/browse/?expanded=azure&terms=attestation)
