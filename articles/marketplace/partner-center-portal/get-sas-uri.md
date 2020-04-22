---
title: Получите общую подпись доступа URI для вашего изображения VM Лазурный рынок
description: В этой статье объясняется, как получить общую подпись доступа (SAS) URI для каждого виртуального жесткого диска (VHD).
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: c226d35647e4a5a2b1d583dd6328bfb73dae2a1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732638"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Получение универсального кода ресурса (URI) подписанного URL-адреса для образа виртуальной машины

> [!IMPORTANT]
> Мы перемещаем управление вашими предложениями azure Virtual Machine с облачного партнерского портала в партнерский центр. До тех пор, пока ваши предложения не будут перенесены, пожалуйста, следуйте инструкциям в [Get shared access signature URI для вашего VM-изображения](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) для облачного partner Portal для управления вашими предложениями.

В этой статье описывается, как создать единый идентификатор ресурсов общей подписи доступа (SAS) (URI) для каждого виртуального жесткого диска (VHD).

Во время процесса публикации необходимо предоставить URI для каждого VHD, который связан с вашими планами. Эти планы ранее назывались СКУ, или складские единицы. Майкрософт понадобится доступ к этим дискам во время сертификации. Вы введете этот URI на вкладке **Планы** в Partner Center.

При генерации URИ SAS для Ваших VHD- итев следуйте следующим требованиям:

* поддерживаются только неуправляемые виртуальные жесткие диски;
* Требуются только `List` и `Read` разрешения. Не предоставляйте доступ к записи или удалению.
* длительность предоставления доступа (до даты окончания срока действия) должна составлять не менее трех недель с момента создания URI SAS;
* Чтобы защититься от изменений времени UTC, установите дату начала на один день до текущей даты. Например, если текущая дата - 6 октября 2019 года, выберите 10/5/2019.

## <a name="generate-the-sas-address"></a>Создание адреса SAS

Для создания адреса SAS (URL) используются два общих инструмента:

* **Microsoft Storage Explorer** — графический инструмент, доступный для Windows, macOS и Linux.
* **Microsoft Azure CLI** - Рекомендуется для операционных систем, не относясь к Windows, а также для автоматизированных или непрерывных интеграционных сред.

### <a name="use-microsoft-storage-explorer"></a>Используйте Microsoft Storage Explorer

1. Скачать и установить [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Откройте explorer и, в левом меню, выберите **Добавить учетную запись**. Отображается диалоговая коробка **Connect to Azure Storage.**
3. Выберите **Добавить учетную запись Azure,** а затем **войти в систему.** Выполните необходимые шаги для вху-на-ам-аккаунте Azure.
4. В левом стека**Explorer** перейдите на **свои учетные записи хранения** и расширьте этот узло.
5. Право нажмите на Ваш VHD, а затем выберите **Получить подпись доступа к акции**.
6. Отображается диалоговая будка **общей подписи доступа.** Заполните следующие поля.

    * **Время начала** - Дата начала разрешения на доступ к VHD. Укажите дату на день раньше текущей даты.
    * **Срок действия** - Срок действия разрешения на доступ к VHD. Укажите дату, которая не менее трех недель после текущей даты.
    * **Разрешения** - Выберите разрешения на чтение и список.
    * **Контейнерный уровень** - Проверьте общий доступ на **уровне контейнеров URI** check box.

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Иллюстрирует диалоговую коробку общей подписи доступа":::

7. Чтобы создать связанный SAS URI для этого VHD, выберите **Создать**. Диалоговая коробка обновляет и показывает детали об этой операции.
8. Копируйте **URI** и сохраните его в текстовом файле в безопасном месте.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Иллюстрирует поле данных общей подписи доступа":::

    Этот сгенерированный SAS URI предназначен для доступа на уровне контейнеров. Чтобы сделать его конкретным, отспособите текстовый файл, чтобы добавить имя VHD (следующий шаг).

9. Вставьте имя VHD после строки vhds в SAS URI (включите передний слэш). Окончательный SAS URI должен выглядеть следующим образом:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Например, если имя VDH, `TestRGVM2.vhd`то в результате SAS URI будет:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Повторите эти действия для каждого VHD в планах, которые вы будете публиковать.

### <a name="using-azure-cli"></a>Использование Azure CLI

1. Скачать и установить [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Доступны версии для macOS, Windows и различных дистрибутивов Linux.
2. Создайте файл PowerShell (.ps1 расширение файла), скопируйте в следующем коде, а затем сохраните его локально.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Отспособите файл, чтобы использовать следующие значения параметров. Укажите даты в формате даты `2020-04-01T00:00:00Z`UTC, например .

    * `<account-name>`- Имя учетной записи хранилища Azure
    * `<account-key>`- Ключ учетной записи хранения Azure
    * `<vhd-name>`- Ваше имя VHD
    * `<start-date>`- Дата начала разрешения на доступ к VHD. Укажите дату на день раньше текущей даты.
    * `<expiry-date>`- Срок действия разрешения на доступ VHD. Укажите дату не менее трех недель после текущей даты.

    В этом примере показаны надлежащие значения параметров (на момент написания настоящего письма):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Сохраните изменения.
5. Используя один из следующих методов, запустите этот сценарий с административными привилегиями для создания **строки соединения SAS** для доступа на уровне контейнеров:

    * Запуск скрипта из консоли. В Windows, правой кнопкой мыши скрипта и выберите **Выполнить в качестве администратора**.
    * Выполнить сценарий из редактора скриптов PowerShell, таких как [Windows PowerShell ISE.](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise) На этом экране показано создание строки подключения SAS в этом редакторе:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Иллюстрирует создание строки подключения SAS с Windows PowerShell ISE":::

6. Копируйте строку соединения SAS и сохраните ее в текстовом файле в безопасном месте. Отспособите эту строку, чтобы добавить информацию о местоположении VHD для создания окончательного SAS URI.
7. На портале Azure перейдите на хранилище капли, которое включает VHD, связанный с новым URI.
8. Копирование URL-адреса **конечной точки службы Blob**, как показано на следующем скриншоте

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Иллюстрирует конечную точку службы Blob":::

9. Измените текстовый файл, добавив строку подключения SAS, полученную на шаге 6. Создайте полный SAS URI с помощью этого формата:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Например, если имя VHD `TestRGVM2.vhd`есть, SAS URI будет:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Повторите эти шаги для каждого виртуального жесткого диска в публикуемом номере SKU.

## <a name="verify-the-sas-uri"></a>Проверка URI SAS

Просмотрите каждый созданный SAS URI, используя следующий контрольный список, чтобы проверить, что:

* URI выглядит следующим образом:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* URI включает в себя имя файла изображения VHD, включая расширение имени файла ".vhd".
* `sp=rl`появляется в середине URI. Эта строка `Read` `List` показывает, что и доступ указан.
* При `sr=c` отосвечке это означает, что указано доступ на уровне контейнера.
* Копировать и вставить URI в браузер для тестирования загрузки капли (вы можете отменить операцию до завершения загрузки).

## <a name="next-step"></a>Следующий шаг

Если у вас возникли трудности с созданием SAS URI, [см.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues) В противном случае сохраните URI SAS в надежном расположении для последующего использования. Он вам понадобится для публикации предложения VM в Partner Center.

* [Создание предложения по виртуальным машинам Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
