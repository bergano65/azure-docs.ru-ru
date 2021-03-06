---
title: Загрузка VHD-файла в Azure DevTest Labs с помощью обозревателя хранилищ Microsoft Azure | Документация Майкрософт
description: Загрузка VHD-файла в учетную запись хранения лаборатории с помощью обозревателя хранилищ Microsoft Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 3c187d104334fe75ec9e0ce41a3fdc14b508dfb2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60623429"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Загрузка VHD-файла в учетную запись хранения лаборатории с помощью обозревателя хранилищ Microsoft Azure

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

В Azure DevTest Labs можно использовать VHD-файлы для создания пользовательских образов, которые используются при подготовке виртуальных машин. В этой статье описывается использование [обозревателя хранилищ Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) для передачи VHD-файла в учетную запись хранения лаборатории. После отправки VHD-файла переходите к разделу [Дальнейшие действия](#next-steps), где перечислены несколько статей, в которых описывается создание пользовательского образа из загруженного VHD-файла. Дополнительные сведения о дисках и VHD в Azure см. в статье [Обзор компонента "Управляемые диски" Azure](../virtual-machines/linux/managed-disks-overview.md).

## <a name="step-by-step-instructions"></a>Пошаговые инструкции

Следующие шаги показывают, как можно отправить VHD-файл в DevTest Labs с помощью [обозревателя хранилищ Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Скачайте и установите последнюю версию обозревателя хранилищ Microsoft Azure](https://www.storageexplorer.com).

1. Получите имя учетной записи хранения лаборатории с помощью портала Azure.

    1. Войдите на [портале Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Щелкните **Все службы** и выберите в списке **DevTest Labs**.
    
    1. Из списка лабораторий выберите нужную лабораторию.  
    
    1. В колонке лаборатории выберите **Конфигурация**. 
    
    1. В колонке **Configuration** (Конфигурация) лаборатории выберите **Custom images (VHDs)** (Пользовательские образы (VHD)).
    
    1. В колонке **Custom images** (Пользовательские образы) выберите **+Add** (+ Добавить). 
    
    1. В колонке **Custom images** (Пользовательские образы) выберите **VHD**.
    
    1. В колонке **VHD** щелкните **Upload a VHD file using PowerShell** (Отправить VHD-файл с помощью PowerShell).
    
        ![Отправка VHD с помощью PowerShell][0]
    
    1. Колонка **Upload an image using PowerShell** (Отправка образа с помощью PowerShell) отображает вызов командлета **Add-AzureVhd**. Первый параметр (*Destination* (Назначение)) содержит имя учетной записи хранения для лаборатории в следующем формате:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Запишите имя учетной записи хранения, оно понадобится вам дальше.
    
1. Подключитесь к учетной записи подписки Azure с помощью обозревателя хранилищ.

    > [!TIP] 
    > 
    > Обозреватель хранилищ поддерживает несколько вариантов подключения. В этом разделе показано подключение к учетной записи хранения, связанной с вашей подпиской Azure. Другие варианты подключения, поддерживаемые обозревателем хранилищ, см. в статье [Приступая к работе с обозревателем службы хранилища (предварительная версия)](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Откройте обозреватель хранилищ.
    
    1. В обозревателе хранилищ выберите раздел **Параметры учетной записи Azure**. 
    
        ![Параметры учетной записи Azure][1]
    
    1. На панели слева отображаются учетные записи Майкрософт, в которые вы вошли. Чтобы подключиться к другой учетной записи, выберите **Добавить учетную запись**. Затем выполните инструкции в диалоговых окнах, чтобы войти в учетную запись Майкрософт, связанную минимум с одной активной подпиской Azure.
    
        ![Добавить учетную запись][2]
    
    1. Выполнив вход с помощью учетной записи Майкрософт, на панели слева вы увидите подписки Azure, связанные с этой учетной записью. Выберите подписки Azure, с которыми вы хотите работать, а затем щелкните **Применить**. (Чтобы выбрать все подписки, установите флажок **Все подписки**. Чтобы отменить выбор, снимите этот флажок.)
    
        ![Выбор подписок Azure][3]
    
    1. На панели слева отобразятся все учетные записи хранения, связанные с выбранными подписками Azure.
    
        ![Выбранные подписки Azure][4]

1. Найдите учетную запись хранения лаборатории:

    1. На панели слева в обозревателе хранилищ найдите и разверните узел подписки Azure, которой принадлежит лаборатории.
    
    1. В этом узле подписки разверните узел **Учетные записи хранения**.

    1. Разверните узел учетной записи хранения лаборатории, где вы увидите узлы **Контейнеры больших двоичных объектов**, **Общие файловые ресурсы**, **Очереди** и **Таблицы**.
    
    1. Разверните узел **Контейнеры**.
    
    1. Выберите контейнер больших двоичных объектов uploads. В области справа отобразится его содержимое.
        
        ![Каталог для отправки][5]

1. Отправьте VHD-файл с помощью обозревателя хранилищ.

    1. На панели справа в обозревателе хранилищ вы увидите список больших двоичных объектов, расположенных в соответствующем контейнере **uploads** в учетной записи хранения лаборатории. На панели инструментов редактора больших двоичных объектов выберите **Отправить**. 
        
        ![Кнопка "Отправить"][6]
    
    1. В раскрывающемся меню **отправки** выберите пункт **Отправка файлов**.
    
    1. В диалоговом окне **Отправка файлов** нажмите кнопку с многоточием.
        
        ![Выбор файла][8]  

    1. В диалоговом окне **Выбор файлов для отправки** перейдите к нужному VHD-файлу, выберите его и нажмите **Открыть**.
    
    1. Вы вернетесь в диалоговое окно **Отправка файлов**. Измените здесь **тип большого двоичного объекта**, указав значение **Страничный BLOB-объект**.
    
    1. Щелкните **Отправить**.

        ![Выбор файла][9]  
    
    1. Обозреватель хранилищ отображает на панели **Журнал действий** состояние загрузки (а также ссылки для отмены отправки). В зависимости от размера VHD-файла и скорости подключения отправка файла может быть продолжительной. 

        ![Статус отправки файлов][10]  

## <a name="next-steps"></a>Дальнейшие действия

- [Управление пользовательскими образами Azure DevTest Labs для создания виртуальных машин](devtest-lab-create-template.md)
- [Создание пользовательского образа из VHD-файла с помощью PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
