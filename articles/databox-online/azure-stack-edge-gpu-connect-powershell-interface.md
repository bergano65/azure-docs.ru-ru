---
title: Подключение к Microsoft Azure Stack пограничному устройству и управление им с помощью интерфейса Windows PowerShell | Документация Майкрософт
description: Описывает подключение к Azure Stack границе и управление ей с помощью интерфейса Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 26d4476217d5a6f6abea6093f453cd52d654911e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086158"
---
# <a name="manage-an-azure-stack-edge-gpu-device-via-windows-powershell"></a>Управление устройством GPU Azure Stack ребра с помощью Windows PowerShell

Azure Stack пограничных решений позволяет обрабатывать данные и передавать их по сети в Azure. В этой статье описываются некоторые задачи по настройке и управлению для устройства Azure Stack. Для управления устройством можно использовать портал Azure, локальный веб-интерфейс или интерфейс Windows PowerShell.

В этой статье рассматриваются способы подключения к интерфейсу PowerShell устройства и задачи, которые можно выполнять с помощью этого интерфейса. 


## <a name="connect-to-the-powershell-interface"></a>Подключение к интерфейсу PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Создать пакет поддержки.

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Передача сертификата

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Можно также отправить сертификаты IoT Edge, чтобы обеспечить безопасное подключение между устройством IoT Edge и подчиненными устройствами, которые могут подключаться к нему. Необходимо установить три IoT Edge сертификатов (формат*PEM* ):

- Сертификат корневого ЦС или ЦС владельца
- Сертификат ЦС устройства
- Сертификат ключа устройства

В следующем примере показано использование этого командлета для установки сертификатов IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
При запуске этого командлета вам будет предложено ввести пароль для сетевой папки.

