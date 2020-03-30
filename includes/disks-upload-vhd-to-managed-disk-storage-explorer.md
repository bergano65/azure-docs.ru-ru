---
title: включить файл
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013777"
---
Storage Explorer 1.10.0 позволяет пользователям загружать, загружать и копировать управляемые диски, а также создавать снимки. Из-за этих дополнительных возможностей можно использовать систему Storage Explorer для переноса данных из наместа в Azure и переноса данных в регионы Azure.

## <a name="prerequisites"></a>Предварительные требования

Для завершения этой статьи вам понадобится следующее:
- Подписка Azure
- Один или несколько дисков, управляемых Azure
- Последняя версия [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Подключение к подписке Azure

Если ваш исследователь хранения не подключен к Azure, вы не сможете использовать его для управления ресурсами. Этот раздел переходит к подключению к учетной записи Azure, чтобы вы могли управлять ресурсами с помощью Storage Explorer.

1. Запустите Explorer хранения azure и нажмите значок **плагина** слева.

    ![Нажмите значок плагина](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Выберите **Добавить учетную запись Azure,** а затем нажмите **далее**.

    ![Добавление учетной записи Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. В поле диалога **Azure Sign введите** учетные данные Azure.

    ![Знак Azure в диалоге](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Выберите свою подписку и нажмите кнопку **Применить**.

    ![Выберите свою подписку.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Загрузить управляемый диск с на прем VHD

1. На левой панели расширьте **диски** и выберите группу ресурсов, на которую вы хотите загрузить свой диск.

    ![Выберите группу ресурсов 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Выберите команду **Отправить**.

    ![Кнопка "Отправить"](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. В **Upload VHD** укажите ваш источник VHD, имя диска, тип ОС, область, на который вы хотите загрузить диск, а также тип учетной записи. В некоторых регионах поддерживаются зоны доступности, для тех регионов можно выбрать зону по вашему выбору.
1. Выберите **Создать,** чтобы начать загрузку диска.

    ![Загрузить vhd диалог](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. Статус загрузки теперь будет отображаться в **деятельности**.

    ![Статус загрузки](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Если загрузка завершена, и вы не видите диск в правом стене, выберите **Refresh.**

## <a name="download-a-managed-disk"></a>Скачать управляемый диск

Следующие шаги объясняют, как загрузить управляемый диск на on-prem VHD. Состояние диска должно быть **непривязано** для загрузки, вы не можете загрузить **прикрепленный** диск.

1. На левой панели, если она еще не расширена, расширьте **диски** и выберите группу ресурсов, с ней нужно загрузить диск.

    ![Выберите группу ресурсов 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. На правом стеку выберите диск, который вы хотите загрузить.
1. Выберите **Скачать,** а затем выбрать, где вы хотели бы сохранить диск.

    ![Скачать управляемый диск](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Выберите **Сохранить** и ваш диск начнется загрузка. Состояние загрузки будет отображаться в **деятельности**.

    ![Статус загрузки](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Копирование управляемого диска

С помощью Storage Explorer можно скопировать выманный диск в регионах или в разных регионах. Для копирования диска:

1. Из выпадения **дисков** слева выберите группу ресурсов, содержащую диск, который вы хотите скопировать.

    ![Выберите группу ресурсов 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. На правом стеку выберите диск, который вы хотите скопировать, и выберите **Copy.**

    ![Копирование управляемого диска](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. На левой панели выберите группу ресурсов, в которая вы хотите вставить диск.

    ![Выберите группу ресурсов 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Выберите **пасту** на правом стекле.

    ![Вставить управляемый диск](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. В диалоге **васковый диск** заполните значения. Вы также можете указать зону доступности в поддерживаемых регионах.

    ![Диалог диска вставки](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Выберите **Пасту** и ваш диск начнет копировать, статус отображается в **действиях**.

    ![Статус копирования пасты](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Создание моментального снимка

1. Из выпадения **дисков** слева выберите группу ресурсов, содержащую диск, который вы хотите моментировать.

    ![Выберите группу ресурсов 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Справа выберите диск, который вы хотите сделать, и выберите **«Создать снимок».**

    ![Создание моментального снимка](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. В **моментальном снимке**укажите имя снимка, а также группу ресурсов, в ней вы хотите его создать. Затем выберите **Создать**.

    ![Создание диалога моментальных снимков](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. После создания снимка можно выбрать **Open in Portal** в **действиях** для просмотра снимка на портале Azure.

    ![Открытый снимок на портале](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Дальнейшие действия
