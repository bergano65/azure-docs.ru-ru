---
title: Краткое руководство по диску Microsoft Azure Data Box | Документация Майкрософт
description: Используйте это краткое руководство для быстрого развертывания диска Azure Data Box на портале Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 23615daf4a07e02b01bbd5a9cdf57ec9a81a2b76
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347407"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Краткое руководство. Развертывание решения "Диск Azure Data Box" с помощью портала Azure

В этом кратком руководстве описывается развертывание диска Azure Data Box с помощью портала Azure. В этих действиях описано как быстро создать заказ, получить диски, распаковать, подключить и копировать данные на диски, таким образом, чтобы оны отправлялись в Azure.

Подробные пошаговое инструкции по развертыванию и отслеживанию см. в статье [Руководство. Заказ Диска Azure Data Box (предварительная версия)](data-box-disk-deploy-ordered.md). 

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F&preserve-view=true).

::: zone-end

::: zone target="chromeless"

В этом руководстве описано, как начать работу с Диском Azure Data Box с помощью портала Azure. Это руководство поможет ответить на следующие вопросы.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы

- Убедитесь, что для службы Azure Data Box включена ваша подписка. Чтобы включить подписку для этой службы, [зарегистрируйтесь в службе](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **Проверка предварительных требований.** Проверьте число дисков и кабелей, операционную систему и другое программное обеспечение.
> - **Подключение и разблокировка.** Подключите устройство и разблокируйте диск для копирования данных.
> - **Копирование данных на диск и проверка.** Скопируйте данные на диски в созданные папки.
> - **Возврат дисков.** Верните диски в центр обработки данных Azure и данные будут загружены в вашу учетную запись хранения.
> - **Проверка данных в Azure.** Перед удалением данных с исходного сервера данных убедитесь, что данные были отправлены в учетную запись хранения.

::: zone-end


::: zone target="docs"

## <a name="order"></a>Порядок

### <a name="portal"></a>[Портал](#tab/azure-portal)

Это действие занимает примерно 5 минут.

1. Создайте новый ресурс Azure Data Box на портале Azure. 
2. Выберите включенную для этой службы подписку и выберите тип передачи **импортирование**. Укажите **Страну источника** , где находятся данные и **регион назначения Azure** для передачи данных.
3. Выберите **Диск Data Box**. Максимальная емкость решения составляет 35 ТБ, и можно создавать заказы на несколько дисков для больших объемов данных.  
4. Введите сведения о заказе и о доставке. Если служба доступна в вашем регионе, укажите адреса электронной почты для уведомлений, просмотрите сводные данные, а затем создайте заказ.

После создания заказа диски подготавливаются к отправке.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Используйте эти команды Azure CLI, чтобы создать задание Диска Azure Data Box.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Выполните команду [az group create](/cli/azure/group#az_group_create), чтобы создать группу ресурсов или использовать существующую группу ресурсов:

   ```azurecli
   az group create --name databox-rg --location westus
   ```

1. Используйте команду [az storage account create](/cli/azure/storage/account#az_storage_account_create), чтобы создать учетную запись хранения или использовать существующую учетную запись хранения:

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. Выполните команду [az databox job create](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_create), чтобы создать задание Data Box со SKU DataBoxDisk:

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxdisk-job \
       --location westus --sku DataBoxDisk --contact-name "Jim Gan" --phone=4085555555 \
       –-city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA \
       --storage-account databoxtestsa --expected-data-size 1
   ```

1. Выполните команду [az databox job update](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_update), чтобы обновить задание, как показано в этом примере, где вы изменяете имя и адрес контакта:

   ```azurecli
   az databox job update -g databox-rg --name databox-job --contact-name "Robert Anic" --email-list RobertAnic@contoso.com
   ```

   Выполните команду [az databox job show](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_show), чтобы получить сведения о задании:

   ```azurecli
   az databox job show --resource-group databox-rg --name databox-job
   ```

   Используйте команду [az databox job list]( /cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_list), чтобы просмотреть все задания Data Box для группы ресурсов:

   ```azurecli
   az databox job list --resource-group databox-rg
   ```

   Выполните команду [az databox job cancel](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_cancel), чтобы отменить задание:

   ```azurecli
   az databox job cancel –resource-group databox-rg --name databox-job --reason "Cancel job."
   ```

   Выполните команду [az databox job delete](/cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_delete), чтобы удалить задание:

   ```azurecli
   az databox job delete –resource-group databox-rg --name databox-job
   ```

1. Используйте команду [az databox job list-credentials]( /cli/azure/ext/databox/databox/job#ext_databox_az_databox_job_list_credentials), чтобы вывести учетные данные для задания Data Box:

   ```azurecli
   az databox job list-credentials --resource-group "databox-rg" --name "databoxdisk-job"
   ```

После создания заказа устройство подготавливается к отправке.

---

## <a name="unpack"></a>Распаковка

Это действие занимает примерно 5 минут.

Диски Data Box высылаются в коробках UPS Express Box. Откройте коробку и убедитесь, что она содержит.

- От 1 до 5 USB-дисков в пузырьковой пленке.
- Соединительный кабель для каждого диска.
- Транспортную этикетку для возврата.

## <a name="connect-and-unlock"></a>Подключение и разблокировка

Это действие занимает примерно 5 минут.

1. Используйте прилагаемый кабель для подключения диска к компьютеру Windows или Linux с поддерживаемой версией ОС. Дополнительные сведения о поддерживаемых версиях ОС, см. в статье [Требования к системе для диска Azure Data Box](data-box-disk-system-requirements.md). 
2. Для разблокировки диска сделайте следующее.

    1. На портале Azure перейдите в раздел **Общие > Сведения об устройстве** и получите ключ доступа.
    2. Скачайте и извлеките инструмент разблокировки Диска для определенной ОС на компьютер, используемый для копирования данных на диски. 
    3. Запустите средство разблокировки Диска и укажите ключ доступа. При повторном подключении диска снова запустите средство unlock и укажите ключ доступа. **Не используйте диалоговое окно или ключ BitLocker для разблокировки диска.** См. дополнительные сведения о разблокировке дисков на клиентах [Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) и [Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).
    4. Инструмент отобразит букву, присвоенную диску. Запишите букву диска. Она будет использоваться в последующих действиях.

## <a name="copy-data-and-validate"></a>Копирование и проверка данных

Время завершения этой операции, зависит от размера ваших данных.

1. Диск содержит папки *PageBlob* , *BlockBlob* , *AzureFile* , *ManagedDisk* и *DataBoxDiskImport*. Для копирования перетащите данные, которые нужно импортировать в качестве блочных BLOB-объектов в папку *BlockBlob*. Аналогичным образом перетащите VHD/VHDX в папку *PageBlob* , а соответствующие данные — в папку *AzureFile*. Скопируйте те VHD, которые хотите отправить как управляемые диски, в папку *ManagedDisk*.

    Хранилище создается в учетной записи хранения Azure для каждой подпапки в папках *BlockBlob* и *PageBlob*. В папке *AzureFile* в качестве вложенной папки создана общая папка.

    Все файлы в папках *BlockBlob* и *PageBlob* копируются по умолчанию в контейнер `$root` в учетную запись хранения Azure. Скопируйте файлы в папку, находящуюся внутри *AzureFile*. Все файлы, скопированные непосредственно в папку *AzureFile* , завершаются сбоем и отправляются как блочные BLOB-объекты.

    > [!NOTE]
    > - Все контейнеры, BLOB-объекты и файлы должны соответствовать [соглашениям об именовании Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Если не следовать этим правилам, произойдет сбой передачи данных в Azure.
    > - Убедитесь в том, что размер файлов не превышает ~4,75 TиБ для блочных BLOB-объектов, ~8 TиБ для страничных BLOB-объектов и ~1 TиБ для файлов Azure.

2. **(Необязательно, но рекомендуется)** После завершения копирования настоятельно рекомендуется как минимум запустить `DataBoxDiskValidation.cmd` из папки *DataBoxDiskImport* и выбрать параметр 1, чтобы проверить файлы. Также рекомендуется, если у вас достаточно времени, выбрать параметр 2, который сгенерирует контрольные суммы для проверки (в зависимости от размера данных может занять много времени). Эти шаги сводят вероятность возникновения ошибок во время отправки данных в Azure к минимуму.
3. Безопасно удалите диск.

## <a name="ship-to-azure"></a>Отправка в Azure

Это действие занимает около 5-7 минут.

1. Поместите все диски в исходную упаковку. Используйте вложенную транспортную этикетку. Если этикетка повреждена или утеряна, загрузить ее на портале. Перейдите в **Обзор** и нажмите на панели команд **Загрузить транспортную этикетку**.
2. Оставьте запечатанный пакет на отправке.  

Служба Data Box Disk отправляет уведомления по электронной почте и обновляет состояние заказа на портале Azure.

## <a name="verify-your-data"></a>Проверка данных

Время завершения этой операции, зависит от размера ваших данных.

1. Когда диск Data Box подключен к сети центра обработки данных Azure, начнется автоматическая загрузка данных в Azure.
2. Служба Azure Data Box уведомляет о завершении копирования данных на портале Azure.
    
    1. Проверьте наличие сбоев в журналах ошибок и предпримите соответствующие действия.
    2. Прежде чем удалять их из источника, убедитесь, что данные сохранены в учетной записи хранения.

## <a name="clean-up-resources"></a>Очистка ресурсов

На выполнение этого действия может потребоваться от 2 до 3 минут.

Для очистки можно отменить заказ Data Box, а затем удалить его.

- Вы можете отменить заказ Data Box на портале Azure, перед его обработкой. Если заказ уже обработан, его отмена невозможна. Заказ будет в процессе обработки пока не достигнет этапа "Завершено".

    Чтобы отменить заказ, перейдите в раздел **Обзор** и нажмите на панели команд **Отменить**.  

- Можно удалить заказ после того как на портале Azure отобразится состояние **Завершено** или **Отменено**.

    Чтобы удалить заказ, перейдите в раздел **Обзор** и нажмите на панели команд **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы развернули диск Azure Data Box для импорта данных в Azure. Дополнительные сведения об управлении диском Azure Data Box, в следующем руководстве.

> [!div class="nextstepaction"]
> [Use Azure portal to administer your Data Box](data-box-portal-ui-admin.md)(Использование портала Azure для управления диском Data Box)

::: zone-end
