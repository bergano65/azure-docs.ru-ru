---
title: Краткое руководство по использованию клиентской библиотеки Azure Key Vault для Python
description: Узнайте, как создавать, извлекать и удалять сертификаты из Azure Key Vault с помощью клиентской библиотеки Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: tracking-python
ms.openlocfilehash: 7c67bc77984af8a25c3514931ed0983df1751d5e
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126133"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Краткое руководство. Использование клиентской библиотеки Azure Key Vault для Python

Приступите к работе с клиентской библиотекой Azure Key Vault для Python. Чтобы установить пакет и испробовать пример кода для выполнения базовых задач, выполните описанные ниже шаги.

Хранилище ключей Azure помогает защитить криптографические ключи и секреты, используемые облачными приложениями и службами. Клиентская библиотека Azure Key Vault для Python позволяет выполнять следующие задачи:

- Повысьте уровень безопасности и увеличьте контроль над ключами и паролями.
- Создавайте и импортируйте ключи шифрования за считаные минуты.
- Сократите время задержки с помощью масштабирования в облаке и глобального резервирования.
- Упрощение и автоматизация задач, связанных с TLS- и SSL-сертификатами.
- Используйте модули HSM, отвечающие стандартам FIPS 140-2 уровня 2.

[Справочная документация по API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [Пакет (Python Package Index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2.7, Python 3.5.3 или более поздней версии.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) или [Azure PowerShell](/powershell/azure/overview).

В этом кратком руководстве предполагается, что вы используете [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) в окне терминала Linux.

## <a name="setting-up"></a>Настройка

### <a name="install-the-package"></a>Установка пакета

Через окно консоли установите клиентскую библиотеку сертификатов Azure Key Vault для Python.

```console
pip install azure-keyvault-certificates
```

Для этого краткого руководства вам также понадобится установить пакет azure.identity.

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Создание группы ресурсов и хранилища ключей

В этом кратком руководстве используется предварительно созданное хранилище ключей Azure. Хранилище ключей можно создать, выполнив действия, описанные в кратких руководствах по [Azure CLI](quick-create-cli.md), [Azure PowerShell](quick-create-powershell.md) или [порталу Azure](quick-create-portal.md). Вы также можете выполнить команды Azure CLI, которые предложены ниже.

> [!Important]
> Каждое хранилище ключей должно иметь уникальное имя. В следующих примерах замените <your-unique-keyvault-name> именем своего хранилища ключей.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Создание субъекта-службы

Самый простой способ проверить подлинность облачного приложения NET — с помощью управляемых удостоверений. Дополнительные сведения см. в статье [Provide Key Vault authentication with a managed identity](../general/managed-identity.md) (Предоставление проверки подлинности Key Vault с помощью управляемого удостоверения).

Однако для простоты в рамках этого краткого руководства создается классическое приложение, требующее использования субъекта-службы и политики управления доступом. Для субъекта-службы требуется уникальное имя в формате http://&lt;уникальное_имя_субъекта-службы&gt;.

Создайте субъект-службу с помощью команды Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac):

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
```

Эта операция возвращает ряд пар "ключ — значение". 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Запишите значения clientId и clientSecret, так как они нам потребуются далее на шаге [настройки переменных среды](#set-environmental-variables).

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Предоставление субъекту-службе доступа к хранилищу ключей

Создайте политику доступа для хранилища ключей, которая предоставляет разрешение субъекту-службе, передавая clientId в команду [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Предоставьте субъекту-службе разрешения на получение, перечисление и создание сертификатов.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --certificate-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Настройка переменных среды

Метод DefaultAzureCredential в нашем приложении использует три переменные среды: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` и `AZURE_TENANT_ID`. Присвойте этим переменным значения clientId, clientSecret и tenantId, записанные на шаге [создания субъекта-службы](#create-a-service-principal), используя формат `export VARNAME=VALUE`. (В этом методе задаются только переменные для текущей оболочки и процессов, созданных в этой оболочке. Чтобы добавить эти переменные в среду на постоянной основе, измените файл `/etc/environment `.) 

Кроме того, необходимо будет сохранить имя хранилища ключей как переменную среды `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Объектная модель

Клиентская библиотека Azure Key Vault для Python позволяет управлять ключами и связанными с ними ресурсами, такими как сертификаты и секреты. В приведенных ниже примерах кода показано, как создать клиент, создать сертификат, получить сертификат и удалить его.

## <a name="code-examples"></a>Примеры кода

### <a name="add-directives"></a>Добавление директив

Добавьте в верхнюю часть кода следующие директивы:

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Аутентификация и создание клиента

Аутентификация в хранилище ключей и создание клиента хранилища ключей зависят от переменных среды, приведенных на шаге [настройки переменные среды](#set-environmental-variables) выше. Имя хранилища ключей расширяется до универсального кода ресурса (URI) хранилища ключей в формате "https://<имя_хранилища_ключей>.vault.azure.net".

```python
credential = DefaultAzureCredential()

client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Сохранение сертификата

Теперь, когда приложение прошло проверку подлинности, можно добавить самозаверяющий сертификат в keyvault. 

```python
certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()
)
certificate = certificate_operation_poller.result()
```

Команда [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) позволяет убедиться, что сертификат задан успешно:

```azurecli
az keyvault certificate show --vault-name <your-unique-keyvault-name> --name myCertificate
```

### <a name="retrieve-a-certificate"></a>Получение сертификата

Теперь вы можете получить ранее созданный сертификат

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Теперь сертификат сохраняется как `retrieved_certificate`.

### <a name="delete-a-certificate"></a>Удаление сертификата

Наконец, давайте удалим сертификат из хранилища ключей.

```python
client.delete_certificate(certificateName)
```

Команда [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) позволяет убедиться, что сертификат успешно удален:

```azurecli
az keyvault certifcate show --vault-name <your-unique-keyvault-name> --name myCertificate
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы больше не нужны, вы можете использовать интерфейс командной строки Azure или Azure PowerShell, чтобы удалить хранилище ключей и соответствующую группу ресурсов.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Образец кода

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = "myCertificate"

print("Creating a certificate in " + keyVaultName + " called '" + certificateName  + "` ...")

certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()

certificate = certificate_operation_poller.result()

print(" done.")

print("Retrieving your certificate from " + keyVaultName + ".")

retrieved_certificate = client.get_certificate(certificateName)

print("Certificate with name '{0}' was found'.".format(retrieved_certificate.name))
print("Deleting your certificate from " + keyVaultName + " ...")

client.delete_certificate(certificateName)

print(" done.")
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали хранилище ключей, сохранили в нем сертификат и извлекли его. Дополнительные сведения о Key Vault и его интеграции в приложения см. в следующих статьях.

- [Обзор Azure Key Vault](../general/overview.md)
- [Руководство разработчика Azure Key Vault](../general/developers-guide.md)
- [Рекомендации по Azure Key Vault](../general/best-practices.md)
