---
title: Регистрация в службе Azure NetApp Files | Документация Майкрософт
description: Сведения о том, как отправить запрос на регистрацию в службе Azure NetApp Files.
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
ms.date: 01/04/2018
ms.author: b-juche
ms.openlocfilehash: 86c016a5dbcc0d78378e59bc6b3606ddf2c54f64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452795"
---
# <a name="register-for-azure-netapp-files"></a>Регистрация в службе Azure NetApp Files
Прежде чем использовать службу Azure NetApp Files, необходимо отправить запрос на регистрацию в ней.  Когда запрос будет утвержден, выполните регистрацию для использования службы.

## <a name="request-to-enroll-in-the-service"></a>Отправка запроса на регистрацию в службе
Требуется участие в программе использования общедоступной предварительной версии и добавление в список разрешений для доступа к поставщику ресурсов Microsoft.NetApp. Дополнительные сведения об участии в этой программе, см. на [страницы регистрации для использования общедоступной предварительной версии Azure NetApp Files](https://aka.ms/nfspublicpreview). 


## <a name="register-the-netapp-resource-provider"></a>Регистрация поставщика ресурсов NetApp

Чтобы использовать службу, необходимо зарегистрировать поставщика ресурсов Azure в Azure NetApp Files. 

1. На портале Azure щелкните значок Azure Cloud Shell в верхнем правом углу:

      ![значок Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Если у вас несколько подписок в учетной записи Azure, выберите ту, которая была добавлена в список разрешений для Azure NetApp Files:
    
        az account set --subscription <subscriptionId>

3. В консоли Azure Cloud Shell введите следующую команду, чтобы проверить добавление подписки в список разрешений:
    
        az feature list | grep NetApp

   Результат команды выглядит следующим образом:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` является идентификатором подписки.

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