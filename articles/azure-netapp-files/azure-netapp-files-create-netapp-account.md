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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 47b9d25f8db2241bb578528780e28f43d56371e5
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963086"
---
# <a name="create-a-netapp-account"></a>Создание учетной записи NetApp
Создание учетной записи NetApp предоставляет возможность настройки пула емкости и впоследствии создания тома. Чтобы создать новую учетную запись NetApp, нужно использовать колонку Azure NetApp Files.

## <a name="before-you-begin"></a>Перед началом работы
Необходимо зарегистрировать подписку для использования поставщика ресурсов NetApp и общедоступной предварительной версии компонента.

[Register for Azure NetApp Files](azure-netapp-files-register.md) (Регистрация в службе Azure NetApp Files).

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
  *  **Подписка**  
    Выберите подписку из существующих подписок.
  * **Группа ресурсов**   
    Используйте имеющуюся группу ресурсов или создайте новую.
  * **Местоположение.**  
    Выберите регион, где нужно разместить учетную запись и ее дочерние ресурсы.  

    ![Новая учетная запись NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Нажмите кнопку **Создать**.     
  Созданная учетная запись NetApp теперь отображается в колонке Azure NetApp Files. 

## <a name="next-steps"></a>Дополнительная информация  

[Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)

