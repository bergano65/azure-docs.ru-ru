---
title: Подключение к Azure Stack при помощи интерфейса командной строки | Документация Майкрософт
description: Узнайте, как использовать межплатформенный интерфейс командной строки (CLI) для развертывания ресурсов и управления ими в Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: b27dd1b9aec89f259649b313d3ba7f944ea647f1
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765727"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Использование профилей версий API и Azure CLI в Azure Stack

Вы можете следовать приведенным здесь инструкциям, чтобы настроить интерфейс командной строки Azure (CLI) для управления ресурсами Пакета средств разработки Azure Stack на клиентских платформах Linux, Mac и Windows.

## <a name="install-cli"></a>Установка CLI

Войдите на рабочую станцию разработки и установите CLI. Для работы с Azure Stack требуется Azure CLI версии 2.0 или выше. Чтобы установить эту версию, выполните действия, описанные в руководстве по [установке Azure CLI](/cli/azure/install-azure-cli). Чтобы проверить успешность установки, откройте окно терминала или командной строки и выполните следующую команду.

```azurecli
az --version
```

Отобразятся номера версий Azure CLI и зависимых библиотек, установленных на этом компьютере.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Доверие для корневого сертификата ЦС Azure Stack

1. Получите корневой сертификат ЦС Azure Stack от [оператора Azure Stack](../azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate) и установите доверие к нему. Чтобы настроить доверие для корневого сертификата ЦС Azure Stack, добавьте его после существующего сертификата Python.

