---
title: Перемещение данных хранилища больших двоичных объектов с помощью обозревателя службы хранилища Azure — командный процесс обработки и анализа данных
description: Узнайте, как использовать Обозреватель службы хранилища Azure для отправки и скачивания данных из хранилища BLOB-объектов Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bfc63c6f5aca92fb7fda9e3ecf63ce4c332b12ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "76720917"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Перемещение данных в хранилище BLOB-объектов Azure и из него с помощью Azure Storage Explorer
Azure Storage Explorer — это бесплатный инструмент от корпорации Майкрософт, позволяющий визуально работать с данными из службы хранилища Azure на платформе Windows, MacOS и Linux. В этом разделе описывается, как использовать этот инструмент для передачи и скачивания данных из хранилища BLOB-объектов Azure. Этот инструмент можно скачать с сайта [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Если используется виртуальная машина, созданная с помощью скриптов, предоставленных [виртуальными машинами для обработки и анализа данных в Azure](virtual-machines.md), то обозреватель хранилищ Azure уже установлен на виртуальной машине.
> 
> [!NOTE]
> Полное описание базовых принципов использования хранилища BLOB-объектов Azure см. в статьях [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) и [Основные понятия службы BLOB-объектов](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Предварительные требования
Для выполнения указаний в этом документе у вас должна быть подписка Azure, учетная запись хранения и соответствующий ключ к хранилищу данных для этой учетной записи. Перед отправкой и загрузкой данных необходимо указать имя учетной записи хранения Azure и ключ учетной записи. 

* Сведения о настройке подписки Azure см. в [статье Бесплатная пробная версия на один месяц](https://azure.microsoft.com/pricing/free-trial/).
* Инструкции по созданию учетной записи хранения и сведениям об учетных записях и ключах [см. в](../../storage/common/storage-create-storage-account.md)этой статье. Запишите ключ доступа для учетной записи хранения. Он необходим для подключения к учетной записи с помощью инструмента Azure Storage Explorer.
* Инструмент Azure Storage Explorer можно скачать с сайта [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Во время установки примите значения по умолчанию.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Использование обозревателя службы хранилища Azure
Ниже рассказывается о том, как отправить или скачать данные с помощью обозревателя хранилищ Azure. 

1. Запустите Microsoft Azure Storage Explorer.
2. Чтобы отобразить мастер **Sign in to your account…** (Вход в учетную запись…), щелкните значок **Azure account settings (Параметры учетной записи Azure)**, а затем выберите **Add an account** (Добавить учетную запись) и введите свои учетные данные. 
![Добавление учетной записи хранения Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Чтобы открыть мастер подключения к службе **хранилища Azure** , щелкните значок **Подключение к службе хранилища Azure** . ![Щелкните "подключиться к службе хранилища Azure".](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Введите ключ доступа из учетной записи хранения Azure в мастере **подключения к хранилищу Azure** , а затем нажмите **Далее**. ![Введите ключ доступа из учетной записи хранения Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Введите имя учетной записи хранения в поле **Account name** (Имя учетной записи) и нажмите кнопку **Далее**. ![Подключение внешнего хранилища](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Теперь должна отобразиться добавленная учетная запись хранения. Чтобы создать контейнер больших двоичных объектов в учетной записи хранения, щелкните правой кнопкой мыши узел **Blob Containers** (Контейнеры больших двоичных объектов) в этой учетной записи, выберите **Create Blob Container** (Создать контейнер BLOB-объектов) и введите имя.
7. Чтобы передать данные в контейнер, выберите целевой контейнер и нажмите кнопку **Передать**.
![Учетные записи хранения](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Щелкните знак многоточия **…** справа от поля **Файлы**, выберите в файловой системе один или несколько файлов для передачи и нажмите кнопку **Отправить**, чтобы начать их передачу. ![Передача файлов](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Чтобы скачать данные, выберите большой двоичный объект в соответствующем контейнере и нажмите кнопку **Download**(Скачать). ![Скачивание файлов](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

