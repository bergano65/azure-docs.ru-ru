---
title: Контейнерная платформа OpenShift 3.11 в предпосылках Azure
description: Предпосылки для развертывания контейнерной платформы OpenShift 3.11 в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: b2b34a6fdf96613c5bc372e585598fabbe43d53d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066613"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Общие предпосылки для развертывания контейнерной платформы OpenShift 3.11 в Azure

В этой статье описаны общие предварительные требования для развертывания OKD или платформы контейнеров OpenShift в Azure.

При установке OpenShift используются модули playbook Ansible. Чтобы выполнить действия по установке, Ansible использует Secure Shell (SSH) для подключения ко всем узлам кластера.

Когда ansible делает подключение SSH к удаленным хостам, он не может ввести пароль. Поэтому развертывание завершится сбоем, если с закрытым ключом связана парольная фраза.

Так как все виртуальные машины развертываются с помощью шаблонов Azure Resource Manager, для доступа к ним используется один открытый ключ. Соответствующий частный ключ должен быть на VM, который выполняет все playbooks, а также. Для безопасного выполнения этого действия хранилище ключей Azure используется для передачи закрытого ключа в VM.

Если контейнерам требуется постоянное хранилище, вам понадобятся постоянные тома. OpenShift поддерживает виртуальные жесткие диски Azure (VHD) для постоянных томов, но Сначала Azure должен быть настроен как поставщик облачных технологий.

В этой модели OpenShift выполняет следующие действия:

- Создает объект VHD в учетной записи хранения Azure или управляемом диске.
- Подключает виртуальный жесткий диск к виртуальной машине и форматирует том.
- Подключает том к модулю.

Чтобы эта конфигурация работала, OpenShift нужны разрешения на выполнение указанных выше задач в Azure. Для этой цели используется директор службы. Субъект-служба — это учетная запись безопасности в Azure Active Directory, которой предоставляются разрешения на доступ к ресурсам.

Субъект-служба должна иметь доступ к учетным записям хранения и виртуальным машинам, входящим в состав кластера. При развертывании всех ресурсов кластера OpenShift в одной группе ресурсов субъект-служба может получить разрешения на доступ к этой группе ресурсов.

В этом руководстве описывается создание артефактов, связанных с необходимыми компонентами.

> [!div class="checklist"]
> * Создайте хранилище ключей, чтобы управлять ключами SSH для кластера OpenShift.
> * Создайте субъект-службу, которую будет использовать поставщик облачных служб Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="sign-in-to-azure"></a>Вход в Azure 
Войдите в подписку Azure с помощью команды [az login](/cli/azure/reference-index) и следуйте инструкциям на экране или щелкните **Попробовать**, чтобы использовать Cloud Shell.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Для размещения хранилища ключей следует использовать специальную группу ресурсов. Эта группа отделена от группы ресурсов, в которой развертываются ресурсы кластера OpenShift.

Следующий пример создает группу ресурсов, названную *keyvaultrg* в *восточном* местоположении:

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.
Создайте хранилище ключей, где будут храниться ключи SSH для кластера, с помощью команды [az keyvault create](/cli/azure/keyvault). Имя хранилища ключей должно быть глобально уникальным и должно быть включено для развертывания шаблонов, иначе развертывание завершится неудачей с ошибкой "KeyVaultParameterReferenceSecretRetrieveFailed".

В следующем примере создается хранилище ключей с именем *keyvault* в группе ресурсов *keyvaultrg*:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Создание ключа SSH 
Ключ SSH необходим для защиты доступа к кластеру OpenShift. Создайте пару ключей SSH с помощью команды `ssh-keygen` (в Linux или macOS):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Паре ключей SSH нельзя присвоить пароль или парольную фразу.

