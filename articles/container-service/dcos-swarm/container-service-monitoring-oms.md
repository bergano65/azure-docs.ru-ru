---
title: (УСТАРЕЛО) Мониторинг кластера Azure DC/OS с помощью Operations Management
description: Мониторинг кластера DC/OS Службы контейнеров Azure с использованием Log Analytics.
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 290141136672729060f5156d645c47ac303fa0c3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110976"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(УСТАРЕЛО) Мониторинг кластера DC/OS Службы контейнеров Azure с использованием Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics — это облачное решение Майкрософт для управления ИТ-средой, которое помогает управлять локальной и облачной инфраструктурой и защищать ее. В Log Analytics реализовано решение для контейнеров, которое помогает просматривать сведения, касающиеся инвентаризации и производительности контейнеров, а также соответствующие журналы в одном расположении. Оно позволяет выполнять аудит и устранять неполадки контейнеров, просматривая журналы в централизованном расположении, а также находить контейнеры с высоким уровнем потребления ресурсов на узле.

![](media/container-service-monitoring-oms/image1.png)

Дополнительные сведения об этом решении см. в статье [Решение для мониторинга контейнеров в Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Настройка Log Analytics в среде DC/ОS


В этой статье предполагается, что вы настроили DC/OS и развернули простые приложения веб-контейнера в кластере.

### <a name="pre-requisite"></a>Предварительные требования
- [Подписка Microsoft Azure](https://azure.microsoft.com/free/) — ее можно получить бесплатно.  
- Настройка рабочей области Log Analytics (см. раздел "Шаг 3" ниже).
- Установленный [интерфейс командной строки DC/OS](https://docs.mesosphere.com/1.12/cli).

1. На панели мониторинга DC/OS щелкните "Вселенная" и выполните поиск по запросу "OMS", как показано ниже.

   >[!NOTE]
   >OMS теперь называется Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Щелкните **Install**(Установить). Отобразится всплывающее окно со сведениями о версии и кнопкой **Установить пакет** или **Advanced Installation** (Расширенная установка). Нажав кнопку **Advanced Installation** (Расширенная установка), вы перейдете на страницу **OMS specific configuration properties** (Свойства конфигурации OMS).

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Здесь вам будет предложено ввести `wsid` (идентификатор рабочей области Log Analytics) и `wskey` (первичный ключ для идентификатора рабочей области). Чтобы получить `wsid` и `wskey`, необходимо создать учетную запись по адресу <https://mms.microsoft.com>.
   Чтобы создать учетную запись, следуйте инструкциям. После создания учетной записи необходимо получить `wsid` и `wskey`, щелкнув **Параметры**, **Подключенные источники**, а затем — **Серверы с Linux**, как показано ниже.

   ![](media/container-service-monitoring-oms/image5.png)

4. Выберите необходимое количество экземпляров и нажмите кнопку Review and Install (Просмотреть и установить). Как правило, требуется количество экземпляров, равное количеству виртуальных машин в кластере агента. Агент Log Analytics для Linux устанавливается в качестве отдельных контейнеров на каждой виртуальной машине, о которой необходимо собирать сведения для мониторинга и ведения журнала.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Настройка простой панели мониторинга Log Analytics

Сразу после установки агента Log Analytics для Linux на виртуальных машинах необходимо настроить панель мониторинга Log Analytics. Панель мониторинга можно настроить на портале Azure.

### <a name="azure-portal"></a>Портал Azure 

Войдите на портал Azure <https://portal.microsoft.com/>. Выберите **Marketplace**, **Мониторинг и управление** и **Показать все**. Затем в поле поиска введите `containers`. В результатах отобразится "контейнеры". Выберите **Контейнеры** и нажмите кнопку **Создать**.

![](media/container-service-monitoring-oms/image9.png)

После нажатия кнопки **Создать** вам будет предложено выбрать рабочую область. Выберите свою рабочую область, а если у вас ее нет, создайте ее.

![](media/container-service-monitoring-oms/image10.PNG)

Выбрав рабочую область, щелкните **Создать**.

![](media/container-service-monitoring-oms/image11.png)

Дополнительные сведения см. в статье [Решение для мониторинга контейнеров в Log Analytics](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Масштабирование агента Log Analytics с помощью среды DC/OS службы ACS 

Если установлен агент Log Analytics с недостаточным количеством фактических узлов или требуется выполнить масштабирование масштабируемого набора виртуальных машин путем добавления дополнительных виртуальных машин, можно масштабировать службу `msoms`.

Вы можете перейти в Marathon или на вкладку пользовательских служб DC/OS и масштабировать количество узлов.

![](media/container-service-monitoring-oms/image12.PNG)

Другие узлы, на которых еще не установлен агент Log Analytics, также будут развернуты.

## <a name="uninstall-ms-oms"></a>Удаление MS OMS

Чтобы удалить MS OMS, введите следующую команду:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Сообщите нам!
Что работает? Чего не хватает? Что еще необходимо улучшить, чтобы этот инструмент был полезным для вас? Сообщите нам по адресу <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Дальнейшие действия

 Теперь, когда вы настроили Log Analytics для мониторинга контейнеров, [просмотрите свою панель мониторинга для контейнера](../../azure-monitor/insights/containers.md).
