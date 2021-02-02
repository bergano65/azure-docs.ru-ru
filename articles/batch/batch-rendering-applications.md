---
title: Приложения для рендеринга
description: Вы можете использовать в пакетной службе Azure любые приложения для рендеринга Но некоторые распространенные версии уже включены в образы виртуальных машин Azure Marketplace.
ms.date: 02/01/2021
ms.topic: how-to
ms.openlocfilehash: 4eeb87c866858f2f1733b81e3e2154f4521e94bc
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430034"
---
# <a name="pre-installed-applications-on-batch-rendering-vm-images"></a>Предварительно установленные приложения при пакетной отрисовке образов виртуальных машин

Вы можете использовать в пакетной службе Azure любые приложения для рендеринга Но некоторые распространенные версии уже включены в образы виртуальных машин Azure Marketplace.

Везде, где это возможно, вам будет доступно лицензирование с оплатой по мере использования для всех предустановленных приложений для рендеринга. При создании пула пакетной службы вы можете указать необходимые приложения, и далее поминутно платить за использование виртуальных машин и приложений. Цены на приложения цены указаны на [странице с ценами на пакетную службу Azure](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Некоторые приложения поддерживают только платформу Windows, но большинство из них доступны как в Windows, так и в Linux.

## <a name="applications-on-centos-7-rendering-image"></a>Приложения на отрисовке изображения CentOS 7

Следующий список относится к отрисовке изображений CentOS 7.6, версия 1.1.6.

* Autodesk Maya I/O 2017 обновление 5 (версия сборки: 201708032230);
* Autodesk Maya I/O 2018 обновление 2 (версия сборки: 201711281015);
* Autodesk Maya I/O 2019 обновление 1;
* Autodesk Arnold for Maya 2017 (Arnold версии 5.3.1.1) MtoA-3.2.1.1-2017;
* Autodesk Arnold for Maya 2018 (Arnold версии 5.3.1.1) MtoA-3.2.1.1-2018;
* Autodesk Arnold for Maya 2019 (Arnold версии 5.3.1.1) MtoA-3.2.1.1-2019;
* Chaos Group V-Ray for Maya 2017 (версия 3.60.04);
* Chaos Group V-Ray for Maya 2018 (версия 3.60.04);
* Blender (2.68).
* Blender (2.8)

## <a name="applications-on-latest-windows-server-rendering-image"></a>Приложения на последнем изображении для подготовки к просмотру Windows Server

Следующий список относится к образу визуализации Windows Server версии 1.5.0.

* Обновление Autodesk Maya ввода-вывода 2020 4,4
* Autodesk 3ds Max I/O 2021 с обновлением 3
* Autodesk Arnold для Maya 2020 (Arnold версии 6.1.0.1) Мтоа-4.1.1.1-2020
* Autodesk Arnold для 3ds Max 2021 (Arnold Version 6.1.0.1) Макстоа-4.2.2.20-2021
* Chaos Group V-Ray для Maya 2020 (версия 5.00.21)
* Chaos Group V-Ray для 3ds Max 2021 (версия 5.00.05)
* Blender (2.79).
* Blender (2.80)
* AZ 10

> [!IMPORTANT]
> Чтобы запустить V-Ray с Maya за пределами [шаблонов расширения пакетной службы Azure](https://github.com/Azure/batch-extension-templates), запустите `vrayses.exe` перед выполнением отрисовки. Чтобы запустить vrayses.exe за пределами шаблонов, можно использовать следующую команду: `%MAYA_2020%\vray\bin\vrayses.exe"`.
>
> Пример см. в описании задачи запуска шаблона [Maya и V-Ray](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) на GitHub.

## <a name="applications-on-previous-windows-server-rendering-images"></a>Приложения на предыдущих изображениях Windows Server для подготовки к просмотру

Следующий список относится к отрисовке изображений Windows Server 2016, версия 1.3.8.

* Autodesk Maya I/O 2017 обновление 5 (версия 17.4.5459);
* Autodesk Maya I/O 2018 обновление 6 (версия 18.4.0.7622);
* Autodesk Maya I/O 2019;
* Autodesk 3ds Max I/O 2018 обновление 4 (версия 20.4.0.4254);
* Autodesk 3ds Max I/O 2019 обновление 1 (версия 21.2.0.2219);
* Autodesk 3ds Max I/O 2020 обновление 2;
* Autodesk Arnold for Maya 2017 (Arnold версии 5.3.0.2) MtoA-3.2.0.2-2017;
* Autodesk Arnold for Maya 2018 (Arnold версии 5.3.0.2) MtoA-3.2.0.2-2018;
* Autodesk Arnold for Maya 2019 (Arnold версии 5.3.0.2) MtoA-3.2.0.2-2019;
* Autodesk Arnold for 3ds Max 2018 (Arnold версии 5.3.0.2) (версия 1.2.926);
* Autodesk Arnold for 3ds Max 2019 (Arnold версии 5.3.0.2) (версия 1.2.926);
* Autodesk Arnold for 3ds Max 2020 (Arnold версии 5.3.0.2) (версия 1.2.926);
* Chaos Group V-Ray for Maya 2017 (версия 4.12.01);
* Chaos Group V-Ray for Maya 2018 (версия 4.12.01);
* Chaos Group V-Ray for Maya 2019 (версия 4.04.03);
* Chaos Group V-Ray for 3ds Max 2018 (версия 4.20.01);
* Chaos Group V-Ray for 3ds Max 2019 (версия 4.20.01);
* Chaos Group V-Ray for 3ds Max 2020 (версия 4.20.01);
* Blender (2.79).
* Blender (2.80)
* AZ 10

Следующий список относится к отрисовке изображений Windows Server 2016, версии 1.3.7.

* Autodesk Maya I/O 2017 обновление 5 (версия 17.4.5459);
* Autodesk Maya I/O 2018 обновление 4 (версия 18.4.0.7622);
* Autodesk 3ds Max I/O 2019 обновление 1 (версия 21.2.0.2219);
* Autodesk 3ds Max I/O 2018 обновление 4 (версия 20.4.0.4254);
* Autodesk Arnold for Maya 2017 (Arnold версии 5.2.0.1) MtoA-3.1.0.1-2017;
* Autodesk Arnold for Maya 2018 (Arnold версии 5.2.0.1) MtoA-3.1.0.1-2018;
* Autodesk Arnold for 3ds Max 2018 (Arnold версии 5.0.2.4) (версия 1.2.926);
* Autodesk Arnold for 3ds Max 2019 (Arnold версии 5.0.2.4) (версия 1.2.926);
* Chaos Group V-Ray for Maya 2018 (версия 3.52.03);
* Chaos Group V-Ray for 3ds Max 2018 (версия 3.60.02);
* Chaos Group V-Ray for Maya 2019 (версия 3.52.03);
* Chaos Group V-Ray for 3ds Max 2019 (версия 4.10.01);
* Blender (2.79).

> [!NOTE]
> Chaos Group V-Ray for 3ds Max 2019 (версия 4.10.01) вносит критические изменения в V-Ray. Чтобы использовать предыдущую версию (версия 3.60.02), используйте узлы отрисовки Windows Server 2016, версии 1.3.2.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы использовать образы виртуальных машин для рендеринга, их необходимо указывать в конфигурации пула при создании пула. Подробнее см. статью о [возможностях рендеринга в пакетной службе](./batch-rendering-functionality.md).
