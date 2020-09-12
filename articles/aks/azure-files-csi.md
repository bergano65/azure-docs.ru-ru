---
title: Использование драйверов интерфейса хранилища контейнера (CSI) для службы файлов Azure в службе Kubernetes Azure (AKS)
description: Узнайте, как использовать драйверы интерфейса хранилища контейнера (CSI) для службы файлов Azure в кластере Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 018275b6db4c2d2d1059f35077f74a6f45ec3ba9
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422084"
---
# <a name="use-the-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Использование драйверов интерфейса хранилища контейнера файлов Azure (CSI) в службе Kubernetes Azure (AKS) (Предварительная версия)
Драйвер CSI файлов Azure — это драйвер, совместимый с [CSI спецификацией](https://github.com/container-storage-interface/spec/blob/master/spec.md) , используемый AKS для управления жизненным циклом файловых ресурсов Azure. 

Интерфейс хранилища контейнера (CSI) является стандартом для предоставления произвольных блоков и систем хранения файлов в контейнерные рабочие нагрузки в Kubernetes. Принимая и используя CSI, служба Kubernetes Azure (AKS) теперь может создавать, развертывать и перебирать подключаемые модули, предоставляя новые или улучшающие существующие системы хранения в Kubernetes без необходимости касаться основного кода Kubernetes и ожидания циклов выпуска.

Сведения о создании кластера AKS с поддержкой драйвера CSI см. в статье [Включение драйверов CSI для дисков Azure и файлов Azure в AKS](csi-storage-drivers.md).

>[!NOTE]
> *"Драйверы в дереве"* относятся к текущим драйверам хранилища, которые являются частью основного кода kubernetes, и новыми ДРАЙВЕРами CSI, которые являются подключаемыми модулями.

## <a name="use-a-persistent-volume-pv-with-azure-files"></a>Использование постоянного тома (ПС) с файлами Azure

[Постоянный том](concepts-storage.md#persistent-volumes) представляет собой часть хранилища, подготовленную для использования с Kubernetes Pod. Постоянный том может использоваться одним или несколькими модулями и может быть подготовлен динамически или статически. Если несколько объектов pod требуют одновременный доступ в одно и то же хранилище, используйте службу Файлов Azure для подключения с помощью [протокола Server Message Block (SMB)][smb-overview]. В этой статье показано, как в кластере Службы Azure Kubernetes с помощью нескольких модулей pod предоставлять общий доступ к динамическому созданию файлов Azure. Сведения о статической подготовке см. [в статье Создание и использование тома с файловым ресурсом Azure вручную](azure-files-volume.md).

Дополнительные сведения о томах Kubernetes см. в статье, [посвященной возможностям хранения данных приложений в AKS][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-using-the-built-in-storage-classes"></a>Динамическое создание файлов Azure постоянного хранилища версий с помощью встроенных классов хранения
Класс хранения используется для определения того, как создается файловый ресурс Azure. Для хранения общих папок Azure учетная запись хранения создается автоматически в [группе ресурсов узла][node-resource-group] для использования с классом хранилища. Выберите один из следующих [вариантов избыточности хранилища Azure][storage-skus] для *skuName*:

* *Standard_LRS* — стандартное локально избыточное хранилище
* *Standard_GRS* — стандартное геоизбыточное хранилище
* *Standard_ZRS* — хранилище, избыточное по стандартным зонам
* *Standard_RAGRS* — стандартное геоизбыточное хранилище с доступом для чтения
* Локально избыточное хранилище уровня "Премиум" *Premium_LRS*

> [!NOTE]
> Служба файлов Azure поддерживает хранилище класса Premium, Минимальная общая папка Premium — 100 ГБ.

При использовании драйверов хранилища CSI на AKS существует 2 дополнительных встроенных `StorageClasses` , которые используют **драйверы хранилища CSI для службы файлов Azure**. Дополнительные классы хранения CSI создаются с кластером наряду с классами хранения по умолчанию в дереве.

- `azurefile-csi` — Использует хранилище Azure уровня "Стандартный" для создания файлового ресурса Azure. 
- `azurefile-csi-premium` — Использует хранилище Azure класса Premium для создания файлового ресурса Azure. 

Политика reclaimа на оба класса хранения гарантирует, что базовый файловый ресурс Azure будет удален при удалении соответствующего постоянного тома. Классы хранения также настраивают файловые ресурсы для расширения, поэтому нужно просто изменить утверждение Постоянного тома с новым размером.

Чтобы использовать эти классы хранения, создайте [постоянное утверждение тома (PVC)](concepts-storage.md#persistent-volume-claims) и соответствующий модуль, который ссылается на них и использует их. Утверждение постоянного тома (PVC) используется для автоматической подготовки хранилища на основе класса хранения. PVC может использовать один из предварительно созданных классов хранения или определяемый пользователем класс хранения для создания общего ресурса службы файлов Azure для требуемого номера SKU и размера. При создании определения pod указывается утверждение постоянного тома для запроса требуемого хранилища.

Создайте [Пример постоянного тома и модуля Pod, который выводит текущую дату в `outfile` ](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) с помощью команды [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

После того как модуль Pod находится в состоянии выполняется, можно проверить, что файловый ресурс правильно подключен, выполнив приведенную ниже команду и убедившись, что выходные данные содержат `outfile` : 

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Создание пользовательского класса хранилища

Классы хранения по умолчанию соответствуют наиболее распространенным сценариям, но не всем. В некоторых случаях может потребоваться настроить собственный класс хранения с собственными параметрами. Например, используйте следующий манифест для настройки `mountOptions` общей папки.

Значение по умолчанию для *fileMode* и *дирмоде* — *0777* для подключенных файловых ресурсов Kubernetes. Можно указать различные параметры монтирования для объекта класса хранения.

Создайте файл с именем `azure-file-sc.yaml` и вставьте следующий пример манифеста: 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

Создайте класс хранения с помощью команды [kubectl apply][kubectl-apply].

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Драйвер CSI файлов Azure поддерживает создание [моментальных снимков постоянных томов](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) и базовых файловых ресурсов. 

Создайте [класс моментальных снимков томов](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) с помощью команды [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Создайте [моментальный снимок тома](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) из постоянной виртуальной цепи, [созданной динамически в начале этого руководства](#dynamically-create-azure-files-pvs-using-the-built-in-storage-classes), `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Убедитесь, что моментальный снимок создан правильно:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume-pv"></a>Изменение размера постоянного тома (ПС)

Вы можете запросить больший объем для PVC. Измените объект PVC и укажите больший размер. Это изменение активирует расширение базового тома, который производит резервное копирование Персистентволуме. 

> [!NOTE] 
> Новый Персистентволуме никогда не будет создан для удовлетворения утверждения. Вместо этого размер существующего тома изменяется.

В AKS встроенный `azurefile-csi` класс хранения уже поддерживает расширение, поэтому используйте [PVC, созданный ранее с этим классом хранения](#dynamically-create-azure-files-pvs-using-the-built-in-storage-classes). PVC запросил общую папку 100Gi. мы можем подтвердить это, выполнив команду:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Разверните PVC, увеличив `spec.resources.requests.storage` поле:

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Убедитесь, что виртуальная цепь и файловая система в Pod отображают новый размер:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```

## <a name="windows-containers"></a>Контейнеры Windows

Драйвер CSI файлов Azure также поддерживает узлы и контейнеры Windows. Если вы хотите использовать контейнеры Windows, следуйте указаниям в [учебнике по контейнерам Windows](windows-container-cli.md) , чтобы добавить пул узлов Windows.

Создав пул узлов Windows, используйте встроенные классы хранения, например `azurefile-csi` или создайте пользовательские. Вы можете развернуть пример [набора с отслеживанием состояния на основе Windows](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) , который сохраняет метки времени в файл `data.txt` , развертывая приведенную ниже команду с помощью команды [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Проверьте содержимое тома, выполнив команду:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Дальнейшие действия

- Сведения об использовании драйвера CSI для дисков Azure см. в статье [Использование дисков Azure с драйверами CSI](azure-disk-csi.md).
- Дополнительные сведения о рекомендациях по хранению см. в статье рекомендации [по хранению и резервному копированию в службе Azure Kubernetes Service (AKS)][operator-best-practices-storage] .


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md