1. Найдите расположение сертификата на своем компьютере. Это расположение зависит от того, куда вы установили Python. Вам потребуется установить [pip](https://pip.pypa.io) и модуль [certifi](https://pypi.org/project/certifi/). Вы можете использовать следующие команды Python из командной строки Bash:

    ```bash  
    python -c "import certifi; print(certifi.where())"
    ```

    Запомните расположение сертификата, например `~/lib/python3.5/site-packages/certifi/cacert.pem`. Конкретный путь зависит от операционной системы и установленной версии Python.

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>Задание пути к компьютеру разработчика внутри облака

Если вы запускаете CLI на компьютере с Linux, созданного в среде Azure Stack, выполните указанную ниже команду Bash с путем к сертификату.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>Задание пути к компьютеру разработчика за пределами облака

Если вы запускаете CLI на компьютере, расположенном за пределами среды Azure Stack:  

1. настройте [подключение VPN к Azure Stack](azure-stack-connect-azure-stack.md);
1. скопируйте сертификат PEM, полученный от оператора Azure Stack, и запомните расположение этого файла (PATH_TO_PEM_FILE);
1. в зависимости от операционной системы на вашей рабочей станции выполните команды в следующих разделах.

#### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="windows"></a> Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting required information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>Получение конечной точки псевдонимов для виртуальных машин

Чтобы создавать виртуальные машины с помощью CLI, обратитесь к оператору Azure Stack и получите URI конечной точки псевдонимов для виртуальных машин. Например, в Azure используется следующий универсальный код ресурса (URI): `https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json`. Администратор облака создает аналогичную конечную точку для Azure Stack с информацией об образах, которые доступны в Azure Stack Marketplace. Вам необходимо передать URI конечной точки в параметр `endpoint-vm-image-alias-doc` команды `az cloud register`, как показано в следующем разделе. 
  
## <a name="connect-to-azure-stack"></a>Подключение к Azure Stack

Для подключения к Azure Stack выполните следующие действия:

1. Зарегистрируйте среду Azure Stack, выполнив команду `az cloud register`.
   
    a. Чтобы зарегистрировать среду *администратора облака*, используйте следующую команду:

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```
    б) Чтобы зарегистрировать среду *пользователя*, используйте команду:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```
    c. Чтобы зарегистрировать *пользователя* в мультитенантной среде, используйте следующие команды:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --endpoint-active-directory-resource-id=<URI of the ActiveDirectoryServiceEndpointResourceID> \
        --profile 2018-03-01-hybrid
      ```
    4.3. Чтобы зарегистрировать пользователя в среде AD FS, используйте команду:

      ```azurecli
      az cloud register \
        -n AzureStack  \
        --endpoint-resource-manager "https://management.local.azurestack.external" \
        --suffix-storage-endpoint "local.azurestack.external" \
        --suffix-keyvault-dns ".vault.local.azurestack.external"\
        --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" \
        --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/"\
        --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/"\
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases> \
        --profile "2018-03-01-hybrid"
      ```
1. Следующие команды позволяют выбрать активную среду:
   
    a. В среде *администратора облака* используйте такую команду:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

    б) В среде *пользователя* используйте команду:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

1. Укажите в конфигурации среды версию API-интерфейса, специально предназначенную для Azure Stack. Чтобы изменить эту конфигурацию, выполните следующую команду:

    ```azurecli
    az cloud update \
      --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Если вы используете версию Azure Stack до 1808, вам нужно использовать профиль версии API **2017-03-09-profile** вместо профиля версии **2018-03-01-hybrid**.

1. Войдите в среду Azure Stack с помощью команды `az login`. Вы можете войти в среду Azure Stack от имени пользователя или [субъекта-службы](../../active-directory/develop/app-objects-and-service-principals.md). 

    * Среды Azure AD
      * Вход от имени *пользователя*. Вы можете указать имя пользователя и пароль непосредственно в команде `az login` или выполнить аутентификацию в браузере. Если для вашей учетной записи включена многофакторная аутентификация, возможным будет только второй вариант.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Если в учетной записи пользователя используется многофакторная аутентификация, команду `az login command` можно выполнять без параметра `-u`. При отсутствии этого параметра команда возвращает URL-адрес и код, которые следует использовать для аутентификации.
   
      * Войдите в систему как *субъект-служба*. Для входа от имени субъекта-службы следует заранее [создать субъект-службу с помощью портала Azure](azure-stack-create-service-principals.md) или CLI, а также назначить ему роль. После этого выполните такую команду для входа:

      ```azurecli  
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```
    * Среды AD FS

        * Войдите в систему как пользователь с помощью веб-браузера с кодом устройства:  
           ```azurecli  
           az login --use-device-code
           ```

           > [!NOTE]  
           >При отсутствии этого параметра команда возвращает URL-адрес и код, которые следует использовать для аутентификации.

        * Войдите в систему как субъект-служба:
        
          1. Подготовьте PEM-файл для использования для входа субъект-службы.

            * На клиентском компьютере, где был создан субъект, экспортируйте сертификат субъект-службы как PFX-файл с закрытым ключом, расположенным по пути `cert:\CurrentUser\My`. Имя сертификата совпадает с именем субъекта.
        
            * Преобразуйте PFX в PEM (используйте служебную программу OpenSSL).

          2.  Вход в интерфейс командной строки:
            ```azurecli  
            az login --service-principal \
              -u <Client ID from the Service Principal details> \
              -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
              --tenant <Tenant ID> \
              --debug 
            ```

## <a name="test-the-connectivity"></a>Проверка подключения

После установки всех настроек можно приступить к созданию ресурсов в Azure Stack с помощью CLI. Например, можно создать группу ресурсов для приложения и добавить виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов с именем MyResourceGroup.

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Если группа ресурсов будет успешно создана, команда выше возвратит следующие свойства созданного ресурса:

![Выходные данные при создании группы ресурсов](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Известные проблемы

При использовании интерфейса командной строки в Azure Stack существуют следующие известные проблемы.

 - Интерактивный режим CLI. Например, команда `az interactive` пока не поддерживается в Azure Stack.
 - Чтобы получить список образов виртуальных машин, доступных в Azure Stack, используйте команду `az vm image list --all` вместо `az vm image list`. Указание параметра `--all` гарантирует, что ответ возвращает только образы, доступные в среде Azure Stack.
 - Псевдонимы образов виртуальных машин, доступные в Azure, могут быть неприменимыми для Azure Stack. При использовании образов виртуальных машин вместо псевдонима образа необходимо использовать параметр полного URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0). Этот URN должен соответствовать спецификации образа, полученной из команды `az vm images list`.

## <a name="next-steps"></a>Дополнительная информация

- [Развертывание шаблонов с помощью интерфейса командной строки Azure](azure-stack-deploy-template-command-line.md)
- [Включение Azure CLI для пользователей Azure Stack (оператор)](../azure-stack-cli-admin.md)
- [Управление разрешениями пользователей](azure-stack-manage-permissions.md) 