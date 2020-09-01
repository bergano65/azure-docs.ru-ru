---
title: Создание сертификатов с помощью средства проверки готовности центра Microsoft Azure Stack | Документация Майкрософт
description: Описание создания запросов на сертификаты, а также получения и установки сертификатов на устройстве Azure Stack ребра с помощью средства проверки готовности центра Azure Stack.
services: Azure Stack Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: ea560cf7d61a208cf29aef977aea6d1584a7cdb7
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146052"
---
# <a name="create-certificates-for-your-azure-stack-edge-using-azure-stack-hub-readiness-checker-tool"></a>Создание сертификатов для Azure Stack пограничных устройств с помощью средства проверки готовности центра Azure Stack 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

В этой статье описывается, как создать сертификаты для Azure Stack ребра с помощью средства проверки готовности центра Azure Stack. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Использование средства проверки готовности центра Azure Stack

Используйте средство проверки готовности концентратора Azure Stack для создания запросов подписи сертификатов (представители по обслуживанию клиентов) для развертывания устройства Azure Stackного пограничных устройств. Эти запросы можно создать после того, как будет размещен заказ на Azure Stack пограничных устройств и ждать, пока устройство будет доставлено. 

> [!NOTE]
> Используйте это средство только для целей тестирования и разработки, а не для рабочих устройств. 

Вы можете использовать средство проверки готовности Azure Stack Hub (AzsReadinessChecker) для запроса следующих сертификатов.

- Сертификат Azure Resource Manager
- Локальный сертификат пользовательского интерфейса
- Сертификат узла
- Сертификат BLOB-объекта
- VPN-сертификат


## <a name="prerequisites"></a>Предварительные требования

Чтобы создать представители по обслуживанию клиентов для Azure Stack развертывания устройств, убедитесь в том, что: 

- У вас есть клиент под управлением Windows 10 или Windows Server 2016 или более поздней версии. 
- Вы скачали средство проверки готовности Microsoft Azure Stack Hub 1.2002.1133.85 [из коллекция PowerShell](https://aka.ms/AzsReadinessChecker) в этой системе. Может потребоваться выполнить поиск этого пакета. Только эта версия средства может создавать сертификаты для Azure Stack пограничных устройств.
- Для сертификатов имеются следующие сведения.
  - Имя устройства
  - Серийный номер узла
  - Внешнее полное доменное имя (FQDN)

## <a name="generate-certificate-signing-requests"></a>Создание запросов на подпись сертификата

Выполните следующие действия, чтобы подготовить сертификаты Azure Stack пограничных устройств:

1. Запустите PowerShell от имени администратора (5,1 или более поздней версии).
2. Установите средство проверки готовности центра Azure Stack. В командной строке PowerShell введите следующую команду: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker -RequiredVersion 1.2002.1133.85 -Force
    ```

    Чтобы проверить установленную версию, введите:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Создайте каталог для всех сертификатов, если он не существует. Тип: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Чтобы создать запрос на сертификат, укажите следующие сведения. При создании VPN-сертификата некоторые из этих входных данных не применяются. 
    
    |Входные данные |Описание  |
    |---------|---------|
    |`OutputRequestPath`|Путь к файлу на локальном клиенте, на котором должны быть созданы запросы на сертификат.        |
    |`DeviceName`|Имя устройства на странице **устройства** в локальном веб-интерфейсе вашего устройства. <br> Это поле не является обязательным для VPN-сертификата.         |
    |`NodeSerialNumber`|Серийный номер узла устройства на странице " **сеть** " в локальном пользовательском веб-интерфейсе вашего устройства. <br> Это поле не является обязательным для VPN-сертификата.       |
    |`ExternalFQDN`|Значение Днсдомаин на странице **устройства** в локальном веб-интерфейсе вашего устройства.         |
    |`RequestType`|Тип запроса может быть для `MultipleCSR` разных сертификатов для различных конечных точек, или `SingleCSR` для всех конечных точек — один сертификат. <br> Это поле не является обязательным для VPN-сертификата.     |

    Для всех сертификатов, кроме VPN-сертификата, введите: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    При создании VPN-сертификата введите: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. Файлы запросов сертификатов находятся в каталоге, указанном в приведенном выше параметре Аутпутрекуестпас. При использовании `MultipleCSR` параметра вы увидите 4 файла с `.req` расширением. Используются следующие файлы:

    
    |Имена файлов  |Тип запроса на сертификат  |
    |---------|---------|
    |Начиная с `DeviceName`     |Локальный запрос сертификата веб-интерфейса      |
    |Начиная с `NodeSerialNumber`     |Запрос сертификата узла         |
    |Начиная с `login`     |Запрос сертификата конечной точки Azure Resource Manager       |
    |Начиная с `wildcard`     |Запрос сертификата хранилища BLOB-объектов; Он содержит подстановочный знак, так как он охватывает все учетные записи хранения, которые можно создать на устройстве.          |
    |Начиная с `AzureStackEdgeVPNCertificate`     |Запрос сертификата VPN-клиента.         |

    Вы также видите папку INF. Он содержит файл сведений об управлении. <ребр-DeviceName> в виде открытого текста, поясняющего сведения о сертификате.  


6. Отправьте эти файлы в центр сертификации (как внутренний, так и общедоступный). Убедитесь, что ваш ЦС создает сертификаты с помощью созданного запроса, удовлетворяющего требованиям к сертификатам [узла](azure-stack-edge-j-series-manage-certificates.md#node-certificates), [конечным точкам](azure-stack-edge-j-series-manage-certificates.md#endpoint-certificates)и сертификатам [локального пользовательского интерфейса](azure-stack-edge-j-series-manage-certificates.md#local-ui-certificates), которые удовлетворяют требования к Azure Stackу сертификатов.

## <a name="prepare-certificates-for-deployment"></a>Подготовка сертификатов к развертыванию

Файлы сертификатов, получаемые от центра сертификации (ЦС), должны быть импортированы и экспортированы с помощью свойств, соответствующих требованиям к сертификатам пограничных устройств Azure Stack. Выполните следующие действия в той же системе, в которой были созданы запросы подписи сертификата.

- Чтобы импортировать сертификаты, выполните действия, описанные в статье [Импорт сертификатов на клиентах, обращающихся к Azure Stack пограничному устройству](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

- Чтобы экспортировать сертификаты, выполните действия, описанные в разделе [Экспорт сертификатов с клиента, обращающегося к Azure Stack пограничному устройству](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).


## <a name="validate-certificates"></a>Проверка сертификатов

Во-первых, вы создадите правильную структуру папок и поместите сертификаты в соответствующие папки. Только после этого вы проверите сертификаты с помощью средства.

1. Запустите оболочку PowerShell от имени администратора.

2. Чтобы создать соответствующую структуру папок, введите в командной строке:

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Преобразуйте пароль PFX в защищенную строку. Тип:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Затем проверьте сертификаты. Тип:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Дальнейшие действия

[Развертывание Azure Stack пограничной устройства](azure-stack-edge-gpu-deploy-prep.md)
