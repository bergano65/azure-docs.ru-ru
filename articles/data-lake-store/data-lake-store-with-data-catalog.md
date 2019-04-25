---
title: Регистрация данных из Azure Data Lake Storage 1-го поколения в каталоге данных Azure | Документы Майкрософт
description: Регистрация данных из Azure Data Lake Storage 1-го поколения в каталоге данных Azure
services: data-lake-store,data-catalog
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fd887560c0011fb1ec2141e33f02f7e3d8a39c81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196614"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Регистрация данных из Azure Data Lake Storage 1-го поколения в каталоге данных Azure
В этой статье вы узнаете, как интегрировать Azure Data Lake Storage 1-го поколения со службой "Каталог данных Azure", чтобы в организации можно было обнаруживать данные с помощью интеграции с каталогом данных. Дополнительные сведения о каталогизации данных см. в статье [Каталог данных Azure](../data-catalog/data-catalog-what-is-data-catalog.md). Чтобы понять, в каких сценариях можно использовать каталог данных, см. статью [Типичные сценарии каталога данных Azure](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Технические условия
Перед началом работы с этим учебником необходимо иметь следующее:

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Включите свою подписку Azure** для Data Lake Storage 1-го поколения. Ознакомьтесь с [инструкциями](data-lake-store-get-started-portal.md).
* **Учетная запись Data Lake Storage 1-го поколения**. Следуйте инструкциям в статье [Начало работы с Azure Data Lake Storage 1-го поколения с помощью портала Azure](data-lake-store-get-started-portal.md). В целях этого руководства создадим учетную запись Data Lake Storage 1-го поколения и назовем ее **datacatalogstore**.

    После создания учетной записи передайте в нее пример набора данных. В этом учебнике мы передадим CSV-файлы в папку **AmbulanceData** в [репозитории Git озера данных Azure](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Чтобы передать данные в контейнер больших двоичных объектов, можно использовать различные клиенты, например [обозреватель хранилищ Azure](https://storageexplorer.com/).
* **Каталог данных Azure**. В организации уже должен быть создан каталог данных Azure. Для каждой организации допускается только один каталог.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Регистрация Data Lake Storage 1-го поколения в качестве источника для каталога данных

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Перейдите на страницу `https://azure.microsoft.com/services/data-catalog`и щелкните **Начало работы**.
1. Войдите на портал каталога данных Azure и щелкните **Опубликовать данные**.

    ![Регистрация источника данных](./media/data-lake-store-with-data-catalog/register-data-source.png "Регистрация источника данных")
1. На следующей странице щелкните **Запустить приложение**. На ваш компьютер будет скачан файл манифеста приложения. Дважды щелкните этот файл манифеста, чтобы запустить приложение.
1. На странице "Приветствие" щелкните **Войти**и введите учетные данные.

    ![Экран приветствия](./media/data-lake-store-with-data-catalog/welcome.screen.png "Экран приветствия")
1. На странице "Выбор источника данных" выберите **Azure Data Lake Store**, а затем нажмите кнопку **Далее**.

    ![Выбор источника данных](./media/data-lake-store-with-data-catalog/select-source.png "Выбор источника данных")
1. На следующей странице укажите имя учетной записи Data Lake Storage 1-го поколения, которую необходимо зарегистрировать в каталоге данных. Оставьте значения по умолчанию для остальных параметров и щелкните **Подключиться**.

    ![Подключение к источнику данных](./media/data-lake-store-with-data-catalog/connect-to-source.png "Подключение к источнику данных")
1. Следующую страницу можно разделить на следующие области.

    a. Поле **Иерархия серверов** представляет структуру папки учетной записи Data Lake Storage 1-го поколения. **$Root** представляет корень учетной записи Data Lake Storage 1-го поколения, а **AmbulanceData** — папку, созданную в корне учетной записи Data Lake Storage 1-го поколения.

    2. В поле **Доступные объекты** перечислены файлы и папки, расположенные в папке **AmbulanceData**.

    c. **Объекты для регистрации** перечислены файлы и папки, которые вы хотите зарегистрировать в службе "Каталог данных Azure".

    ![Просмотр структуры данных](./media/data-lake-store-with-data-catalog/view-data-structure.png "Просмотр структуры данных")
1. В рамках этого учебника необходимо зарегистрировать все файлы в каталоге. Для этого нажмите кнопку (![Перемещение объектов](./media/data-lake-store-with-data-catalog/move-objects.png "Перемещение объектов")), чтобы переместить все файлы в поле **Объекты для регистрации**.

    Так как данные будут зарегистрированы в каталоге данных на уровне всей организации, рекомендуется добавить какие-либо метаданные, которые позже можно будет использовать для быстрого поиска данных. Скажем, можно добавить электронный адрес владельца данных (например, того, кто передает данные) или добавить тег для идентификации данных. На снимке экрана ниже показан тег, добавляемый к данным.

    ![Просмотр структуры данных](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Просмотр структуры данных")

    Щелкните **Зарегистрировать**.
1. На следующем снимке экрана показано, что данные успешно зарегистрированы в каталоге данных.

    ![Регистрация завершена](./media/data-lake-store-with-data-catalog/registration-complete.png "Просмотр структуры данных")
1. Щелкните **Просмотреть портал** , чтобы вернуться на портал каталога данных и убедиться, что теперь вы можете обращаться к зарегистрированным данным на портале. Для поиска данных можно использовать тег, который вы добавили при регистрации данных.

     ![Поиск данных в каталоге](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Поиск данных в каталоге")
1. Теперь можно выполнять такие операции, как добавление аннотаций и документации к данным. Дополнительные сведения см. по следующим ссылкам.

    * [Создание заметок к источникам данных](../data-catalog/data-catalog-how-to-annotate.md)
    * [Создание документации по источникам данных](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>См. также
* [Создание заметок к источникам данных](../data-catalog/data-catalog-how-to-annotate.md)
* [Создание документации по источникам данных](../data-catalog/data-catalog-how-to-documentation.md)
* [Интеграция Data Lake Storage 1-го поколения c другими службами Azure](data-lake-store-integrate-with-other-services.md)
