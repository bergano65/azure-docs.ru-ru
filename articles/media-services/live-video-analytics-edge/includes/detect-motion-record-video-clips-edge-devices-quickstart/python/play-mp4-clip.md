---
ms.openlocfilehash: f4a1cc432a50a555fe6e050ca318b4cfaf1092d4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682364"
---
MP4-файлы записываются в каталог на пограничном устройстве, указанном в файле *.env* с помощью ключа OUTPUT_VIDEO_FOLDER_ON_DEVICE. Если использовано значение по умолчанию, результаты должны находиться в папке */var/media/* .

Чтобы воспроизвести MP4-файл:

1. Перейдите к группе ресурсов, найдите виртуальную машину, а затем подключитесь с помощью Бастиона Azure.

    ![Группа ресурсов](../../../media/quickstarts/resource-group.png)
    
    ![ВМ](../../../media/quickstarts/virtual-machine.png)
1. Войдите, используя учетные данные, созданные при [настройке ресурсов Azure](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. В командной строке перейдите в соответствующий каталог. Расположение по умолчанию — */var/media*. В каталоге должны отобразиться файлы MP4.

    ![Выходные данные](../../../media/quickstarts/samples-output.png) 

1. Используйте [безопасное копирование (SCP)](../../../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) для копирования файлов на локальный компьютер. 
1. Воспроизведите файлы с помощью [проигрывателя мультимедиа VLC](https://www.videolan.org/vlc/) или любого другого проигрывателя, поддерживающего формат MP4.
