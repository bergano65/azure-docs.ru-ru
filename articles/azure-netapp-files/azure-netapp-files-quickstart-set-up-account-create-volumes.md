---
title: Краткое руководство. Настройка службы Azure NetApp Files и создание тома NFS | Документация Майкрософт
description: Краткое руководство. Описание быстрой настройки Azure NetApp Files и создания тома.
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
ms.topic: quickstart
ms.date: 4/16/2019
ms.author: b-juche
ms.openlocfilehash: 2bcd8163cb3c6071812d4d247b5b333edcfc89e5
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523028"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Краткое руководство. Настройка Azure NetApp Files и создание тома NFS 

В этой статье описывается, как быстро настроить Azure NetApp Files и создать том. 

В этом кратком руководстве описывается настройка следующих элементов:

- регистрации для использования Azure NetApp Files и поставщика ресурсов NetApp;
- учетной записи NetApp;
- пула емкости;
- тома NFS для Azure NetApp Files.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="before-you-begin"></a>Перед началом работы 

> [!IMPORTANT] 
> Необходимо иметь доступ к службе Azure NetApp Files.  Запросить доступ к службе можно на [странице заявки на доступ к Azure NetApp Files](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u).  Перед продолжением необходимо дождаться официального подтверждения по электронной почте от команды Azure NetApp Files. 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Регистрация для использования Azure NetApp Files и поставщика ресурсов NetApp

1. На портале Azure щелкните значок Azure Cloud Shell в верхнем правом углу.

    ![значок Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell-window.png)

2. Укажите подписку, которая была добавлена в список разрешений для Azure NetApp Files.
    
        az account set --subscription <subscriptionId>

3. Зарегистрируйте поставщик ресурсов Azure. 
    
        az provider register --namespace Microsoft.NetApp --wait  

    Процесс регистрации может занять некоторое время.

## <a name="create-a-netapp-account"></a>Создание учетной записи NetApp

1. В поле поиска на портале Azure введите **Azure NetApp Files**, затем выберите **Azure NetApp Files (preview)** из появившегося списка.

      ![Выбор Azure NetApp Files](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Нажмите кнопку **+ Добавить** для создания новой учетной записи NetApp.

     ![Создание учетной записи NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. В окне "New NetApp Account" (Новая учетная запись NetApp) укажите следующую информацию. 
   1. Введите имя учетной записи **myaccount1**. 
   2. Выберите свою подписку.
   3. Выберите **Создать**, чтобы создать группу ресурсов. Введите имя группы ресурсов **myRG1**. Последовательно выберите **ОК**. 
   4. Выберите расположение учетной записи.  

      ![Окно создания учетной записи NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Окно группы ресурсов](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Нажмите кнопку **Создать**, чтобы создать учетную запись NetApp.

## <a name="set-up-a-capacity-pool"></a>Настройка пула емкости

1. В колонке управления Azure NetApp Files выберите свою учетную запись NetApp (**myaccount1**).

    ![Выбор учетной записи NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. В колонке управления Azure NetApp Files для своей учетной записи NetApp щелкните **Пулы емкости**.

    ![Выбор "Пулы емкости"](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Щелкните **+ Добавить пулы**. 

    ![Выбор "+ Добавить пулы"](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Укажите сведения о пуле емкости. 
    1. Введите имя пула **mypool1**.
    2. Выберите уровень обслуживания **Премиум**. 
    3. Укажите размер пула **4 (Тиб)** . 

5. Последовательно выберите **ОК**.

## <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Создание тома NFS для Azure NetApp Files

1. В колонке управления Azure NetApp Files для своей учетной записи NetApp щелкните **Тома**.

    ![Выбор "Тома"](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Щелкните **+ Add volume** (+ Добавить том).

    ![Выбор "+ Добавить том"](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. В окне "Создать том" укажите сведения о томе. 
   1. Введите имя тома **myvol1**. 
   3. Выберите пул емкости (**mypool1**).
   4. Используйте значение квоты по умолчанию. 
   5. В разделе "Виртуальная сеть" щелкните **Создать**, чтобы создать виртуальную сеть Azure.  Затем укажите следующие сведения.
       * Введите имя виртуальной сети **myvnet1**.
       * Укажите диапазон адресов для параметра, например 10.7.0.0/16.
       * Введите имя подсети **myANFsubnet**.
       * Укажите диапазон адресов подсети, например 10.7.0.0/24. Обратите внимание на то, что невозможно использовать выделенную подсеть совместно с другими ресурсами.
       * Для делегирования подсети выберите **Microsoft.NetApp/volumes**.
       * Нажмите кнопку **ОК**, чтобы создать виртуальную сеть.
   6. В разделе "Подсеть" выберите созданную виртуальную сеть (**myvnet1**) в качестве делегируемой подсети.

      ![Окно создания тома](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Окно создания виртуальной сети](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Нажмите кнопку **Протокол**, а затем для типа протокола тома выберите **NFS**.   

    Укажите путь к файлу (**myfilepath1**), который будет использоваться для создания пути экспорта для этого тома. 

    ![Чтобы сразу же начать работу, выберите протокол NFS](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Щелкните **Review + create** (Просмотреть и создать).

    ![Окно просмотра и создания](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Ознакомьтесь с информацией о томе, а затем щелкните **Создать**.  
    Созданный том появится в колонке "Тома".

    ![Созданный том](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="clean-up-resources"></a>Очистка ресурсов

По окончании группу ресурсов можно при необходимости удалить. Удаление группы ресурсов — необратимая операция.  

> [!IMPORTANT]
> Все ресурсы в группе ресурсов будут окончательно удалены, и отменить эту операцию невозможно. 

1. В поле поиска на портале Azure введите **Azure NetApp Files**, затем выберите **Azure NetApp Files** из появившегося списка.

2. В списке подписок щелкните группу ресурсов (myRG1), которую необходимо удалить. 

    ![Переход к группе ресурсов](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. На странице группы ресурсов выберите команду **Удалить группу ресурсов**.

    ![Удалить группу ресурсов](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Откроется окно с предупреждением о ресурсах, которые будут удалены вместе с группой ресурсов.

4. Введите имя группы ресурсов (myRG1) для подтверждения того, что вы хотите окончательно удалить группу ресурсов и все входящие в нее ресурсы, а затем нажмите кнопку **Удалить**.

    ![Удалить группу ресурсов](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

## <a name="next-steps"></a>Дополнительная информация  

> [!div class="nextstepaction"]
> [Управление томами с помощью Azure NetApp Files](azure-netapp-files-manage-volumes.md)  
