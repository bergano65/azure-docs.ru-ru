---
title: Настройка Azure NetApp Files и создание тома | Документация Майкрософт
description: Описывается, как быстро настроить Azure NetApp Files и создать том.
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
ms.date: 2/20/2019
ms.author: b-juche
ms.openlocfilehash: 634f23cf3161fff09f21c79fd8300cb269dcc5b7
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546589"
---
# <a name="set-up-azure-netapp-files-and-create-a-volume"></a>Настройка Azure NetApp Files и создание тома 

В этой статье описывается, как быстро настроить Azure NetApp Files и создать том. 

## <a name="before-you-begin"></a>Перед началом работы 

Требуется участие в программе использования общедоступной предварительной версии и добавление в список разрешений для доступа к поставщику ресурсов Microsoft.NetApp. Дополнительные сведения об участии в этой программе, см. на [страницы регистрации для использования общедоступной предварительной версии Azure NetApp Files](https://aka.ms/nfspublicpreview). 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Регистрация для использования Azure NetApp Files и поставщика ресурсов NetApp

1. На портале Azure щелкните значок Azure Cloud Shell в верхнем правом углу.

      ![значок Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

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
    3. Укажите размер пула **4 (Тиб)**. 

5. Последовательно выберите **ОК**.

## <a name="create-a-volume-for-azure-netapp-files"></a>Создание тома для Azure NetApp Files

1. В колонке управления Azure NetApp Files для своей учетной записи NetApp щелкните **Тома**.

    ![Выбор "Тома"](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Щелкните **+ Add volume** (+ Добавить том).

    ![Выбор "+ Добавить том"](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. В окне "Создать том" укажите сведения о томе. 
   1. Введите имя тома **myvol1**. 
   2. Укажите путь к файлу (**myfilepath1**), который будет использоваться для создания пути экспорта для этого тома.
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

4. Щелкните **Review + create** (Просмотреть и создать).

    ![Окно просмотра и создания](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Ознакомьтесь с информацией о томе, а затем щелкните **Создать**.  
    Созданный том появится в колонке "Тома".

    ![Созданный том](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="next-steps"></a>Дополнительная информация  

* [Общие сведения об иерархии хранилища Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Управление томами с помощью Azure NetApp Files](azure-netapp-files-manage-volumes.md) 
