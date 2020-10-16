---
title: Руководство по копированию данных из Azure Data Box через SMB | Документация Майкрософт
description: Узнайте, как копировать данные в Azure Data Box через SMB.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 204e89bdf8c660a7e96438f8cb3b8a18aeaec306
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742389"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb-preview"></a>Руководство по Копирование данных из Azure Data Box через SMB (предварительная версия)

В этом руководстве объясняется, как подключиться к Data Box и скопировать оттуда данные на локальный сервер с помощью локального пользовательского интерфейса. Устройство Data Box содержит данные, экспортированные из учетной записи хранения Azure.

В этом руководстве описано следующее:

> [!div class="checklist"]
>
> * Предварительные требования
> * подключение к Data Box;
> * копирование данных из Data Box.

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Предварительные требования

Перед тем как начать, убедитесь в следующем.

1. Вы разместили заказ на Azure Data Box.
    - Сведения о заказе на импорт см. в статье [Руководство по оформлению заказа на Azure Data Box](data-box-deploy-ordered.md).
    - Сведения о заказе на экспорт см. в статье [Руководство по оформлению заказа на Azure Data Box](data-box-deploy-export-ordered.md).
2. Вы получили Data Box. Состояние заказа на портале — **Доставлено**.
3. У вас есть главный компьютер, на который вы хотите скопировать данные из Data Box. На главном компьютере должно быть следующее ПО:
   * [поддерживаемая операционная система](data-box-system-requirements.md);
   * Компьютер должен быть подключен к высокоскоростной сети. Настоятельно рекомендуем использовать хотя бы одно соединение Ethernet со скоростью передачи данных 10 Гбит/с. Если подключение 10 Гбит/с недоступно, можно использовать канал передачи данных 1 Гбит/с, но при этом скорость копирования будет снижена.

## <a name="connect-to-data-box"></a>подключение к Data Box;

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Если вы используете главный компьютер с ОС Windows Server, для подключения к Data Box выполните указанные ниже действия.

1. В первую очередь необходимо выполнить проверку подлинности и начать сеанс. Перейдите на страницу **Подключение и копирование**. Чтобы получить учетные данные для доступа к общим папкам, связанным с вашей учетной записью хранения, выберите элемент **Получить учетные данные**. 

    ![Получение учетных данных для доступа к общей папке](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. В диалоговом окне "Доступ к общей папке и копирование данных" скопируйте **имя пользователя** и **пароль**, соответствующие необходимой общей папке. Щелкните **ОК**.
    
    ![Получение учетных данных для доступа к общей папке 2](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. Чтобы получить доступ к общим папкам, связанным с вашей учетной записью хранения (*exportbvtdataset2* в следующем примере) на главном компьютере, откройте окно командной строки. В командной строке введите:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    В зависимости от формата ваших данных, пути к общей папке могут быть следующими:
    - Блочный BLOB-объект Azure — `\\169.254.143.85\exportbvtdataset2_BlockBlob`.
    - Страничный BLOB-объект Azure — `\\169.254.143.85\exportbvtdataset2_PageBlob`.
    - Файлы Azure — `\\169.254.143.85\exportbvtdataset2_AzFile`.

4. При отображении соответствующего запроса введите пароль для общей папки. В примере ниже показан процесс подключения к общей папке с помощью указанной выше команды.

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

4. Нажмите клавиши WINDOWS+R. В окне **Выполнить** введите `\\<device IP address>`. Нажмите кнопку **ОК**, чтобы открыть проводник.
    
    ![Подключение к общей папке с помощью проводника](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    Должны отобразиться папки, в том числе общие.
    
    ![Подключение к общей папке с помощью проводника 2](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
Если вы используете клиент Linux, вставьте общий ресурс SMB с помощью следующей команды. Параметр "vers" указывает версию SMB, которую поддерживает ваш узел Linux. Вставьте необходимую версию в указанную ниже команду. Версии SMB, которые поддерживает Data Box, см. в разделе [Поддерживаемые файловые системы для клиентов Linux](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>Копирование данных из Data Box

После подключения к общим папкам Data Box можно скопировать данные.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 После подключения к общей папке SMB начните копирование данных. Кроме того, для копирования данных можно использовать любое средство для копирования файлов, совместимое с протоколом SMB, например Robocopy. С помощью средства Robocopy можно запустить несколько заданий копирования. 


Дополнительные сведения о команде Robocopy см. в статье [Robocopy и несколько примеров](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

По завершении копирования перейдите к **панели мониторинга** и проверьте использованное и свободное место на устройство.

Теперь вы можете отправить свое устройство Data Box в корпорацию Майкрософт.


## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве были освещены следующие темы относительно Azure Data Box.

> [!div class="checklist"]
>
> * Предварительные требования
> * подключение к Data Box;
> * копирование данных из Data Box.

Перейдите к следующему руководству, чтобы узнать, как отправить свой Data Box обратно в корпорацию Майкрософт.

> [!div class="nextstepaction"]
> [Отправка Azure Data Box в корпорацию Майкрософт](./data-box-deploy-export-picked-up.md)

