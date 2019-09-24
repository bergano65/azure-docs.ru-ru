---
title: Приложения для рендеринга — пакетная служба Azure
description: Предварительно установленные приложения пакетной службы для рендеринга
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: 30a365b36645dfe79f35b4bb889c0a06535a4c73
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212725"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Предварительно установленные приложения в образах виртуальных машин для рендеринга

Вы можете использовать в пакетной службе Azure любые приложения для рендеринга Но некоторые распространенные версии уже включены в образы виртуальных машин Azure Marketplace.

Везде, где это возможно, вам будет доступно лицензирование с оплатой по мере использования для всех предустановленных приложений для рендеринга. При создании пула пакетной службы вы можете указать необходимые приложения, и далее поминутно платить за использование виртуальных машин и приложений. Цены на приложения цены указаны на [странице с ценами на пакетную службу Azure](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Некоторые приложения поддерживают только платформу Windows, но большинство из них доступны как в Windows, так и в Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Приложения на изображениях отрисовки CentOS 7

Следующий список относится к образам визуализации CentOS 7,6, версия 1.1.6.

* Autodesk Maya I/O 2017 обновление 5 (версия сборки: 201708032230);
* Autodesk Maya I/O 2018 обновление 2 (версия сборки: 201711281015);
* Обновление 1 для Autodesk Maya I/O 2019
* Autodesk Arnold для Maya 2017 (Arnold версии 5.3.1.1) Мтоа-3.2.1.1-2017
* Autodesk Arnold для Maya 2018 (Arnold версии 5.3.1.1) Мтоа-3.2.1.1-2018
* Autodesk Arnold для Maya 2019 (Arnold версии 5.3.1.1) Мтоа-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (версия 3.60.04);
* Chaos Group V-Ray for Maya 2018 (версия 3.60.04);
* Blender (2.68).
* Наложение (2,8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Приложения на последних изображениях отрисовки Windows Server 2016

Приведенный ниже список относится к образам визуализации Windows Server 2016, версия 1.3.8.

* Autodesk Maya I/O 2017 обновление 5 (версия 17.4.5459);
* Autodesk Maya I/O 2018 с обновлением 6 (версия 18.4.0.7622)
* Autodesk Maya ввода-вывода 2019
* Autodesk 3ds Max I/O 2018 обновление 4 (версия 20.4.0.4254);
* Autodesk 3ds Max I/O 2019 обновление 1 (версия 21.2.0.2219);
* Autodesk 3ds Max I/O 2020 с обновлением 2
* Autodesk Arnold для Maya 2017 (Arnold версии 5.3.0.2) Мтоа-3.2.0.2-2017
* Autodesk Arnold для Maya 2018 (Arnold версии 5.3.0.2) Мтоа-3.2.0.2-2018
* Autodesk Arnold для Maya 2019 (Arnold версии 5.3.0.2) Мтоа-3.2.0.2-2019
* Autodesk Arnold для 3ds Max 2018 (Arnold версия 5.3.0.2) (версия 1.2.926)
* Autodesk Arnold для 3ds Max 2019 (Arnold версия 5.3.0.2) (версия 1.2.926)
* Autodesk Arnold для 3ds Max 2020 (Arnold версия 5.3.0.2) (версия 1.2.926)
* Chaos Group V-Ray для Maya 2017 (версия 4.12.01)
* Chaos Group V-Ray для Maya 2018 (версия 4.12.01)
* Chaos Group V-Ray для Maya 2019 (версия 4.04.03)
* Chaos Group V-Ray для 3ds Max 2018 (версия 4.20.01)
* Chaos Group V-Ray для 3ds Max 2019 (версия 4.20.01)
* Chaos Group V-Ray для 3ds Max 2020 (версия 4.20.01)
* Blender (2.79).
* Наложение (2,80)
* AZ 10

> [!IMPORTANT]
> Чтобы запустить V-Ray с Maya за пределами [шаблонов расширений пакетной](https://github.com/Azure/batch-extension-templates)службы `vrayses.exe` Azure, запустите перед выполнением подготовки к просмотру. Чтобы запустить врайсес. exe за пределами шаблонов, можно использовать следующую команду `%MAYA_2017%\vray\bin\vrayses.exe"`.
>
> Пример см. в описании задачи запуска [шаблона Maya и V-Ray](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) на сайте GitHub.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Приложения на предыдущих изображениях подготовки к просмотру Windows Server 2016

Приведенный ниже список относится к образам визуализации Windows Server 2016, версия 1.3.7.

* Autodesk Maya I/O 2017 обновление 5 (версия 17.4.5459);
* Autodesk Maya I/O 2018 обновление 4 (версия 18.4.0.7622);
* Autodesk 3ds Max I/O 2019 обновление 1 (версия 21.2.0.2219);
* Autodesk 3ds Max I/O 2018 обновление 4 (версия 20.4.0.4254);
* Autodesk Arnold for Maya 2017 (Arnold версии 5.2.0.1) MtoA-3.1.0.1-2017;
* Autodesk Arnold for Maya 2018 (Arnold версии 5.2.0.1) MtoA-3.1.0.1-2018;
* Autodesk Arnold для 3ds Max 2018 (Arnold версия 5.0.2.4) (версия 1.2.926)
* Autodesk Arnold для 3ds Max 2019 (Arnold версия 5.0.2.4) (версия 1.2.926)
* Chaos Group V-Ray для Maya 2018 (версия 3.52.03)
* Chaos Group V-Ray для 3ds Max 2018 (версия 3.60.02)
* Chaos Group V-Ray для Maya 2019 (версия 3.52.03)
* Chaos Group V-Ray для 3ds Max 2019 (версия 4.10.01)
* Blender (2.79).

> [!NOTE]
> Chaos Group V-Ray для 3ds Max 2019 (версия 4.10.01) вводит критические изменения в V-Ray. Чтобы использовать предыдущую версию (Version 3.60.02), используйте узлы отрисовки Windows Server 2016, версия 1.3.2.

## <a name="next-steps"></a>Следующие шаги

Чтобы использовать образы виртуальных машин для рендеринга, их необходимо указывать в конфигурации пула при создании пула. Подробнее см. статью о [возможностях рендеринга в пакетной службе](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).
