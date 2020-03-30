---
title: Приложения для рендеринга — пакетная служба Azure
description: Вы можете использовать в пакетной службе Azure любые приложения для рендеринга Но некоторые распространенные версии уже включены в образы виртуальных машин Azure Marketplace.
services: batch
ms.service: batch
author: LauraBrenner
ms.author: labrenne
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: 77672534b2aad993a44e9b637fbed58df8610e97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022993"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Предварительно установленные приложения в образах виртуальных машин для рендеринга

Вы можете использовать в пакетной службе Azure любые приложения для рендеринга Но некоторые распространенные версии уже включены в образы виртуальных машин Azure Marketplace.

Везде, где это возможно, вам будет доступно лицензирование с оплатой по мере использования для всех предустановленных приложений для рендеринга. При создании пула пакетной службы вы можете указать необходимые приложения, и далее поминутно платить за использование виртуальных машин и приложений. Цены на приложения цены указаны на [странице с ценами на пакетную службу Azure](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Некоторые приложения поддерживают только платформу Windows, но большинство из них доступны как в Windows, так и в Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Приложения на рендеринге изображений CentOS 7

Следующий список относится к CentOS 7.6, версия 1.1.6 рендеринга изображений.

* Autodesk Maya I/O 2017 обновление 5 (версия сборки: 201708032230);
* Autodesk Maya I/O 2018 обновление 2 (версия сборки: 201711281015);
* Autodesk Майя I/O 2019 Обновление 1
* Autodesk Арнольд для Майя 2017 (Версия Арнольда 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Арнольд для Майя 2018 (Версия Арнольда 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Арнольд для Майя 2019 (Версия Арнольда 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (версия 3.60.04);
* Chaos Group V-Ray for Maya 2018 (версия 3.60.04);
* Blender (2.68).
* Блендер (2.8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Приложения на последних изображениях рендеринга Windows Server 2016

Следующий список относится к Windows Server 2016, версия 1.3.8 рендеринга изображений.

* Autodesk Maya I/O 2017 обновление 5 (версия 17.4.5459);
* Autodesk Maya I/O 2018 Обновление 6 (версия 18.4.0.7622)
* Autodesk Майя I/O 2019
* Autodesk 3ds Max I/O 2018 обновление 4 (версия 20.4.0.4254);
* Autodesk 3ds Max I/O 2019 обновление 1 (версия 21.2.0.2219);
* Autodesk 3ds Макс I/O 2020 Обновление 2
* Autodesk Арнольд для Майя 2017 (Версия Арнольда 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Арнольд для Майя 2018 (Версия Арнольда 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Арнольд для Майя 2019 (Версия Арнольда 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Арнольд для 3ds Max 2018 (Версия Арнольда 5.3.0.2) (версия 1.2.926)
* Autodesk Арнольд для 3ds Max 2019 (Версия Арнольда 5.3.0.2) (версия 1.2.926)
* Autodesk Арнольд для 3ds Max 2020 (Версия Арнольда 5.3.0.2) (версия 1.2.926)
* Хаос Группv Ray для Maya 2017 (версия 4.12.01)
* Хаос Группv Ray для Maya 2018 (версия 4.12.01)
* Хаос группы V-Ray для Майя 2019 (версия 4.04.03)
* Chaos Group V-Ray для 3ds Max 2018 (версия 4.20.01)
* Хаос группы V-Ray для 3ds Max 2019 (версия 4.20.01)
* Хаос группа V-Ray для 3ds Max 2020 (версия 4.20.01)
* Blender (2.79).
* Блендер (2.80)
* АЗ 10

> [!IMPORTANT]
> Чтобы запустить V-Ray с Maya за пределами [шаблонов расширения Azure Batch,](https://github.com/Azure/batch-extension-templates)начните `vrayses.exe` перед запуском рендера. Для запуска vrayses.exe за пределами шаблонов можно `%MAYA_2017%\vray\bin\vrayses.exe"`использовать следующую команду.
>
> Например, смотрите задачу запуска [шаблона Maya и V-Ray](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) на GitHub.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Приложения на предыдущих визуализациях Windows Server 2016

Следующий список относится к Windows Server 2016, версия 1.3.7 рендеринга изображений.

* Autodesk Maya I/O 2017 обновление 5 (версия 17.4.5459);
* Autodesk Maya I/O 2018 обновление 4 (версия 18.4.0.7622);
* Autodesk 3ds Max I/O 2019 обновление 1 (версия 21.2.0.2219);
* Autodesk 3ds Max I/O 2018 обновление 4 (версия 20.4.0.4254);
* Autodesk Arnold for Maya 2017 (Arnold версии 5.2.0.1) MtoA-3.1.0.1-2017;
* Autodesk Arnold for Maya 2018 (Arnold версии 5.2.0.1) MtoA-3.1.0.1-2018;
* Autodesk Арнольд для 3ds Max 2018 (Версия Арнольда 5.0.2.4)(версия 1.2.926)
* Autodesk Арнольд для 3ds Max 2019 (Версия Арнольда 5.0.2.4)(версия 1.2.926)
* Хаос Группv Ray для Maya 2018 (версия 3.52.03)
* Chaos Group V-Ray для 3ds Max 2018 (версия 3.60.02)
* Хаос группы V-Ray для Майя 2019 (версия 3.52.03)
* Хаос группы V-Ray для 3ds Max 2019 (версия 4.10.01)
* Blender (2.79).

> [!NOTE]
> Chaos Group V-Ray для 3ds Max 2019 (версия 4.10.01) вводит изменения v-ray. Для использования предыдущей версии (версия 3.60.02) используйте Windows Server 2016, версию 1.3.2 визуализации узлов.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы использовать образы виртуальных машин для рендеринга, их необходимо указывать в конфигурации пула при создании пула. Подробнее см. статью о [возможностях рендеринга в пакетной службе](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).