Дополнительные сведения о сертификатах см. в [подAzure IoT Edge сертификаты](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) или [Установка сертификатов на шлюзе](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Просмотр сведений об устройстве
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Просмотр сведений о драйвере GPU

Если на устройстве настроена роль вычислений, можно также получить сведения о драйвере GPU с помощью интерфейса PowerShell. 

1. [Подключитесь к интерфейсу PowerShell](#connect-to-the-powershell-interface).
2. Используйте `Get-HcsGpuNvidiaSmi` для получения сведений о драйвере GPU для вашего устройства.

    В следующем примере показано использование этого командлета:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Запишите сведения о драйвере из примера выходных данных этого командлета.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Включение многопроцессной службы (MPS)

Многопроцессная служба (MPS) на видеопроцессорах NVIDIA предоставляет механизм, в котором графические процессоры могут совместно использоваться несколькими заданиями, в которых каждому заданию выделяется некоторый процент ресурсов GPU. Чтобы включить MPS на Azure Stack пограничном устройстве, выполните следующие действия.

1. Прежде чем начать, убедитесь, что 

    1. Вы настроили и [активировали устройство Azure Stack погранично](azure-stack-edge-gpu-deploy-activate.md) с помощью Azure Stackного ресурса шлюз Data Box в Azure.
    1. Вы [настроили вычисление на этом устройстве в портал Azure](azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Подключитесь к интерфейсу PowerShell](#connect-to-the-powershell-interface).
1. Используйте следующую команду, чтобы включить MPS на устройстве.

    ```powershell
    Start-HcsGpuMPS
    ```

## <a name="reset-your-device"></a>выполнить сброс устройства;

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Получение журналов вычислений

Если на устройстве настроена роль вычислений, можно также получить журналы вычислений с помощью интерфейса PowerShell.

1. [Подключитесь к интерфейсу PowerShell](#connect-to-the-powershell-interface).
2. Используйте `Get-AzureDataBoxEdgeComputeRoleLogs` для получения журналов вычислений для устройства.

    В следующем примере показано использование этого командлета:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Ниже приведено описание параметров, используемых для командлета.
    - `Path`: Укажите сетевой путь к общей папке, в которой нужно создать пакет журнала вычислений.
    - `Credential`: Укажите имя пользователя для сетевой папки. При выполнении этого командлета необходимо указать пароль для общей папки.
    - `FullLogCollection`: Этот параметр гарантирует, что пакет журнала будет содержать все журналы вычислений. По умолчанию пакет журналов содержит только подмножество журналов.



## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Отладка Kubernetes проблем, связанных с IoT Edge

При создании кластера Kubernetes также создается пользователь по умолчанию, `aseuser` связанный с пространством имен System `iotedge` . Для отладки любых проблем, связанных с IoT Edge, можно использовать пространство имен пользователя и системы.  

### <a name="create-config-file-for-system-namespace"></a>Создать файл конфигурации для пространства имен System

Для устранения неполадок сначала создайте `config` файл, соответствующий `iotedge` пространству имен с помощью `aseuser` .

Выполните `Get-HcsKubernetesUserConfig -AseUser` команду и сохраните результат в виде `config` файла (без расширения файла). Сохраните файл в `.kube` папке вашего профиля пользователя на локальном компьютере.

Ниже приведен пример выходных данных `Get-HcsKubernetesUserConfig` команды.

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```

На Azure Stack пограничном устройстве с настроенной ролью вычислений `kubectl` доступны все команды для мониторинга и устранения неполадок модулей. Чтобы просмотреть список доступных команд, выполните `kubectl --help` команду из командного окна.

```PowerShell
C:\Users\myuser>kubectl --help
kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

Полный список `kubectl` команд см. по адресу [ `kubectl` Памятка](https://kubernetes.io/docs/reference/kubectl/cheatsheet/). 

### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Получение IP-адреса службы или модуля, предоставляемых за пределами кластера Kubernetes

Чтобы получить IP-адрес службы балансировки нагрузки или модулей, предоставляемых за пределами Kubernetes, выполните следующую команду:

`kubectl get svc -n iotedge`

Ниже приведен пример выходных данных всех служб или модулей, которые доступны за пределами кластера Kubernetes. 


```powershell
C:\Users\user>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

C:\Users\user>
```
IP-адрес в столбце External IP соответствует внешней конечной точке службы или модуля. Кроме того, [внешний IP-адрес можно получить на панели мониторинга Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


### <a name="to-check-if-module-deployed-successfully"></a>Проверка успешного развертывания модуля

Модули вычислений — это контейнеры, в которых реализована бизнес-логика. Модуль Kubernetes может работать с несколькими контейнерами. Чтобы проверить, успешно ли развернут модуль вычислений, выполните `get pods` команду и проверьте, выполняется ли контейнер (соответствующий модулю вычислений).

Чтобы получить список всех модулей Pod, выполняющихся в определенном пространстве имен, выполните следующую команду:

`get pods -n <namespace>`

Ниже приведен пример выходных данных всех модулей Pod, выполняемых в `iotedge` пространстве имен.

```
C:\Users\myuser>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

C:\Users\myuser>
```

**Состояние состояния означает,** что все модули в пространстве имен выполняются, а значение **Готово** указывает число контейнеров, развернутых в Pod. В приведенном выше примере выполняются все модули Pod, а также выполняется развертывание всех модулей, развернутых в каждом из модулей Pod. 

Чтобы получить более подробные выходные данные определенного модуля Pod для заданного пространства имен, можно выполнить следующую команду:

`kubectl describe pod <pod name> -n <namespace>` 

Пример выходных данных показан здесь.

```
C:\Users\myuser>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


C:\Users\myuser>
```

### <a name="to-get-container-logs"></a>Получение журналов контейнеров

Чтобы получить журналы для модуля, выполните следующую команду:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Поскольку `all-containers` флаг будет выгружать все журналы для всех контейнеров, хорошим способом просмотреть последние ошибки является использование параметра `--tail 10` .

Ниже приведен пример выходных данных. 

```
C:\Users\myuser>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

C:\Users\myuser>
```

## <a name="exit-the-remote-session"></a>Выход из удаленного сеанса

Чтобы выйти из удаленного сеанса PowerShell, закройте окно PowerShell.

## <a name="next-steps"></a>Дальнейшие действия

- Разверните [Azure Stack ребро](azure-stack-edge-gpu-deploy-prep.md) в портал Azure.
