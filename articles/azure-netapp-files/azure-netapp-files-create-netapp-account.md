---
title: Создание учетной записи NetApp для доступа к Azure NetApp Files | Документация Майкрософт
description: Описание способа получения доступа к Azure NetApp Files и создания учетной записи NetApp, чтобы иметь возможность настраивать пул емкости и создавать том.
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
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 25cae58663f6fa7ef27995c10509eb33e49dd4c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522813"
---
# <a name="create-a-netapp-account"></a>Создание учетной записи NetApp
Создание учетной записи NetApp предоставляет возможность настройки пула емкости и впоследствии создания тома. Чтобы создать новую учетную запись NetApp, нужно использовать колонку Azure NetApp Files.

## <a name="before-you-begin"></a>Перед началом работы
Необходимо, вы получили сообщение электронной почты от команды Azure NetApp файлы, подтверждающее, что вам был предоставлен доступ к службе. См. в разделе [ожидания помещается запрос доступа к службе](azure-netapp-files-register.md#waitlist).

Необходимо также зарегистрировать подписку для поставщика ресурсов NetApp. См. в разделе [зарегистрировать поставщик ресурсов NetApp](azure-netapp-files-register.md#resource-provider).

## <a name="steps"></a>Действия 

1. Войдите на портал Azure. 
2. Подключитесь к колонке Azure NetApp Files, используя один из следующих способов.  
   * Искать **Azure NetApp Files** в поле поиска на портале Azure.  
   * Нажать кнопку **Все службы** в навигации, а затем отфильтровать для Azure NetApp Files.  

   Колонку Azure NetApp Files можно добавить в избранное, щелкнув значок звездочки рядом с ней. 

3. Нажмите кнопку **+ Добавить** для создания новой учетной записи NetApp.  
   Появится окно новой учетной записи NetApp.  

4. Укажите следующие сведения для учетной записи NetApp. 
   * **Имя учетной записи**  
     Укажите уникальное имя для подписки.
   * **Подписка**  
     Выберите подписку из существующих подписок.
   * **Группа ресурсов**   
     Используйте имеющуюся группу ресурсов или создайте новую.
   * **Location**  
     Выберите регион, где нужно разместить учетную запись и ее дочерние ресурсы.  

     ![Новая учетная запись NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Нажмите кнопку **Создать**.     
   Созданная учетная запись NetApp теперь отображается в колонке Azure NetApp Files. 

> [!NOTE] 
> Если вы не имеют доступа к службе файлов Azure NetApp, вы получите следующую ошибку при попытке создать первую учетную запись NetApp:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>Дальнейшие действия  

[Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)