Дополнительные сведения о ключах SSH в Windows см. в статье [Использование ключей SSH с Windows в Azure](/azure/virtual-machines/linux/ssh-from-windows). Закрытый ключ нужно экспортировать в формате OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Сохранение закрытого ключа SSH в Azure Key Vault
Развертывание OpenShift использует созданный ключ SSH, чтобы защитить доступ к основному кластеру OpenShift. Чтобы обеспечить для развертывания безопасное извлечение ключа SSH, сохраните ключ в Key Vault с помощью следующей команды:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Создание субъекта-службы 
OpenShift взаимодействует с Azure, используя имя пользователя и пароль или субъект-службу. Субъект-служба Azure является удостоверением безопасности, которое можно использовать с приложениями, службами и инструментами автоматизации, такими как OpenShift. Вы можете определять разрешения на то, какие операции может выполнять субъект-служба в Azure, и управлять ими. Лучше всего схожее с разрешения службы - с конкретными группами ресурсов, а не со всей подпиской.

Создайте субъект-службу с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp) и выведите учетные данные, необходимые для OpenShift:

Следующий пример создает основную службу и назначает ему разрешения на участников группе ресурсов, названной *openshiftrg.*

Во-первых, создать группу ресурсов под названием *openshiftrg*:

```azurecli
az group create -l eastus -n openshiftrg
```

Создайте субъект-службу:

```azurecli
az group show --name openshiftrg --query id
```

Сохранить выход команды и использовать вместо $scope в следующей команде

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Обратите внимание на свойство appId и пароль, возвращенный из команды:

```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

 > [!WARNING] 
 > Не забудьте записать безопасный пароль, так как получить этот пароль снова не удастся.

Дополнительные сведения о субъектах-службах см. в статье [Создание субъекта-службы Azure с помощью Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Предпосылки, применимые только к шаблону менеджера ресурсов

Секреты должны быть созданы для SSH частный ключ **(sshPrivateKey**), Azure AD клиента секрет (**aadClientSecret**), OpenShift админ пароль **(openshiftPassword**), и Red Hat Подписка менеджер пароль или активации ключа (**rhsmPasswordOrActivationKey**).  Кроме того, если пользовательские сертификаты SSL используются, то шесть дополнительных секретов должны быть созданы - **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**, и **masterkeyfile**.  Эти параметры будут объяснены более подробно.

Шаблон ссылается на конкретные секретные имена, поэтому вы **должны** использовать жирные имена, перечисленные выше (дело чувствительное).

### <a name="custom-certificates"></a>Пользовательские сертификаты

По умолчанию шаблон развернет кластер OpenShift с использованием самоподписанных сертификатов для веб-консоли OpenShift и домена для разгрома. Если вы хотите использовать пользовательские SSL-сертификаты, установите 'routingCertType' на 'custom' и 'masterCertType' на 'таможню'.  Для сертификатов вам понадобятся файлы CA, Cert и Key в формате .pem.  Пользовательские сертификаты можно использовать для одного, но не для другого.

Вам нужно будет хранить эти файлы в секретах Key Vault.  Используйте тот же Key Vault, что и для частного ключа.  Вместо того, чтобы требовать 6 дополнительных входов для секретных имен, шаблон жестко закодирован для использования конкретных секретных имен для каждого из файлов сертификатов SSL.  Храните данные сертификата, используя информацию из следующей таблицы.

| Секретное имя      | Файл сертификата   |
|------------------|--------------------|
| мастеркафил     | мастер файла CA     |
| mastercertfile   | мастер файла CERT   |
| masterkeyfile    | мастер ключ файл    |
| routingcafile    | файл CA-извлза    |
| routingcertfile  | разгром файла CERT  |
| routingkeyfile   | файл ключа к онеку   |

Создавайте секреты с помощью Azure CLI. Ниже приведен пример.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье рассматриваются следующие темы:
> [!div class="checklist"]
> * Создайте хранилище ключей, чтобы управлять ключами SSH для кластера OpenShift.
> * Создайте субъект-службу для поставщика облачных решений Azure.

Теперь разверните кластер OpenShift:

- [Развертывание платформы контейнеров OpenShift в Azure](./openshift-container-platform-3x.md)
- [Развертывание OpenShift контейнерплатформы самоуправляемой Marketplace Предложение](./openshift-container-platform-3x-marketplace-self-managed.md)
