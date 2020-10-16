---
title: Учебник. Фильтрация и анализ данных с помощью вычислений на устройстве Azure Stack Edge Pro с GPU | Документация Майкрософт
description: Сведения о том, как настроить роль вычислений в Azure Stack Edge Pro с GPU и использовать ее для преобразования данных перед их отправкой в Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 711da24b3edf08f4867109d0d70165955236c39a
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743426"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Руководство по Настройка вычислений на устройстве Azure Stack Edge Pro с GPU

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

В этом учебнике описывается, как настроить роль вычислений и создать кластер Kubernetes на устройстве Azure Stack Edge Pro. 

Эта процедура может занять от 20 до 30 минут.


В этом руководстве описано следующее:

> [!div class="checklist"]
> * настройка вычислений;
> * Получение конечных точек Kubernetes

 
## <a name="prerequisites"></a>Предварительные требования

Прежде чем вы настроите роль вычислений на устройстве Azure Stack Edge Pro, убедитесь, что:

- Устройство Azure Stack Edge Pro активировано, как описано в статье [Активация Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).
- Убедитесь, что вы выполнили инструкции, приведенные в разделе [Включение сети вычислений](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network), и сделали следующее:
    - включили сетевой интерфейс для служб вычислений;
    - назначили IP-адреса узлов Kubernetes и внешних служб Kubernetes.

## <a name="configure-compute"></a>настройка вычислений;

Чтобы настроить вычисления в Azure Stack Edge Pro, необходимо создать ресурс Центра Интернета вещей на портале Azure.

1. На портале Azure своего ресурса Azure Stack Edge перейдите к разделу **Обзор**. В области справа на плитке **Вычисления** щелкните **Начало работы**.

    ![Начало работы с вычислениями](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. На плитке **Настройка вычислений Edge** щелкните **Настройка вычислений**.

    ![настройка вычислений;](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. В колонке **Настройка вычислений Edge** введите следующие значения:

   
    |Поле  |Значение  |
    |---------|---------|
    |Центр Интернета вещей     | Выберите **Новый** или **Существующий**. <br> По умолчанию для создания ресурса Интернета вещей используется уровень служб "Стандартный" (S1). Чтобы использовать ресурс Интернета вещей уровня служб "Бесплатный", создайте его и выберите существующий ресурс. <br> В любом случае для ресурса Интернета вещей используется та же подписка и группа ресурсов, что и для ресурса Azure Stack Edge.     |
    |Имя     |Введите имя ресурса Центра Интернета вещей.         |

    ![Начало работы с вычислениями 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Нажмите кнопку **создания**. Для создания ресурса Центра Интернета вещей нужно несколько минут. После этого на плитке **Настройка вычислений** появляется конфигурация вычислений. 

    ![Начало работы с вычислениями 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Чтобы подтвердить настройку роли вычислений Edge, на плитке **Настройка вычислений** щелкните **Просмотреть вычисления**.
    
    ![Начало работы с вычислениями 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

    > [!NOTE]
    > Если диалоговое окно **Настройка вычислений** закрыть, прежде чем устройство Azure Stack Edge Pro будет связано с Центром Интернета вещей, Центр Интернета вещей будет создан, но не отобразится в настройках вычислений. 
    
При настройке роли вычислений Edge на устройстве Edge создается два устройства — устройство Интернета вещей и устройство IoT Edge. Оба устройства можно просмотреть в ресурсе Центра Интернета вещей. На этом устройстве IoT Edge также запущена среда выполнения IoT Edge. На данный момент доступна только платформа Linux для устройства IoT Edge.

Настройка вычислений может занять 20–30 минут, так как в фоновом режиме создаются виртуальные машины и кластер Kubernetes. 

После успешной настройки вычислений на портале Azure вам будут доступны кластер Kubernetes и пользователь по умолчанию, связанный с пространством имен Интернета вещей (системное пространство имен, контролируемое Azure Stack Edge Pro). 

## <a name="get-kubernetes-endpoints"></a>Получение конечных точек Kubernetes

Чтобы настроить клиент для доступа к кластеру Kubernetes, вам понадобится конечная точка Kubernetes. Выполните приведенные ниже действия, чтобы получить конечную точку API Kubernetes из локального пользовательского интерфейса устройства Azure Stack Edge Pro.

1. Перейдите на страницу **Устройства** в локальном пользовательском веб-интерфейсе устройства.
2. В разделе **Device endpoints** (Конечные точки устройства) скопируйте конечную точку **службы API Kubernetes**. Эта конечная точка представляет собой строку в следующем формате: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]`. 

    ![Страница "Устройство" в локальном пользовательском интерфейсе](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Сохраните строку конечной точки. Вы будете использовать ее позже при настройке клиента для доступа к кластеру Kubernetes через kubectl.

4. Находясь в локальном веб-интерфейсе, вы можете выполнить следующее.

    - Перейдите в API Kubernetes, выберите **дополнительные параметры** и скачайте расширенный файл конфигурации для Kubernetes. 

        ![Страница "Устройство" в локальном пользовательском интерфейсе 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Вы можете использовать этот файл конфигурации, если вам предоставлен ключ от Майкрософт (он может быть у некоторых пользователей).

        ![Страница "Устройство" в локальном пользовательском интерфейсе 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - Вы также можете перейти к конечной точке **панели мониторинга Kubernetes** и скачать файл конфигурации `aseuser`. 
    
        ![Страница "Устройство" в локальном пользовательском интерфейсе 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        Этот файл конфигурации можно использовать для входа на панель мониторинга Kubernetes или отладки любых проблем в кластере Kubernetes. Дополнительные сведения см. в разделе [Доступ к панели мониторинга](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 


## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * настройка вычислений;
> * Получение конечных точек Kubernetes


Сведения об администрировании устройства Azure Stack Edge Pro см. в следующей статье:

> [!div class="nextstepaction"]
> [Использование локального пользовательского веб-интерфейса для администрирования Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
