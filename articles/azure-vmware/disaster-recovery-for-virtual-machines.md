---
title: Завершение аварийного восстановления виртуальных машин
description: В этой статье показано, как выполнить аварийное восстановление виртуальных машин с помощью решения VMware для Azure.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 2ccb6546b9b01255e4a28aed79fd0d3ccbc4516c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580188"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Завершение аварийного восстановления виртуальных машин с помощью решения VMware для Azure

Эта статья содержит сведения о выполнении аварийного восстановления виртуальных машин с помощью решения VMware ХККС и использовании частного облака решения Azure VMware в качестве восстановления или целевого сайта.

VMware ХККС предоставляет различные операции, обеспечивающие четкое управление и детализацию в политиках репликации. Доступные операции включают:

- **Обратный** — после аварии. Обратить помогает сделать сайт B исходным сайтом и сайтом, где находится Защищенная виртуальная машина.

- **Пауза** — приостановка текущей политики репликации, связанной с выбранной виртуальной машиной.

- **Возобновить** — приостановить текущую политику репликации, связанную с выбранной виртуальной машиной.

- **Удалить** — удалить текущую политику репликации, связанную с выбранной виртуальной машиной.

- **Синхронизация сейчас** — вне привязанной ИСХОДНОЙ виртуальной машины синхронизации с ЗАЩИЩАЕМОЙ виртуальной машиной.

В этом руководство рассматриваются следующие сценарии репликации:

- Защитите виртуальную машину или группу виртуальных машин.

- Выполните тестирование восстановления виртуальной машины или группы виртуальных машин.

- Восстановление виртуальной машины или группы виртуальных машин.

- Обратная Защита виртуальной машины или группы виртуальных машин.

## <a name="protect-vms"></a>Защита виртуальных машин

1. Войдите в **vSphere Client** на исходном сайте и получите доступ к **подключаемому модулю хккс**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="Параметр ХККС в vSphere" border="true":::

1. Введите область **аварийного восстановления** и выберите **защитить виртуальные машины**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="Параметр ХККС в vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. Выберите источник и удаленные сайты. В этом случае удаленный сайт должен быть частным облаком решения Azure VMware.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="Параметр ХККС в vSphere" border="true":::

1. При необходимости выберите параметры **репликации по умолчанию** .

   - **Включить сжатие:** Рекомендуется для сценариев с низкой пропускной способностью.

   - **Включить замороженной:** Приостанавливает работу виртуальной машины, чтобы обеспечить синхронизацию согласованной копии с удаленным сайтом.

   - **Целевое хранилище:** Удаленное хранилище данных для защищенных виртуальных машин и в частном облаке решения Azure VMware это должно быть хранилище данных vSAN.

   - **Контейнер вычислений:** Удаленный кластер vSphere или пул ресурсов.

   - **Конечная папка:** Удаленная папка назначения, которая является необязательной. Если папка не выбрана, виртуальные машины будут размещены непосредственно в выбранном кластере.

   - **RPO:** Интервал синхронизации между исходной виртуальной машиной и защищенной виртуальной машиной и может находиться в диапазоне от 5 минут до 24 часов.

   - **Интервал моментальных снимков:** Интервал между моментальными снимками.

   - **Число моментальных снимков:** Общее число моментальных снимков в пределах заданного интервала моментальных снимков.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="Параметр ХККС в vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. Выберите одну или несколько виртуальных машин из списка и настройте параметры репликации по мере необходимости.

   По умолчанию виртуальные машины наследуют политику глобальных параметров, настроенную в параметрах репликации по умолчанию. Для каждого сетевого интерфейса в выбранной виртуальной машине настройте **группу удаленных сетевых портов** и нажмите кнопку **Готово** , чтобы запустить процесс защиты.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="Параметр ХККС в vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. Отслеживайте процесс для каждой из выбранных виртуальных машин в той же области аварийного восстановления.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="Параметр ХККС в vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. После защиты виртуальной машины можно просмотреть различные моментальные снимки на вкладке **моментальные снимки** .

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="Параметр ХККС в vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   Желтый треугольник означает, что моментальные снимки и виртуальный объект не проверялись в ходе операции восстановления теста.

   Существуют основные различия между выключенной виртуальной машиной и питанием. На рисунке показан процесс синхронизации для виртуальной машины. Он запускает синхронизацию, пока не завершит первый моментальный снимок, который является полной копией виртуальной машины, а затем заполнит следующие данные в заданном интервале. Для виртуальной машины с питанием выполняется синхронизация копии, после чего виртуальная машина отображается как неактивная, а операция защиты отображается как завершенная.  Если виртуальная машина включена, она запускает процесс синхронизации на удаленном сайте.

