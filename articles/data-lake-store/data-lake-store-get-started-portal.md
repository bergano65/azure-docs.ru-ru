---
title: Начало работы с Azure Data Lake Storage 1-го поколения-порталом
description: Используйте портал Azure для создания учетной записи Data Lake Storage 1-го поколения и выполнения основных операций в учетной записи.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: e5a3df82fae9d81a870d3dba929bc7e4cd4123d2
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904696"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Начало работы с Azure Data Lake Storage Gen1 с помощью портала Azure

> [!div class="op_single_selector"]
> * [Портал](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Интерфейс командной строки Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Узнайте, как использовать портал Azure для создания учетной записи Data Lake Storage 1-го поколения и выполнения основных операций, таких как создание папок, отправка и скачивание файлов данных, удаление учетной записи и т. д. Дополнительные сведения см. в разделе Общие сведения о [Azure Data Lake Storage 1-го поколения](data-lake-store-overview.md).

## <a name="prerequisites"></a>предварительным требованиям

Перед началом работы с этим учебником необходимо иметь следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Создание учетной записи Data Lake Storage 1-го поколения

1. Войдите на новый [портал Azure](https://portal.azure.com).
2. Последовательно выберите **Создать ресурс > Хранилище > Data Lake Storage 1-го поколения**.
3. В колонке **Новая учетная запись Data Lake Storage 1-го поколения** задайте значения, как показано на следующем снимке экрана:

    ![Создание новой учетной записи Data Lake Storage 1-го поколения](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Создание новой учетной записи Data Lake Storage 1-го поколения")

   * **Имя**. Введите уникальное имя учетной записи Azure Data Lake Storage 1-го поколения (ADLS 1-го поколения).
   * **Подписка**. Выберите подписку, в которой нужно создать учетную запись ADLS 1-го поколения.
   * **Создать группу ресурсов**, чтобы создать новую. Выберите существующую группу ресурсов или щелкните **Создать**, чтобы создать новую. Дополнительные сведения см. Дополнительные сведения см. в разделе [Группы ресурсов](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Расположение**: выберите расположение, в котором нужно создать учетную запись ADLS 1-го поколения.
   * **Параметры шифрования**. Доступны три параметра:

     * **Не включать шифрование**.
     * Для управления ключами шифрования с помощью ADLS 1-го поколения выберите пункт **Использовать ключи, управляемые Data Lake Storage 1-го поколения**.
     * **Использовать ключи из собственного хранилища ключей**. Вы можете выбрать существующее хранилище Azure Key Vault или создать новое. Чтобы использовать ключи из хранилища Key Vault, учетной записи ADLS 1-го поколения необходимо назначить разрешения на доступ к хранилищу Azure Key Vault. Инструкции см. в разделе, посвященном [назначению разрешений для Azure Key Vault](#assign-permissions-to-azure-key-vault).

        ![Шифрование Data Lake Storage 1-го поколения](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Шифрование Data Lake Storage 1-го поколения")

        Нажмите кнопку **ОК** в колонке **Параметры шифрования**.

        Дополнительные сведения см. в статье о [шифровании данных в Azure Data Lake Storage 1-го поколения](./data-lake-store-encryption.md).

4. Нажмите кнопку **Создать**. Если вы закрепили учетную запись на панели мониторинга, вы вернетесь на панель мониторинга, где сможете следить за ходом подготовки учетной записи ADLS 1-го поколения. После подготовки учетной записи ADLS 1-го поколения появится колонка учетной записи.

## <a name="assign-permissions-to-azure-key-vault"></a> Назначение разрешений для Azure Key Vault

Если для настройки шифрования в учетной записи ADLS 1-го поколения использовались ключи из Azure Key Vault, учетной записи ADLS 1-го поколения необходимо назначить разрешения на доступ к хранилищу Azure Key Vault. Для этого выполните следующие действия.

1. Если вы использовали ключи из Azure Key Vault, вверху колонки учетной записи ADLS 1-го поколения отображается предупреждение. Щелкните предупреждение, чтобы открыть колонку **Шифрование**.

    ![Шифрование Data Lake Storage 1-го поколения](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Шифрование Data Lake Storage 1-го поколения")
2. В колонке доступно два варианта настройки доступа.

    ![Шифрование Data Lake Storage 1-го поколения](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Шифрование Data Lake Storage 1-го поколения")

   * В первом случае, чтобы настроить доступ, щелкните **Предоставить разрешения**. Этот вариант возможен, только если пользователь, который создал учетную запись ADLS 1-го поколения, также является администратором Azure Key Vault.
   * Другой вариант — выполнить командлет PowerShell, отображаемый в колонке. В этом случае нужно быть владельцем хранилища ключей Azure или иметь возможность предоставлять разрешения на него. Выполнив командлет, вернитесь в колонку и нажмите кнопку **Включить** для настройки доступа.

> [!NOTE]
> Вы можете также создать учетную запись ADLS 1-го поколения, используя шаблоны Azure Resource Manager. Эти шаблоны доступны на сайте [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store).
> * Шаблон без шифрования: [Deploy Azure Data Lake Store with no data encryption](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/) (Развертывание Azure Data Lake Store без шифрования данных).
> * С шифрованием данных с помощью ADLS 1-го поколения: [Deploy Data Lake Store account with encryption(Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/) (Развертывание учетной записи Data Lake Store с шифрованием данных (Data Lake)).
> * С шифрованием данных с помощью Azure Key Vault: [Deploy Data Lake Store account with encryption(Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/) (Развертывание учетной записи Data Lake Store с шифрованием данных (Key Vault)).
>
>

## <a name="createfolder"></a>Создание папок

Чтобы хранить данные и управлять ими, вы можете создать папки в своей учетной записи ADLS 1-го поколения.

1. Откройте созданную учетную запись ADLS 1-го поколения. В левой области щелкните **все ресурсы**, а затем в колонке **все ресурсы** щелкните имя учетной записи, в которой нужно создать папки. Если учетная запись была закреплена на начальной панели, щелкните элемент этой учетной записи.
2. В колонке учетной записи Data Lake Storage 1-го поколения щелкните **Обозреватель данных**.

    ![Создание папок в учетной записи Data Lake Storage 1-го поколения](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Создание папок в учетной записи Data Lake Storage 1-го поколения")
3. В колонке обозревателя данных щелкните **Создать папку**, введите имя новой папки и нажмите кнопку **ОК**.

    ![Создание папок в учетной записи Data Lake Storage 1-го поколения](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Создание папок в учетной записи Data Lake Storage 1-го поколения")

    Созданная папка появится в колонке **Обозреватель данных**. Вы можете создавать вложенные папки любого уровня.

    ![Создание папок в учетной записи Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Создание папок в учетной записи Data Lake")

## <a name="uploaddata"></a>Передача данных

Данные можно передавать в учетную запись Data Lake Storage 1-го поколения непосредственно на корневом уровне или в папку, созданную в учетной записи.

1. В колонке **Обозреватель данных** щелкните **Отправить**.
2. В колонке **Отправить файлы** перейдите к файлам, которые необходимо отправить, а затем выберите **Добавить выбранные файлы**. Можно также выбрать несколько файлов для отправки.

    ![Передача данных](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Отправка данных")

Если у вас нет под рукой подходящих для этих целей данных, передайте папку **Ambulance Data** из [репозитория Git для озера данных Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="properties"></a>Доступные действия с сохраненными данными

Щелкните значок с многоточием рядом с файлом, а также из всплывающего меню выберите действие, которое требуется выполнить с данными.

![Свойства данных](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Свойства данных")

## <a name="secure-your-data"></a>Защита данных

Защитить данные, хранящиеся в учетной записи Data Lake Storage 1-го поколения, можно с помощью Azure Active Directory и управления доступом (ACL). Соответствующие инструкции см. в статье о [защите данных, хранящихся в Azure Data Lake Storage 1-го поколения](data-lake-store-secure-data.md).

## <a name="delete-your-account"></a>Удаление учетной записи

Чтобы удалить учетную запись Data Lake Storage 1-го поколения, в колонке этой учетной записи нажмите кнопку **Удалить**. Чтобы подтвердить действие, вам будет предложено ввести имя учетной записи, которую вы хотите удалить. Введите имя учетной записи и нажмите кнопку **Удалить**.

![Удалить учетную запись Data Lake Storage 1-го поколения](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Удаление учетной записи озера данных")

## <a name="next-steps"></a>Дополнительная информация

* [Использование Data Lake Storage Gen1 для обеспечения соответствия требованиям больших данных](data-lake-store-data-scenarios.md)
* [Защита данных в Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Использование Azure Data Lake Analytics с Azure Data Lake Storage 1-го поколения](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Использование Azure HDInsight с Data Lake Storage 1-го поколения](data-lake-store-hdinsight-hadoop-use-portal.md)
