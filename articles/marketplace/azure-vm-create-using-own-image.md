---
title: Создание предложения виртуальной машины Azure в Azure Marketplace с помощью собственного образа
description: Узнайте, как опубликовать предложение виртуальной машины в Azure Marketplace с помощью собственного образа.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 42022d1204c3b524ee2e9ef2770f616fba89dc8c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284805"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Создание виртуальной машины с помощью собственного образа

В этой статье описывается создание и развертывание образа виртуальной машины, предоставленной пользователем.

> [!NOTE]
> Перед началом этой процедуры ознакомьтесь с [техническими требованиями](marketplace-virtual-machines.md#technical-requirements) для предложений виртуальных машин Azure, включая требования к виртуальному жесткому диску (VHD).

Чтобы вместо этого использовать утвержденный базовый образ, следуйте инструкциям в разделе [Создание образа виртуальной машины из утвержденной базы](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Настройка виртуальной машины

В этом разделе описано обновление и подготовка виртуальной машины Azure, а также определение ее размера. Эти шаги нужны для того, чтобы подготовить виртуальную машину к развертыванию в Azure Marketplace.

### <a name="size-the-vhds"></a>Размер виртуальных жестких дисков

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Установка самых свежих обновлений

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Выполнение дополнительных проверок безопасности

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Выполнение пользовательской настройки и запланированные задачи

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

## <a name="upload-the-vhd-to-azure"></a>Отправка виртуального жесткого диска в Azure

Настройте и подготовьте виртуальную машину, которая будет отправлена, как описано в статье [Подготовка VHD или VHDX Windows к отправке в Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) или [Создание и передача виртуального жесткого диска Linux](../virtual-machines/linux/create-upload-generic.md).

## <a name="extract-the-vhd-from-image-if-using-image-building-services"></a>Извлечение виртуального жесткого диска из образа (при использовании служб Image строит службы)

Если вы используете службу создания образов, например [Pack](https://www.packer.io/), возможно, потребуется извлечь VHD из образа. Для этого нет прямого способа. Вам потребуется создать виртуальную машину и извлечь виртуальный жесткий диск с диска виртуальной машины.

### <a name="create-the-vm-on-the-azure-portal"></a>Создание виртуальной машины на портал Azure

Выполните следующие действия, чтобы создать базовый образ виртуальной машины на [портал Azure](https://ms.portal.azure.com/).

1. Войдите на [портал Azure](https://ms.portal.azure.com/).
2. Выберите **Виртуальные машины**.
3. Выберите **+ Добавить** , чтобы открыть экран **Создание виртуальной машины** .
4. Выберите изображение из раскрывающегося списка или выберите **Просмотреть все общедоступные и частные образы** , чтобы найти или просмотреть все доступные образы виртуальных машин.
5. Чтобы создать виртуальную машину **Gen 2** , перейдите на вкладку **Дополнительно** и выберите параметр **Gen 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Выберите Gen 1 или Gen 2.":::

6. Выберите размер виртуальной машины для развертывания.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Выберите Gen 1 или Gen 2.":::

7. Укажите другие необходимые сведения для создания виртуальной машины.
8. Выберите **Просмотр и создание**, чтобы проверить выбранные параметры. Когда появится сообщение **Проверка пройдена** , выберите **создать**.

Azure начнет подготовку указанной вами виртуальной машины. Чтобы отслеживать ход выполнения, выберите вкладку **виртуальные машины** в меню слева. После создания состояние виртуальных машин изменится на **работает**.

### <a name="connect-to-your-vm"></a>Подключение к виртуальной машине

Обратитесь к следующей документации, чтобы подключиться к виртуальной машине [Windows](../virtual-machines/windows/connect-logon.md) или [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) .

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Дальнейшие действия

- Рекомендуемый следующий шаг: [Проверьте образ виртуальной машины](azure-vm-image-test.md) , чтобы убедиться, что он соответствует требованиям к публикации Azure Marketplace. Это необязательно.
- Если вы не протестируете образ виртуальной машины, продолжайте [создавать URI SAS](azure-vm-get-sas-uri.md).
- Если вы столкнулись с трудностями при создании нового виртуального жесткого диска на основе Azure, см. раздел [вопросы и ответы о виртуальной машине для Azure Marketplace](azure-vm-create-faq.md).
