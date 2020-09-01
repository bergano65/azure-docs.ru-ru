---
title: Мониторинг Azure Stack пограничных устройств с помощью панели мониторинга Kubernetes | Документация Майкрософт
description: В этой статье описывается, как получить доступ к панели мониторинга Kubernetes и использовать ее для мониторинга устройства Azure Stack пограничных устройств.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: 7274cef73bff3fb87d55ad636ff0167c8a064796
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180683"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-gpu-device"></a>Использование панели мониторинга Kubernetes для мониторинга устройства GPU Azure Stack ребра

В этой статье описывается, как получить доступ к панели мониторинга Kubernetes и использовать ее для мониторинга устройства GPU Azure Stack. Для мониторинга устройства можно использовать диаграммы в портал Azure, просматривать панель мониторинга Kubernetes или выполнять `kubectl` команды через интерфейс PowerShell устройства. 

Эта статья посвящена только задачам мониторинга, которые можно выполнять на панели мониторинга Kubernetes.

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
>
> * Доступ к панели мониторинга Kubernetes на устройстве
> * Скачать `aseuser` конфигурацию
> * Просмотр модулей, развернутых на устройстве
> * Получение IP-адреса для приложений, развернутых на устройстве
> * Просмотр журналов контейнеров для модулей, развернутых на устройстве


## <a name="about-kubernetes-dashboard"></a>О панели мониторинга Kubernetes

Панель мониторинга Kubernetes — это веб-интерфейс пользователя, который можно использовать для устранения неполадок в контейнерных приложениях. Панель мониторинга Kubernetes — это альтернатива, основанная на пользовательском интерфейсе, в `kubectl` командной строке Kubernetes. Дополнительные сведения см. в статье [Kubernetes Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

На Azure Stack пограничном устройстве можно использовать панель мониторинга Kubernetes в режиме *только для чтения* , чтобы получить общие сведения о приложениях, работающих на Azure Stack пограничном устройстве, просмотреть состояние ресурсов кластера Kubernetes и просмотреть ошибки, произошедшие на устройстве.

## <a name="access-dashboard"></a>Панель мониторинга доступа

Панель мониторинга Kubernetes доступна *только для чтения* и выполняется на главном узле Kubernetes по порту 31000. Чтобы получить доступ к панели мониторинга, выполните следующие действия. 

1. В локальном пользовательском интерфейсе устройства перейдите на **устройство** и перейдите в раздел **конечные точки устройства**. Выберите URL-адрес панели мониторинга Kubernetes, чтобы открыть панель мониторинга в браузере.

    ![URL-адрес панели мониторинга Kubernetes на странице устройства в локальном пользовательском интерфейсе](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. На странице **входа в панель мониторинга Kubernetes** выберите **токен**. 
1. Укажите токен. 
    1. Чтобы получить маркер, [подключитесь через интерфейс PowerShell устройства](azure-stack-edge-gpu-connect-powershell-interface.md).
    1. Выполните команду:  `Get-HcsKubernetesDashboardToken`
    
    1. Скопируйте строку токена, предоставленную вам, в командной строке. Пример выходных данных:
        
        ```powershell
        [10.100.10.10]: PS>Get-HcsKubernetesDashboardToken
        eyJhbGciOiJSUzI1NiIsImtpZCI6IkpFTEtBYTMyZ0Ezb01OYTVFSnVaUV85OWtLdXNETTZQR0k0UlFybGdReFUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi03czZ6ayIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjU3NzY3ZDAzLTJlYWUtNDlkMi1hNDEyLTNkOTU3MDFiMThiMyIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.UgNrpVYVJBEaWxFlljuENUQQmzFXMYG2VsJUIYFdp2AO20zX0k5dRvwcCpeGlqSKb9MyYjG0c6RmT9uCOZk-vAwt7btszQLD7KPCwh_nn_NiIyO8ApgGRYZP8NuP8CBTX3tl_hpwfHtZ0ksbuKAduIL-0uPF0rG5wgLk9cTEw6fKSc2UZW6bIzhNSp_uSiP6MexOS6OftF9JFZejkIGd33dSp-k-tgFlm2Zy96sdFJC0q-XsH7jygiVnfxA9XMs5wqW26LkCh0rfO2WI3C1XFK-4TpufRZLJHo5WPlu-Tnsxa8xmtk2jQ3us-sXcBRrvhPNPrNKkbqc9hbjmWfGD0Q
        [10.100.10.10]: PS>
        ```
        
1. Выберите **Войти**.

    ![Войти на панель мониторинга Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png)

6. Теперь вы можете просмотреть панель мониторинга Kubernetes для устройства Azure Stack пограничных устройств в режиме только для чтения.

    ![Главная страница панели мониторинга Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Просмотр состояния модуля

Модули вычислений — это контейнеры, в которых реализована бизнес-логика. Вы можете использовать панель мониторинга, чтобы проверить, успешно ли развернут модуль вычислений на Azure Stack пограничном устройстве.

ЧТОБЫ просмотреть состояние модуля, выполните следующие действия на панели мониторинга.

1. В левой области панели мониторинга перейдите к **пространству имен**. Отфильтруйте по пространству имен, где отображаются модули IoT Edge, в данном случае **iotedge**.
1. В левой области перейдите к **рабочей нагрузке > развертывания**.
1. На правой панели вы увидите все модули, развернутые на устройстве. В этом случае модуль Жеттингстартедвисгпу был развернут на Azure Stack границе. Вы видите, что модуль был развернут.

    ![Просмотр развертывания модуля](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Получение IP-адреса для служб или модулей

Панель мониторинга можно использовать для получения IP-адресов служб или модулей, которые необходимо предоставить за пределами кластера Kubernetes. 

Диапазон IP-адресов для этих внешних служб назначается через локальный веб-интерфейс устройства на странице « **Параметры вычислений сети** ». После развертывания модулей IoT Edge может потребоваться получить IP-адрес, назначенный конкретному модулю или службе. 

Чтобы получить IP-адрес, выполните следующие действия на панели мониторинга:

1. В левой области панели мониторинга перейдите к **пространству имен**. Фильтрация по пространству имен, в котором развернута внешняя служба, в данном случае **iotedge**.
1. В левой области выберите **Обнаружение и балансировка нагрузки > службы**.
1. На правой панели вы увидите все службы, которые выполняются в `iotedge` пространстве имен на Azure Stack пограничном устройстве.

    ![Получение IP-адреса для внешних служб](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>Просмотр журналов контейнеров

Существуют экземпляры, в которых необходимо просмотреть журналы контейнеров. Панель мониторинга можно использовать для получения журналов для определенного контейнера, развернутого в кластере Kubernetes.

Чтобы просмотреть журналы контейнеров, выполните следующие действия на панели мониторинга.

1. В левой области панели мониторинга перейдите к **пространству имен**. Фильтрация по пространству имен, в котором развернуты IoT Edge модули, в данном случае **iotedge**.
1. В левой области перейдите к **рабочей нагрузке >** модули Pod.
1. На правой панели вы увидите все модули Pod, выполняющиеся на устройстве. Найдите модуль Pod, на котором выполняется модуля, для которого необходимо просмотреть журналы. Выберите вертикальное многоточие для идентифицируемого Pod и в контекстном меню выберите **журналы**.

    ![Просмотр журналов контейнеров 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. Журналы отображаются в средстве просмотра журналов, встроенном в панель мониторинга. Кроме того, можно скачать журналы.

    ![Просмотр журналов контейнеров 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="next-steps"></a>Дальнейшие действия

Сведения об устранении неполадок Kubernetes <!--insert link-->.
