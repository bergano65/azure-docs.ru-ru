---
title: Включить вложенную виртуализацию на шаблоне VM в лабораторных службах Azure (Скрипт) Документы Майкрософт
description: Узнайте, как создать шаблон VM с несколькими vMs внутри.  Другими словами, включите вложенную виртуализацию на шаблоне VM в Службах лаборатории Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 56e5ad21f94521565b4df193b2450a1c994b66f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503040"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Включить вложенную виртуализацию на виртуальной машине шаблона в Azure Lab Services с помощью сценария

Вложенная виртуализация позволяет создать мульти-VM-среду внутри виртуальной машины шаблона лаборатории. Публикация шаблона обеспечит каждого пользователя в лаборатории с виртуальной машиной, настроенной с несколькими VMs внутри него.  Для получения дополнительной информации о вложенной виртуализации и лабораторных службах Azure [см.](how-to-enable-nested-virtualization-template-vm.md)

Шаги в этой статье сосредоточены на настройке вложенной виртуализации для Windows Server 2016 или Windows Server 2019. Вы будете использовать скрипт для настройки шаблона машины с Hyper-V.  Следующие шаги помогут вам использовать [скрипты Hyper-V служб лабораторных служб.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)

>[!IMPORTANT]
>Выберите **Большой (вложенная виртуализация)** или **Medium (вложенная виртуализация)** для размера виртуальной машины при создании лаборатории.  Вложенная виртуализация не будет работать иначе.  

## <a name="run-script"></a>Запуск сценария

1. Если вы используете Internet Explorer, возможно, вам придется добавить `https://github.com` в список доверенных сайтов.
    1. Откройте обозреватель Internet Explorer.
    1. Выберите значок передачи и выберите **варианты Интернета.**  
    1. При пояснении диалога **«Параметры Интернета»** выберите **«Безопасность»,** выберите **«Доверенные сайты»,** нажмите кнопку **«Сайты».**
    1. При пояснении `https://github.com` диалога **«Доверенные сайты»** добавьте в список доверенных веб-сайтов и выберите **«Закрыть».**

        ![Доверенные сайты](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Загрузите файлы репозитория Git, изложенные в следующих шагах.
    1. Перейти [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)к .
    1. Нажмите кнопку **Клон или Скачать.**
    1. Нажмите **Скачать ЗИП**.
    1. Извлеките ZIP-файл

    >[!TIP]
    >Вы также можете клонировать репозиторий Git в [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git).

1. Запуск **PowerShell** в режиме **администратора.**
1. В окне PowerShell перейдите к папке с загруженным скриптом. Если вы перемещаетесь по верхней папке файлов репозитория, `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`скрипт находится в .
1. Возможно, вам придется изменить политику выполнения, чтобы успешно запустить скрипт. Выполните следующую команду:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Выполните скрипт:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Скрипт может потребовать перезапуска машины. Следуйте инструкциям из сценария и повторно запустить сценарий до **завершения сценария** видно в выходе.
1. Не забудьте сбросить политику выполнения. Выполните следующую команду:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Заключение

Теперь ваш шаблон машина готова к созданию Hyper-V виртуальных машин. Смотрите [Создать виртуальную машину в Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) для инструкций о том, как создать Hyper-V виртуальных машин. Кроме того, см [Microsoft Evaluation Center,](https://www.microsoft.com/evalcenter/) чтобы проверить доступные операционные системы и программное обеспечение.  

## <a name="next-steps"></a>Дальнейшие действия

Следующие шаги являются общими для создания любой лаборатории.

- [Добавление пользователей](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Установленная квота](how-to-configure-student-usage.md#set-quotas-for-users)
- [Установить расписание](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Ссылки на регистрацию по электронной почте студентам](how-to-configure-student-usage.md#send-invitations-to-users)