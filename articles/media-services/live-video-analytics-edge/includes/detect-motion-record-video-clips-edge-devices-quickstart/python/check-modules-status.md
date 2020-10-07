---
ms.openlocfilehash: e46a56742ab8b98c53c1cd05e840e8ad4b8a73da
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682306"
---
На шаге [Создание и развертывание манифеста развертывания IoT Edge](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) в Visual Studio Code, разверните узел **lva-sample-device** в **Центр Интернета вещей Azure** (в разделе слева внизу). Вы должны увидеть развертывание следующих модулей:

* Модуль Аналитики видеотрансляций `lvaEdge`.
* Модуль `rtspsim`, имитирующий RTSP-сервер, выступающий в качестве источника веб-канала видеотрансляции

  ![Модули](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Если вы используете собственное пограничное устройство вместо того, которое было подготовлено с помощью нашего скрипта установки, перейдите на свое пограничное устройство и выполните следующие команды с **правами администратора**, чтобы извлечь и сохранить пример видеофайла, используемый для этого краткого руководства.  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
