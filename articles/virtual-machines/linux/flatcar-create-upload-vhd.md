---
title: Создание и передача Флаткар контейнера Linux VHD для использования в Azure
description: Узнайте, как создать и передать виртуальный жесткий диск, содержащий Флаткар контейнер операционной системы Linux.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 555e53899ed78a5200009d04659e974f8157057e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87268245"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Использование предварительно созданного образа Флаткар для Azure

Вы можете скачать предварительно созданные образы виртуальных жестких дисков Azure для контейнера Флаткар в Linux для каждого из поддерживаемых каналов Флаткар:

- [работает](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [бета-версия](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [alpha](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [угловой](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

Этот образ уже полностью настроен и оптимизирован для работы в Azure. Его необходимо распаковать.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Создание виртуальной машины на основе Флаткар для Azure

Кроме того, можно создать собственный Флаткар контейнер с образом Linux.

На любом компьютере под управлением Linux выполните инструкции, описанные в разделе [Флаткар Container for Linux Developer SDK](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/). При выполнении `image_to_vm.sh` скрипта убедитесь, что вы прошли `--format=azure` Создание виртуального жесткого диска Azure.

## <a name="next-steps"></a>Дальнейшие действия

Создав VHD-файл, вы можете использовать полученный файл для создания новых виртуальных машин в Azure. Если вы отправляете VHD-файл в Azure в первый раз, см. статью [Создание виртуальной машины Linux на основе пользовательского диска](upload-vhd.md#option-1-upload-a-vhd).
