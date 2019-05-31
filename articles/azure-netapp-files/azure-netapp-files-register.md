---
title: Регистрация в службе Azure NetApp Files | Документация Майкрософт
description: В этой статье описывается регистрация для использования Azure NetApp Files.
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
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299218"
---
# <a name="register-for-azure-netapp-files"></a>Регистрация в службе Azure NetApp Files

> [!IMPORTANT] 
> Перед регистрацией поставщика ресурсов службы файлов Azure NetApp, вы должны получили сообщение электронной почты от команды Azure NetApp файлы, подтверждающее, что вам был предоставлен доступ к службе. 

В этой статье сведения о регистрации для службы файлов Azure NetApp, таким образом, вы можете использовать службу.

## <a name="waitlist"></a>Отправить запрос ожидания помещается доступа к службе

1. Отправить запрос на доступ к службе файлов Azure NetApp через ожидания помещается [страницу отправки файлов NetApp Azure ожидания помещается](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    Ожидания помещается регистрации не гарантирует доступ к службе немедленно. 

2. Дождитесь официальное подтверждение по электронной почте от команды Azure NetApp файлы перед продолжением работы с другими задачами. 

## <a name="resource-provider"></a>Регистрация поставщика ресурсов NetApp

Чтобы использовать службу, необходимо зарегистрировать поставщика ресурсов Azure в Azure NetApp Files.

> [!NOTE] 
> Можно успешно зарегистрировать поставщик ресурсов NetApp даже без предоставления им доступа для службы. Тем не менее без авторизации доступа любого портала Azure или API запрос на создание учетной записи NetApp или любого другого ресурса службы файлов Azure NetApp будет отклонен со следующей ошибкой:  
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

    Если вы не видите имя компонента `Microsoft.NetApp/ANFGA`, у вас нет доступа к службе. На этом шаге останавливается. Следуйте инструкциям в [ожидания помещается запрос доступа к службе](#waitlist) для запроса доступа к службе, прежде чем продолжить. 

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


## <a name="next-steps"></a>Дальнейшие действия

[Создание учетной записи NetApp](azure-netapp-files-create-netapp-account.md)