## <a name="complete-a-test-recover-of-vms"></a>Завершение тестового восстановления виртуальных машин

1. Войдите в **vSphere Client** на удаленном сайте, который является частным облаком решения Azure VMware. 
1. В **подключаемом модуле хккс**в области аварийного восстановления выберите вертикальные многоточия на любой виртуальной машине, чтобы отобразить меню операции, и выберите **тест восстановить виртуальную машину**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="Параметр ХККС в vSphere" border="true":::

1. Выберите параметры теста и моментальный снимок, который вы хотите использовать для проверки различных состояний виртуальной машины.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="Параметр ХККС в vSphere" border="true":::

1. После выбора **теста**начинается операция восстановления.

1. По завершении вы можете проверить новую виртуальную машину в частном облаке Azure для решения VMware.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="Параметр ХККС в vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. После выполнения тестирования на виртуальной машине или запущенном на нем приложении выполните очистку, чтобы удалить тестовый экземпляр.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="Параметр ХККС в vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>Восстановление виртуальных машин

1. Войдите в **vSphere Client** на удаленном сайте, который является частным облаком решения Azure VMware, и получите доступ к **подключаемому модулю хккс**.

   Для сценария восстановления это группа виртуальных машин, используемых в этом примере.

1. Выберите виртуальную машину для восстановления из списка, откройте меню **действия** и выберите **восстановить виртуальные машины**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="Параметр ХККС в vSphere" border="true":::

1. Настройте параметры восстановления для каждого экземпляра и нажмите кнопку **восстановить** , чтобы начать операцию восстановления.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="Параметр ХККС в vSphere" border="true":::

1. После завершения операции восстановления новые виртуальные машины отобразятся в удаленном vCenter Server инвентаризации.

## <a name="complete-a-reverse-replication-on-vms"></a>Выполнить обратную репликацию на виртуальных машинах

1. Войдите в **клиент vSphere** в частном облаке решения Azure VMware и получите доступ к **подключаемому модулю хккс**.
   
   >[!NOTE]
   >Прежде чем начать обратную репликацию, убедитесь, что исходные виртуальные машины на исходном сайте отключены. Операция завершается ошибкой, если виртуальные машины не выключены.

1. В списке выберите виртуальные машины, которые необходимо реплицировать обратно на исходный сайт, откройте меню **действия** и выберите пункт **обратить**. 
1. Выберите **обратный** , чтобы начать репликацию.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="Параметр ХККС в vSphere" border="true":::

1. Мониторинг в разделе сведений каждой виртуальной машины.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="Параметр ХККС в vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>Автоматизация планов аварийного восстановления

В настоящее время VMware ХККС не имеет встроенного механизма для создания и автоматизации плана аварийного восстановления. Однако VMware ХККС предоставляет набор API-интерфейсов, включая интерфейсы API для операции аварийного восстановления. Доступ к спецификации API можно получить в диспетчере виртуальных машин VMware ХККС в URL-адресе.

Эти API охватывают следующие операции аварийного восстановления.

- Защита

- Recover

- Проверка восстановления

- Запланированное восстановление

- Reverse

- Запрос

- Очистка теста

- Пауза

- Возобновить

- Снять защиту

- Перенастройка

Ниже приведен пример полезных данных операции восстановления в JSON.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Используя эти API, клиент может создать собственный механизм для автоматизации создания и выполнения плана аварийного восстановления.
