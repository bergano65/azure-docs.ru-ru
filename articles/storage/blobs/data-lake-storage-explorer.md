---
title: Use Azure Storage Explorer with Azure Data Lake Storage Gen2
description: In this quickstart, you learn how to use Azure Storage Explorer to create a container in an Azure Data Lake Storage Gen2 account, as well as a directory and a file. Далее вы узнаете, как загружать файл на локальный компьютер и как просматривать все файлы в каталоге.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 53f43945b13a9dae44eba752f935eb34d7aa498b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327621"
---
# <a name="use-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Use Azure Storage Explorer with Azure Data Lake Storage Gen2

В этом кратком руководстве вы научитесь использовать [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/) для создания каталога и большого двоичного объекта. Далее вы узнаете, как загружать большой двоичный объект на локальный компьютер и как просматривать все большие двоичные объекты в каталоге. Вы также узнаете, как создавать моментальный снимок большого двоичного объекта, управлять политиками доступа к каталогу и создавать подписанный URL-адрес.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Для работы с этим кратким руководством необходимо установить Обозреватель службы хранилища Azure. Чтобы установить Обозреватель службы хранилища Azure для Windows, Macintosh или Linux, см. раздел [Обозреватель хранилищ Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Вход в Обозреватель службы хранилища

При первом запуске откроется окно **подключения обозревателя службы хранилища Microsoft Azure**. Хотя Обозреватель службы хранилища предоставляет несколько способов подключения к учетным записям хранения, в настоящее время поддерживается только один из способов управления ACL.

|Задача|Цель|
|---|---|
|Добавление учетной записи Azure | Перенаправляет вас на страницу входа организации для аутентификации в Azure. В настоящее время это единственный поддерживаемый метод проверки подлинности, если вы хотите задавать списки ACL и управлять ими. |

Select **Add an Azure Account** and click **Sign in..** . Follow the on-screen prompts to sign into your Azure account.

![Обозреватель службы хранилища Microsoft Azure — окно подключения](media/storage-quickstart-blobs-storage-explorer/connect.png)

После подключения загружается обозреватель службы хранилища Azure с вкладкой **Обозреватель**. В этом представлении вы можете просматривать сведения обо всех учетных записях хранения Azure, а также о локальном хранилище, настроенном с помощью учетных записей [эмулятора службы хранилища Azure](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) или сред [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Обозреватель службы хранилища Microsoft Azure — окно подключения](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Создание контейнера

Большие двоичные объекты всегда отправляются в каталог. Это позволяет упорядочивать группы BLOB-объектов аналогично организации файлов в папки на компьютере.

Чтобы создать каталог, разверните учетную запись хранения, созданную на предыдущем шаге. Щелкните правой кнопкой мыши элемент **Контейнеры больших двоичных объектов** и выберите пункт **Создать контейнер BLOB-объектов**. Enter the name for your container. When complete, press **Enter** to create the container. После успешного создания каталога больших двоичных объектов он отобразится в папке **Контейнеры больших двоичных объектов** выбранной учетной записи хранения.

![Microsoft Azure Storage Explorer - Creating a container](media/storage-quickstart-blobs-storage-explorer/creating-a-filesystem.png)

## <a name="upload-blobs-to-the-directory"></a>Отправка больших двоичных объектов в каталог

Хранилище BLOB-объектов поддерживает блочные, добавочные и страничные BLOB-объекты. VHD-файлы, используемые для резервных виртуальных машин IaaS, являются страничными BLOB-объектами. Добавочные BLOB-объекты используются для ведения журнала, например если требуется выполнить запись в файл и затем добавлять дополнительные сведения. Большинство файлов, находящихся в хранилище BLOB-объектов, представляют собой блочные BLOB-объекты.

На ленте каталога выберите **Передать**. Эта операция позволяет передать папку или файл.

Выберите файлы или папку для передачи. Выберите **тип большого двоичного объекта**. Допустимые варианты больших двоичных объектов: **добавочный**, **страничный** или **блочный**.

Если вы передаете VHD- или VHDX-файл, выберите **Upload .vhd/.vhdx files as page blobs (recommended)** (Передать VHD- или VHDX-файлы как страничные BLOB-объекты (рекомендуется)).

В поле **Upload to folder (optional)** (Передача в папку (необязательно)) укажите папку для хранения файлов или папок в папке каталога. Если папка не выбрана, файлы передаются прямо в каталог.

![Обозреватель службы хранилища Microsoft Azure — передача BLOB-объекта](media/storage-quickstart-blobs-storage-explorer/uploadblob.png)

Если нажать кнопку **ОК**, выбранные файлы помещаются в очередь для передачи, а затем каждый файл передается. После завершения передачи результаты отображаются в окне **Действия**.

## <a name="view-blobs-in-a-directory"></a>Просмотр больших двоичных объектов в каталоге

В приложении **Обозреватель службы хранилища Azure** выберите каталог в учетной записи хранения. На основной панели отображается список больших двоичных объектов в выбранном каталоге.

![Обозреватель службы хранилища Microsoft Azure — список больших двоичных объектов в каталоге](media/storage-quickstart-blobs-storage-explorer/listblobs.png)

## <a name="download-blobs"></a>Скачивание больших двоичных объектов

Чтобы скачать большие двоичные объекты с помощью **обозревателя службы хранилища Azure**, выберите большой двоичный объект, а затем на ленте нажмите кнопку **Скачать**. Откроется диалоговое окно, в котором можно ввести имя файла. Нажмите кнопку **Сохранить** чтобы начать скачивание большого двоичного объекта в локальную папку.

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как передавать файлы между локальным диском и хранилищем BLOB-объектов Azure с помощью **обозревателя службы хранилища Azure**. Дополнительные сведения о том, как задать ACL для файлов и каталогов, см. в нашем практическом руководстве по этой теме.

> [!div class="nextstepaction"]
> [Установка разрешений на уровне файлов и каталогов в Azure Data Lake Storage 2-го поколения (предварительная версия) с помощью Обозревателя службы хранилища Azure](data-lake-storage-how-to-set-permissions-storage-explorer.md)
