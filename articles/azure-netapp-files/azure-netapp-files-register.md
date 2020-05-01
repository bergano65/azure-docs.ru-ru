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
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79274064"
---
# <a name="register-for-azure-netapp-files"></a>Регистрация в службе Azure NetApp Files

> [!IMPORTANT] 
> Перед регистрацией поставщика ресурсов Azure NetApp Files необходимо получить электронное письмо от команды Azure NetApp Files, подтверждая, что вам предоставлен доступ к службе. 

В этой статье вы узнаете, как зарегистрироваться для Azure NetApp Files, чтобы можно было приступить к работе со службой.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Отправка запроса ваитлист для доступа к службе

1. Отправьте запрос ваитлист для доступа к службе Azure NetApp Files на [странице отправки Azure NetApp Files ваитлист](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

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
    
        az account set --subscription <subscriptionId>

3. В консоли Azure Cloud Shell введите следующую команду, чтобы проверить добавление подписки в список разрешений:
    
        az feature list | grep NetApp

   Результат команды выглядит следующим образом:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>` является идентификатором подписки.

    Если имя `Microsoft.NetApp/ANFGA`компонента не отображается, у вас нет доступа к службе. На этом шаге следует закончить работу. Следуйте инструкциям в разделе [Отправка запроса ваитлист](#waitlist) на доступ к службе, чтобы запросить доступ к службе, прежде чем продолжить. 

4. В консоли Azure Cloud Shell введите следующую команду, чтобы зарегистрировать поставщика ресурсов Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait

   Параметр `--wait` указывает консоли ожидать завершения регистрации. Процесс регистрации может занять некоторое время.

5. В консоли Azure Cloud Shell введите следующую команду, чтобы проверить, что поставщик ресурсов Azure был зарегистрирован: 
    
        az provider show --namespace Microsoft.NetApp

   Результат команды выглядит следующим образом:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` является идентификатором подписки.  Для параметра `state` задано значение `Registered`.

6. На портале Azure щелкните колонку **Подписки**.
7. В колонке подписок щелкните идентификатор подписки. 
8. В параметрах подписки щелкните **Поставщики ресурсов**, чтобы убедиться, что для поставщика Microsoft.NetApp отображается состояние "Зарегистрировано": 

      ![Зарегистрированный Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Дальнейшие шаги

[Создание учетной записи NetApp](azure-netapp-files-create-netapp-account.md)