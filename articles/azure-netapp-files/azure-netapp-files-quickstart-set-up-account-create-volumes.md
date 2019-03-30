---
title: Настройка файлов NetApp Azure и создайте том | Документация Майкрософт
description: Описывает, как быстро настроить NetApp службы файлов Azure и создать том.
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
ms.topic: quickstarts
ms.date: 2/20/2019
ms.author: b-juche
ms.openlocfilehash: e2b9b3cdcb712fcf6c415f574dc687e80ae9ee3b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660517"
---
# <a name="set-up-azure-netapp-files-and-create-a-volume"></a>Настройка Azure NetApp Files и создание тома 

В этой статье показано, как быстро настроить файлы NetApp Azure и создайте том. 

## <a name="before-you-begin"></a>Перед началом работы 

Требуется участие в программе использования общедоступной предварительной версии и добавление в список разрешений для доступа к поставщику ресурсов Microsoft.NetApp. Дополнительные сведения об участии в этой программе, см. на [страницы регистрации для использования общедоступной предварительной версии Azure NetApp Files](https://aka.ms/nfspublicpreview). 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Регистрация для Azure NetApp файлов и поставщика ресурсов NetApp

1. На портале Azure щелкните значок Azure Cloud Shell в правом верхнем углу.

      ![значок Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Укажите подписку, которая была добавлена в список разрешений для файлов Azure NetApp:
    
        az account set --subscription <subscriptionId>

3. Зарегистрируйте поставщик ресурсов Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait  

    Процесс регистрации может занять некоторое время.

## <a name="create-a-netapp-account"></a>Создание учетной записи NetApp

1. В поле поиска на портале Azure, введите **файлов Azure NetApp** , а затем выберите **NetApp службы файлов Azure (Предварительная версия)** из появившегося списка.

      ![Выберите файлы Azure NetApp](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Нажмите кнопку **+ Добавить** для создания новой учетной записи NetApp.

     ![Создать новую учетную запись NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. В окне новой учетной записи NetApp укажите следующие сведения: 
   1. Введите **myaccount1** имя учетной записи. 
   2. Выберите свою подписку.
   3. Выберите **создать** для создания новой группы ресурсов. Введите **myRG1** для имени группы ресурсов. Последовательно выберите **ОК**. 
   4. Выберите страну учетной записи.  

      ![Новое окно учетной записи NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Окно группы ресурсов](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Нажмите кнопку **создать** для создания новой учетной записи NetApp.

## <a name="set-up-a-capacity-pool"></a>Настройка пула емкости

1. В колонке управления файлов NetApp Azure выберите свою учетную запись NetApp (**myaccount1**).

    ![Выберите учетную запись NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. В колонке управления службой файлов Azure NetApp учетной записи NetApp, щелкните **пулов емкости**.

    ![Нажмите кнопку пулов емкости](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Нажмите кнопку **+ Добавить пулы**. 

    ![Нажмите кнопку Добавить пулы](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Содержатся сведения о емкости пула. 
    1. Введите **mypool1** именем пула.
    2. Выберите **уровня "премиум"** уровня обслуживания. 
    3. Укажите **4 (Тиб)** как размер пула. 

5. Последовательно выберите **ОК**.

## <a name="create-a-volume-for-azure-netapp-files"></a>Создание тома для Azure NetApp Files

1. В колонке управления службой файлов Azure NetApp учетной записи NetApp, щелкните **тома**.

    ![Щелкните "тома"](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Щелкните **+ Add volume** (+ Добавить том).

    ![Нажмите кнопку Добавить тома](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. В создание окна тома укажите сведения для тома. 
   1. Введите **myvol1** как имя тома. 
   2. Введите **myfilepath1** как путь к файлу, который будет использоваться для создания пути экспорта для тома.
   3. Выберите пул ресурсов (**mypool1**).
   4. Используйте значение по умолчанию для квоты. 
   5. В виртуальной сети щелкните **создать** для создания новой виртуальной сети Azure (Vnet).  Затем укажите следующие сведения:
       * Введите **myvnet1** как имя виртуальной сети.
       * Укажите адресное пространство для параметров, например, 10.7.0.0/16
       * Введите **myANFsubnet** как имя подсети.
       * Укажите диапазон адресов подсети, к примеру, 10.7.0.0/24. Обратите внимание на то, что нельзя совместно использовать выделенную подсеть с другими ресурсами.
       * Выберите **Microsoft.NetApp/volumes** для делегирования подсети.
       * Нажмите кнопку **ОК** для создания виртуальной сети.
   6. В подсети, выберите созданную виртуальную сеть (**myvnet1**) как подсеть делегата.

      ![Создать окно тома](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Создать окно виртуальной сети](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Щелкните **Review + create** (Просмотреть и создать).

    ![Просмотр и создание окна](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Ознакомьтесь с информацией о том, а затем щелкните **создать**.  
    В колонке "тома" появится созданный том.

    ![Тома, созданные](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="next-steps"></a>Дальнейшие действия  

* [Общие сведения об иерархии хранилища Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Управление томами с помощью службы файлов Azure NetApp](azure-netapp-files-manage-volumes.md) 
