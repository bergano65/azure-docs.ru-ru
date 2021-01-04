---
title: Управление пакетами Python 3 в службе автоматизации Azure
description: В этой статье рассказывается, как управлять пакетами Python 3 (Предварительная версия) в службе автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734307"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Управление пакетами Python 3 (Предварительная версия) в службе автоматизации Azure

Служба автоматизации Azure позволяет запускать модули Runbook для Python 3 (Предварительная версия) в Azure и гибридные рабочие роли Runbook для Linux. Чтобы упростить модули runbook, можно использовать пакеты Python для импорта необходимых модулей. В этой статье описывается, как управлять пакетами Python 3 (Предварительная версия) и использовать их в службе автоматизации Azure.

## <a name="import-packages"></a>Импорт пакетов

В учетной записи службы автоматизации выберите **пакеты Python** в разделе **Общие ресурсы**. Выберите **+ Добавить пакет Python**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Снимок экрана: страница пакетов Python 3 содержит пакеты Python 3 в меню слева и добавляет выделенный пакет Python 2.":::

На странице **Добавление пакета Python** выберите Python 3 для **версии** и выберите локальный пакет для отправки. Пакет может иметь расширение файла **.whl** или **.tar.gz**. Когда пакет выбран, нажмите кнопку **ОК** , чтобы передать его.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="На снимке экрана показана страница Добавление пакета Python 3 с выбранным файлом tar. gz.":::

После импорта пакета он отображается на странице пакеты Python в учетной записи службы автоматизации на вкладке **пакеты Python 3 (Предварительная версия)** . Если необходимо удалить пакет, выберите пакет и нажмите кнопку **Удалить**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="На снимке экрана показана страница пакетов Python 3 после импорта пакета.":::

## <a name="import-packages-with-dependencies"></a>Импорт пакетов с зависимостями

Служба автоматизации Azure не разрешает зависимости для пакетов Python в процессе импорта. Однако существует способ импортировать пакет со всеми его зависимостями.

### <a name="manually-download"></a>Скачивание вручную

На компьютере под управлением Windows 64 с установленным [Python 3,8](https://www.python.org/downloads/release/python-380/) и [PIP](https://pip.pypa.io/en/stable/) выполните следующую команду, чтобы скачать пакет и все его зависимости:

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

После загрузки пакетов их можно импортировать в учетную запись службы автоматизации.

## <a name="use-a-package-in-a-runbook"></a>Использование пакета в модуле runbook

Импортируемый пакет можно использовать в модуле Runbook. Добавьте следующий код, чтобы вывести список всех групп ресурсов в подписке Azure.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в статье [Создание runbook для Python](learn/automation-tutorial-runbook-textual-python-3.md).
