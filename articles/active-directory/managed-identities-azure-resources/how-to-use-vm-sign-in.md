---
title: Использование управляемых удостоверений на виртуальной машине Azure для входа в Azure AD
description: Пошаговые инструкции с примерами использования субъекта-службы управляемых удостоверений для ресурсов Azure на виртуальной машине Azure для входа и доступа к ресурсам из клиентского сценария.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34f4dc749c0254b5aa4e9ff018d2a869832de3f0
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547384"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Как использовать управляемые удостоверения для ресурсов Azure на виртуальной машине Azure для входа 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Эта статья содержит примеры сценариев PowerShell и интерфейса командной строки, позволяющих войти в систему с использованием субъекта-службы управляемых удостоверений для ресурсов Azure, а также рекомендации по некоторым важным вопросам, таким как обработка ошибок.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Если вы планируете использовать примеры Azure PowerShell или CLI из этой статьи, убедитесь, что у вас установлена последняя версия [Azure PowerShell](/powershell/azure/install-az-ps) или [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Во всех примерах сценариев в этой статье предполагается, что клиент командной строки выполняется на виртуальной машине с включенными управляемыми удостоверениями для ресурсов Azure. Используйте функцию подключения виртуальной машины на портале Azure для удаленного подключения к своей виртуальной машине. Дополнительные сведения о включении управляемых удостоверений для ресурсов Azure на виртуальной машине см. в статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью Azure CLI](qs-configure-portal-windows-vm.md) или в одном из вариантов статьи (с использованием PowerShell, интерфейса командной строки, шаблона или пакета SDK для Azure). 
> - Чтобы избежать ошибок во время доступа к ресурсам, для управляемого удостоверения виртуальной машины необходимо предоставить по меньшей мере роль "Читатель" в соответствующей области (на уровне виртуальной машины или выше), то есть разрешить операции Azure Resource Manager на виртуальной машине. Дополнительные сведения см. в статье [Назначение доступа на основе управляемого удостоверения службы для ресурса с помощью портала Azure](howto-assign-access-portal.md).

## <a name="overview"></a>Краткое описание

Управляемые удостоверения для ресурсов Azure предоставляют [объект субъекта-службы](../develop/developer-glossary.md#service-principal-object), который [создается при включении управляемых удостоверений для ресурсов Azure](overview.md#how-does-the-managed-identities-for-azure-resources-work) на виртуальной машине. Субъекту-службе можно предоставить доступ к определенным ресурсам Azure и использовать его в качестве удостоверения для входа и использования ресурсов из скриптов и клиентов командной строки. Чтобы клиентский скрипт мог обращаться к защищенным ресурсам с собственным удостоверением, обычно нужно выполнить следующие условия:  

   - зарегистрировать скрипт в качестве конфиденциального клиента приложения или приложения веб-клиента в Azure AD и получить соответствующее согласие;
   - выполнить вход из скрипта с соответствующим субъектом-службой, используя учетные данные приложения (обычно они встраиваются в скрипт).

Благодаря управляемым удостоверениям для ресурсов Azure вашему клиенту сценария больше не требуется делать ни то, ни другое, так как он может войти с помощью субъекта-службы управляемых удостоверений для ресурсов Azure. 

## <a name="azure-cli"></a>Azure CLI

Следующий сценарий демонстрирует, как:

1. войти в Azure AD с помощью субъекта-службы управляемых удостоверений для ресурсов Azure на виртуальной машине;  
2. вызвать Azure Resource Manager и получить идентификатор субъекта-службы виртуальной машины. CLI автоматически управляет получением и использованием маркера. Не забудьте указать правильное имя виртуальной машины вместо `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Следующий сценарий демонстрирует, как:

1. войти в Azure AD с помощью субъекта-службы управляемых удостоверений для ресурсов Azure на виртуальной машине;  
2. вызвать командлет Azure Resource Manager для получения сведений о виртуальной машине. PowerShell автоматически управляет использованием маркера.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Идентификаторы ресурсов для служб Azure

Список ресурсов, которые поддерживают Azure AD и были протестированы с управляемыми удостоверениями для ресурсов Azure и соответствующими идентификаторами ресурсов, приведен в разделе [Службы Azure, поддерживающие аутентификацию Azure AD](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

## <a name="error-handling-guidance"></a>Руководство по обработке ошибок 

Такие ответы могут обозначать, что управляемые удостоверения для ресурсов Azure на виртуальной машины настроены неправильно:

- PowerShell: *Invoke-WebRequest : Unable to connect to the remote server* (Invoke-WebRequest: Unable to connect to the remote server)
- CLI: *MSI: не удалось получить маркер из `http://localhost:50342/oauth2/token` с ошибкой "HTTPConnectionPool (узел =" localhost ", порт = 50342)* 

Если вы встретите любую из этих ошибок, найдите виртуальную машину Azure на [портале Azure](https://portal.azure.com) и выполните на ней следующие действия:

- Перейдите на страницу **Удостоверение** и убедитесь, что параметр **Назначено системой** имеет значение "Да".
- Откройте страницу **Расширения** и убедитесь, что расширение управляемых удостоверений для ресурсов Azure **(прекращение поддержки запланировано на январь 2019 г.)** успешно развернуто.

Если какое-либо из условий не выполнено, может потребоваться повторно развернуть управляемые удостоверения для ресурсов Azure для ресурса или устранить неполадки развертывания. Если вам нужна помощь с конфигурацией виртуальной машины, ознакомьтесь с разделом [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине с помощью портала Azure](qs-configure-portal-windows-vm.md).

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы включить управляемые удостоверения для ресурсов Azure на виртуальной машине Azure, обратитесь к статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью PowerShell](qs-configure-powershell-windows-vm.md) или [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью Azure CLI](qs-configure-cli-windows-vm.md).






