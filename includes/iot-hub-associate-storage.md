---
title: включение файла
description: включение файла
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: f88f6aa6daec342b24d165c4c4cc0a89a0805f7a
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558466"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Связывание учетной записи хранения Azure с Центром Интернета вещей

Так как приложение виртуального устройства отправляет файл в большой двоичный объект, необходимо связать учетную запись [хранилища Azure](../articles/storage/common/storage-quickstart-create-account.md) с центром Интернета вещей. При связывании учетной записи хранения Azure с Центром Интернета вещей Центр Интернета вещей создает URI SAS. Устройство может использовать этот URI SAS для безопасной передачи файла в контейнер больших двоичных объектов. Служба Центра Интернета вещей и пакеты SDK для устройств координируют процесс создания URI SAS, делая его доступным для устройства, которое отправляет файл.

Следуйте инструкциям в статье [Настройка отправки файлов Центра Интернета вещей с помощью портала Azure](../articles/iot-hub/iot-hub-configure-file-upload.md). Убедитесь, что контейнер больших двоичных объектов связан с вашим Центром Интернета вещей и что уведомления файлов включены.

![Включение уведомлений файлов на портале](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
