---
title: Предварительные требования для OpenShift в Azure | Документация Майкрософт
description: Необходимые условия для развертывания OpenShift в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2019
ms.author: haroldw
ms.openlocfilehash: 834484278bb597bba4a5e1821d0b6572913a761d
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147003"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Общие предварительные требования для развертывания OpenShift в Azure

В этой статье описаны общие предварительные требования для развертывания OKD или платформы контейнеров OpenShift в Azure.

При установке OpenShift используются модули playbook Ansible. Чтобы выполнить действия по установке, Ansible использует Secure Shell (SSH) для подключения ко всем узлам кластера.

Когда ansible делает SSH-подключения к удаленным компьютерам, его нельзя ввести пароль. Поэтому развертывание завершится сбоем, если с закрытым ключом связана парольная фраза.

Так как все виртуальные машины развертываются с помощью шаблонов Azure Resource Manager, для доступа к ним используется один открытый ключ. Соответствующий закрытый ключ должны находиться в виртуальной Машине, которая выполняет все тренировочные. Чтобы сделать это безопасно, Azure key vault используется для передачи закрытого ключа в виртуальную Машину.

Если контейнерам требуется постоянное хранилище, вам понадобятся постоянные тома. OpenShift поддерживает виртуальные жесткие диски Azure (VHD) для постоянного тома, но сначала требуется настроить Azure в качестве поставщика облачных служб.

В этой модели OpenShift выполняет следующие действия:

- Создает объект виртуального жесткого диска в учетную запись хранилища Azure или управляемого диска.
- Подключает виртуальный жесткий диск к виртуальной машине и форматирует том.
- Подключает том к модулю.

Чтобы эта конфигурация работала, OpenShift нужны разрешения на выполнение указанных выше задач в Azure. Субъект-служба используется для этой цели. Субъект-служба — это учетная запись безопасности в Azure Active Directory, которой предоставляются разрешения на доступ к ресурсам.

Субъект-служба должна иметь доступ к учетным записям хранения и виртуальным машинам, входящим в состав кластера. При развертывании всех ресурсов кластера OpenShift в одной группе ресурсов субъект-служба может получить разрешения на доступ к этой группе ресурсов.

В этом руководстве описывается создание артефактов, связанных с необходимыми компонентами.

> [!div class="checklist"]
> * Создайте хранилище ключей, чтобы управлять ключами SSH для кластера OpenShift.
> * Создайте субъект-службу, которую будет использовать поставщик облачных служб Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="sign-in-to-azure"></a>Вход в Azure 
Войдите в подписку Azure с помощью команды [az login](/cli/azure/reference-index) и следуйте инструкциям на экране или щелкните **Попробовать**, чтобы использовать Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Следует использовать выделенную группу ресурсов для размещения хранилища ключей. Эта группа отделена от группы ресурсов, в которой развертываются ресурсы кластера OpenShift.

В следующем примере создается группа ресурсов с именем *keyvaultrg* в расположении *eastus*:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.
Создайте хранилище ключей, где будут храниться ключи SSH для кластера, с помощью команды [az keyvault create](/cli/azure/keyvault). Имя хранилища ключей должно быть глобально уникальным и должен быть включен для развертывания шаблона, или развертывание завершится с ошибкой «KeyVaultParameterReferenceSecretRetrieveFailed».

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
OpenShift взаимодействует с Azure, используя имя пользователя и пароль или субъект-службу. Субъект-служба Azure является удостоверением безопасности, которое можно использовать с приложениями, службами и инструментами автоматизации, такими как OpenShift. Вы можете определять разрешения на то, какие операции может выполнять субъект-служба в Azure, и управлять ими. Рекомендуется ограничить область разрешения участника для определенных групп ресурсов службы, а не всю подписку.

Создайте субъект-службу с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp) и выведите учетные данные, необходимые для OpenShift:

В следующем примере создается субъект-служба, а группе ресурсов с именем openshiftrg назначаются разрешения участника.

Прежде всего создайте группу ресурсов с именем openshiftrg:

```azurecli
az group create -l eastus -n openshiftrg
```

Создайте субъект-службу:

```azurecli
scope=`az group show --name openshiftrg --query id`
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --password {Strong Password} \
      --scopes $scope
```
Если вы используете Windows, выполните ```az group show --name openshiftrg --query id``` и используйте полученные выходные данные вместо $scope.

Запишите свойство appId, возвращенное из команды:
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
 > Обязательно создайте надежный пароль. Следуйте руководству по [правилам и ограничениям для паролей Azure AD](/azure/active-directory/active-directory-passwords-policy).

Дополнительные сведения о субъектах-службах см. в статье [Создание субъекта-службы Azure с помощью Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Предварительные требования, применимо только к шаблона Resource Manager

Секреты будут должны быть созданы для закрытого ключа SSH (**sshPrivateKey**), секрет клиента Azure AD (**aadClientSecret**), пароль администратора OpenShift (**openshiftPassword** ) и Red Hat Subscription Manager пароля или активации ключа (**rhsmPasswordOrActivationKey**).  Кроме того, если используются пользовательские SSL-сертификаты, затем шесть дополнительных секретов будет должны быть созданы - **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**, и **masterkeyfile**.  Эти параметры будут рассматриваться более подробно.

Шаблон ссылается на определенные имена секретов, поэтому вы **необходимо** использования, выделенные полужирным шрифтом имена перечисленных выше (с учетом регистра).

### <a name="custom-certificates"></a>Пользовательские сертификаты

По умолчанию шаблон развернет кластер OpenShift, с использованием самозаверяющих сертификатов для веб-консоли OpenShift и домена маршрутизации. Если вы хотите использовать пользовательские сертификаты SSL, задайте «routingCertType» на «custom» и «masterCertType» на «custom».  Вам потребуется файлы ЦС, сертификат и ключ в формате PEM-файл для сертификатов.  Это можно использовать пользовательские сертификаты для одного, но не в другой.

Вам потребуется для хранения этих файлов в секретов Key Vault.  Используйте в том же хранилище ключей, которая использовалась для закрытого ключа.  А не требовать 6 дополнительных входных данных для имена секретов, шаблон, жестко использовать определенные секретные имена для каждого из файлов сертификатов SSL.  Store данные сертификата, используя сведения из следующей таблицы.

| Имя секрета      | Файл сертификата   |
|------------------|--------------------|
| mastercafile     | главный файл ЦС     |
| mastercertfile   | главный файл сертификата   |
| masterkeyfile    | файл главного ключа    |
| routingcafile    | файл маршрутизации ЦС    |
| routingcertfile  | маршрутизации файла сертификата  |
| routingkeyfile   | файл ключа маршрутизации   |

Создайте секреты с помощью Azure CLI. Ниже приведен пример.

```bash
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье рассматриваются следующие темы:
> [!div class="checklist"]
> * Создайте хранилище ключей, чтобы управлять ключами SSH для кластера OpenShift.
> * Создайте субъект-службу для поставщика облачных решений Azure.

Теперь разверните кластер OpenShift:

- [Развертывание платформы контейнеров OpenShift в Azure](./openshift-container-platform.md)
- [Развертывание предложения самостоятельным управлением Marketplace платформы контейнеров OpenShift](./openshift-marketplace-self-managed.md)