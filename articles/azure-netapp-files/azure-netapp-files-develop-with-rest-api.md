---
title: Разработка для Azure NetApp Files с помощью REST API | Документация Майкрософт
description: Здесь описано, как приступить к работе с помощью REST API Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: b-juche
ms.openlocfilehash: 996fbcc7c3c9af0da9160216785ecd54840660e8
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957044"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Разработка для Azure NetApp Files с помощью REST API 

REST API для службы Azure NetApp Files определяет операции HTTP в ресурсах, таких как учетная запись NetApp, пул емкости, тома и моментальные снимки. В этой статье описано, как приступить к работе с помощью REST API Azure NetApp Files.

## <a name="azure-netapp-files-rest-api-specification"></a>Azure спецификации NetApp файловый API REST

Спецификации REST API для службы файлов Azure NetApp публикуется через [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager):

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Доступ к REST API Azure NetApp Files  

1. [Установите Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), если это еще не сделано.
2. Создайте субъект-службу в Azure Active Directory (Azure AD).
   1. Убедитесь, что у вас [достаточно разрешений](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

   1. Выполните следующую команду в Azure CLI:  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      Результат команды должен быть примерно таким:  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      Сохраните результат команды.  Вам понадобятся значения `appId`, `password` и `tenant`. 

3. Запросите маркер доступа OAuth.

    В примерах этой статьи используется cURL.  Можно также использовать различные инструменты API, например [Postman](https://www.getpostman.com/), [Insomnia](https://insomnia.rest/) и [Paw](https://paw.cloud/).  

    Замените переменные в следующем примере результатами команды из шага 2 выше. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    Результат будет содержать примерно такой маркер доступа:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    Отображенный маркер действителен в течение 3600 секунд. После этого нужно запросить новый маркер. 
    Сохраните маркер в текстовый редактор.  Он понадобится вам на следующем шаге.

4. Отправьте тестовый вызов и включите маркер, чтобы проверить доступ к REST API:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Примеры в работе с API  

В этой статье для запросов используется приведенный ниже URL-адрес. Этот URL-адрес указывает на корень пространства имен Azure NetApp Files. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

В следующих примерах замените значения `subID` и `resourceGroups` собственными значениями. 

### <a name="get-request-examples"></a>Примеры запросов GET

Используйте запрос GET, чтобы запросить объекты Azure NetApp Files в подписке, как показано в приведенных ниже примерах: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>Примеры запросов PUT

Используйте запрос PUT, чтобы создать новые объекты в Azure NetApp Files, как показано в приведенных ниже примерах. Текст запроса PUT может включать данные в формате JSON для изменений, или в нем может быть указан файл для чтения. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>Примеры JSON

В приведенном ниже примере показано, как создать учетную запись NetApp.

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

В приведенном ниже примере показано, как создать пул емкости. 

    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME"
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }

В приведенном ниже примере показано, как создать новый том. 

    {
        "name": "MYNEWVOLUME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
        "location": "westus2",
        "properties": {
            "serviceLevel": "Premium",
            "usageThreshold": "322122547200",
            "creationToken": "MY-FILEPATH",
            "snapshotId": "",
            "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
            }
    }

В приведенном ниже примере показано, как сделать моментальный снимок тома. 

    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }

> [!NOTE] 
> Для создания моментального снимка необходимо указать `fileSystemId`.  Вы можете получить значение `fileSystemId` с помощью запроса GET к тому. 

## <a name="next-steps"></a>Дальнейшие действия

[См. статью о REST API Azure NetApp Files](https://docs.microsoft.com/rest/api/netapp/)
