---
title: Регистрация в службе Azure NetApp Files | Документация Майкрософт
description: Описание процесса регистрации для использования Azure NetApp Files.
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
ms.topic: how-to
ms.date: 06/09/2020
ms.author: b-juche
ms.openlocfilehash: cdb96f08f78e22dd0e46070ab62bf9327e2d72a3
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956310"
---
# <a name="register-for-azure-netapp-files"></a>Регистрация в службе Azure NetApp Files

> [!IMPORTANT] 
> Перед регистрацией поставщика ресурсов Azure NetApp Files необходимо получить электронное письмо от команды Azure NetApp Files, подтверждая, что вам предоставлен доступ к службе. 

В этой статье вы узнаете, как зарегистрироваться для Azure NetApp Files, чтобы можно было приступить к работе со службой.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Отправка запроса ваитлист для доступа к службе

1. Отправьте запрос ваитлист для доступа к службе Azure NetApp Files на [странице отправки Azure NetApp Files ваитлист](https://aka.ms/azurenetappfiles). 

    Ваитлист регистрация не гарантирует немедленного доступа к службе. 

2. Перед продолжением выполнения других задач дождитесь официального сообщения электронной почты от команды Azure NetApp Files. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>Регистрация поставщика ресурсов NetApp

Чтобы использовать службу, необходимо зарегистрировать поставщика ресурсов Azure в Azure NetApp Files.

> [!NOTE] 
> Вы сможете зарегистрировать поставщик ресурсов NetApp даже без предоставления доступа к службе. Однако без авторизации доступа любой портал Azure или запрос API для создания учетной записи NetApp или любого другого Azure NetApp Files ресурса будет отклонен со следующей ошибкой:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. На портале Azure щелкните значок Azure Cloud Shell в верхнем правом углу:

      ![значок Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Если у вас несколько подписок в учетной записи Azure, выберите ту, которая была добавлена в список разрешений для Azure NetApp Files:
    
    ```azurepowershell
    az account set --subscription <subscriptionId>
    ```

3. В консоли Azure Cloud Shell введите следующую команду, чтобы проверить добавление подписки в список разрешений:
    
    ```azurepowershell
    az feature list | grep NetApp
    ```

   Результат команды выглядит следующим образом:
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>` является идентификатором подписки.

    Если имя компонента не отображается `Microsoft.NetApp/ANFGA` , у вас нет доступа к службе. На этом шаге следует закончить работу. Следуйте инструкциям в разделе [Отправка запроса ваитлист](#waitlist) на доступ к службе, чтобы запросить доступ к службе, прежде чем продолжить. 

4. В консоли Azure Cloud Shell введите следующую команду, чтобы зарегистрировать поставщика ресурсов Azure: 
    
    ```azurepowershell
    az provider register --namespace Microsoft.NetApp --wait
    ```

   Параметр `--wait` указывает консоли ожидать завершения регистрации. Процесс регистрации может занять некоторое время.

5. В консоли Azure Cloud Shell введите следующую команду, чтобы проверить, что поставщик ресурсов Azure был зарегистрирован: 
    
    ```azurepowershell
    az provider show --namespace Microsoft.NetApp
    ```

   Результат команды выглядит следующим образом:
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>` является идентификатором подписки.  Для параметра `state` задано значение `Registered`.

6. На портале Azure щелкните колонку **Подписки**.
7. В колонке подписок щелкните идентификатор подписки. 
8. В параметрах подписки щелкните **Поставщики ресурсов**, чтобы убедиться, что для поставщика Microsoft.NetApp отображается состояние "Зарегистрировано": 

      ![Зарегистрированный Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Дальнейшие шаги

[Создание учетной записи NetApp](azure-netapp-files-create-netapp-account.md)
