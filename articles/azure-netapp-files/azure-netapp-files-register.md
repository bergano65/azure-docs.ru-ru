---
title: Регистрация в службе Azure NetApp Files | Документация Майкрософт
description: Описывает, как зарегистрироваться для использования файлов NetApp Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274064"
---
# <a name="register-for-azure-netapp-files"></a>Регистрация в службе Azure NetApp Files

> [!IMPORTANT] 
> Прежде чем зарегистрировать сярприжающего ресурса Azure NetApp Files, вы должны получить электронное письмо от команды Azure NetApp Files, подтверждающее, что вам был предоставлен доступ к сервису. 

В этой статье узнайте, как зарегистрироваться для файлов Сети Azure, чтобы вы могли начать пользоваться сервисом.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Отправить запрос листа ожидания для доступа к службе

1. Отправить запрос в список ожидания для доступа к сервису Файлов NetApp Azure через [страницу отправки файлов ожидания Azure NetApp.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u) 

    Регистрация Вожиданиста не гарантирует немедленный доступ к сервису. 

2. Подождите официального подтверждения электронной почты от команды Azure NetApp Files, прежде чем продолжить выполнение других задач. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>Регистрация поставщика ресурсов NetApp

Чтобы использовать службу, необходимо зарегистрировать поставщика ресурсов Azure в Azure NetApp Files.

> [!NOTE] 
> Вы сможете успешно зарегистрировать поставщика ресурсов NetApp даже без доступа к сервису. Однако без авторизации доступа любой портал Azure или API-запрос на создание учетной записи NetApp или любого другого ресурса Azure NetApp Files будет отклонен со следующей ошибкой:  
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

    Если вы не видите `Microsoft.NetApp/ANFGA`имя функции, у вас нет доступа к службе. Остановитесь на этом шаге. Следуйте инструкциям в [Отправить запрос лист ожидания для доступа к службе,](#waitlist) чтобы запросить доступ к службе, прежде чем продолжить. 

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