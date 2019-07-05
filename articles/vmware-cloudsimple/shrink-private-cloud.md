---
title: Сжать решение VMware в Azure с CloudSimple частного облака
description: Содержатся инструкции по сжатию CloudSimple частного облака.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e639feb603f1654b4dcd40f16d8e3094307839a
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544521"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Сжать CloudSimple частного облака

CloudSimple вы получаете возможность динамически сжать частного облака.  Частное облако состоит из одного или нескольких кластерах vSphere. Каждый кластер может содержать 3 до 16 узлов. При сжатии частного облака, можно удалить узел из существующего кластера или удалить весь кластер. 

## <a name="before-you-begin"></a>Перед началом работы

Следующие условия должны быть выполнены для сжатия частного облака.  Управление кластером (первый кластер) создан при создании частного облака нельзя удалить.

* В кластере vSphere должен иметь три узла.  Нельзя сжать кластер с узлами только три.
* Общий объем занятого хранилища не должна превышать общую емкость после сжатия кластера. 

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Сжать частного облака 

1. [Доступ к порталу CloudSimple](access-cloudsimple-portal.md).

2. Откройте **ресурсы** страницы.

3. Щелкните частное облако, которую нужно сжать

4. На странице "Сводка" нажмите кнопку **Сжать**.

    ![Сжать частного облака](media/shrink-private-cloud.png)

5. Выберите кластер, который вы хотите уменьшить или удалить. 

    ![Сжать частного облака — Выбор кластера](media/shrink-private-cloud-select-cluster.png)

6. Выберите **удалить один узел** или **удалить весь кластер**. 

7. Проверьте ресурсы кластера

8. Нажмите кнопку **отправить** сжатие частного облака.

Запускает сжатия частного облака.  Вы можете отслеживать ход выполнения задачи.  Процесс сжатия может занять несколько часов в зависимости от данных, который должен быть resynced в виртуальной сети хранения данных.

## <a name="next-steps"></a>Дальнейшие действия

* [Использование виртуальных машин VMware в Azure](quickstart-create-vmware-virtual-machine.md)
* Дополнительные сведения о [частных облаков](cloudsimple-private-cloud.md)