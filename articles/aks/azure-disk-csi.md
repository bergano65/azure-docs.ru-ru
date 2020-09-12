---
title: Использование драйверов интерфейса хранилища контейнера (CSI) для дисков Azure в службе Kubernetes Azure (AKS)
description: Узнайте, как использовать драйверы интерфейса хранилища контейнера (CSI) для дисков Azure в кластере службы Kubernetes Azure (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 4b5ccd2712a95f5f020daa0161f1b5908a38a62e
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422089"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Использование драйверов интерфейса хранилища контейнера дисков Azure (CSI) в службе Kubernetes Azure (AKS) (Предварительная версия)
Драйвер CSI для дисков Azure — это драйвер, совместимый с [CSI спецификацией](https://github.com/container-storage-interface/spec/blob/master/spec.md) , используемый AKS для управления жизненным циклом дисков Azure. 

Интерфейс хранилища контейнера (CSI) является стандартом для предоставления произвольных блоков и систем хранения файлов в контейнерные рабочие нагрузки в Kubernetes. Принимая и используя CSI, служба Kubernetes Azure (AKS) может писать, развертывать и выполнять итерацию подключаемых модулей, предоставляя новые или улучшающие существующие системы хранения в Kubernetes без необходимости касаться основного кода Kubernetes и ожидания циклов выпуска.

Сведения о создании кластера AKS с поддержкой драйвера CSI см. в статье [Включение драйверов CSI для дисков Azure и файлов Azure в AKS](csi-storage-drivers.md).

>[!NOTE]
> *"Драйверы в дереве"* относятся к текущим драйверам хранилища, которые являются частью основного кода kubernetes, и новыми ДРАЙВЕРами CSI, которые являются подключаемыми модулями.

## <a name="use-csi-persistent-volumes-pv-with-azure-disks"></a>Использование постоянных томов CSI (ПС) с дисками Azure 

[Постоянный том](concepts-storage.md#persistent-volumes) представляет собой часть хранилища, подготовленную для использования с Kubernetes Pod. Постоянный том может использоваться одним или несколькими модулями и может быть подготовлен динамически или статически. В этой статье описывается динамическое создание постоянных томов с дисками Azure, используемых одним модулем pod в кластере службы Azure Kubernetes (AKS). Сведения о статической подготовке см. [в статье Создание и использование тома с дисками Azure вручную](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Дополнительные сведения о томах Kubernetes см. в статье, [посвященной возможностям хранения данных приложений в AKS][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes"></a>Динамическое создание постоянного хранилища версий диска Azure с помощью встроенных классов хранения

Класс хранения используется для определения того, как единица хранения создается динамически с помощью постоянного тома. Дополнительные сведения о классах хранения Kubernetes см. в разделе [Kubernetes Storage Classes][kubernetes-storage-classes] (Классы хранения Kubernetes). При использовании драйверов хранилища CSI в AKS существует 2 дополнительных встроенных `StorageClasses` , использующих **драйверы хранилища Azure CSI**. Дополнительные классы хранения CSI создаются с кластером наряду с классами хранения по умолчанию в дереве.

>[!NOTE]
> *"Драйверы в дереве"* относятся к текущим драйверам хранилища, которые являются частью основного кода kubernetes и драйверов CSI, которые являются подключаемыми модулями.

- `managed-csi` — Использует локально избыточное хранилище Azure Стандардссд (LRS) для создания управляемого диска.
- `managed-csi-premium` — Использует локально избыточное хранилище Azure Premium (LRS) для создания управляемого диска. 

Политика reclaimа в обоих классах хранения гарантирует, что базовый диск Azure будет удален при удалении соответствующего постоянного тома. Классы хранения также настраивают постоянное создание расширяемых томов, поэтому нужно просто изменить утверждение Постоянного тома с новым размером.

Чтобы использовать эти классы хранения, нужно просто создать [постоянное требование к тому (PVC)](concepts-storage.md#persistent-volume-claims) и соответствующий модуль, который ссылается на них и использует их. Утверждение постоянного тома (PVC) используется для автоматической подготовки хранилища на основе класса хранения. PVC может использовать один из предварительно созданных классов хранения или определяемый пользователем класс хранения для создания диска, управляемого Azure, для требуемого номера SKU и размера. При создании определения pod указывается утверждение постоянного тома для запроса требуемого хранилища.

Создайте пример Pod и соответствующее утверждение Постоянного тома с помощью команды [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

После того как модуль Pod находится в состоянии выполняется, создайте новый файл с именем `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Теперь можно проверить, правильно ли подключен диск, выполнив следующую команду и убедившись, что `test.txt` файл отображается в выходных данных: 

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Создание пользовательского класса хранилища

Классы хранения по умолчанию соответствуют наиболее распространенным сценариям, но не всем. В некоторых случаях может потребоваться настроить собственный класс хранения с собственными параметрами. Для проиллюстрировать существует сценарий, в котором может потребоваться изменить `volumeBindingMode` . 

Классы хранения по умолчанию используют объект `volumeBindingMode: Immediate` , гарантирующий, что происходит сразу же после создания персистентволумеклаим. В случаях, когда пулы узлов ограничены топологией, например с помощью Зоны доступности, постоянные тома будут привязаны или подготовлены без знания требований к планированию Pod (в данном случае в определенной зоне).

Для решения этой ситуации можно использовать `volumeBindingMode: WaitForFirstConsumer` , который будет задерживать привязку и подготовку персистентволуме до тех пор, пока не будет создан модуль, использующий персистентволумеклаим. Таким образом, ПС будет соответствовать и подготовлен в зоне доступности (или другой топологии), указанной в ограничениях расписания Pod. 

Создайте файл с именем `sc-azuredisk-csi-waitforfirstconsumer.yaml` и вставьте приведенный ниже манифест.
Класс хранения совпадает `managed-csi` с классом хранения, но с другим `volumeBindingMode` . 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Создайте класс хранения с помощью команды [kubectl Apply][kubectl-apply] и укажите `sc-azuredisk-csi-waitforfirstconsumer.yaml` файл:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Моментальные снимки томов

Драйвер CSI диска Azure поддерживает создание [моментальных снимков постоянных томов](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). В рамках этой возможности драйвер может выполнять *полные* или [ *добавочные* моментальные снимки](../virtual-machines/windows/disks-incremental-snapshots.md) в зависимости от значения, заданного в `incremental` параметре (по умолчанию это значение равно true). 

Дополнительные сведения о всех параметрах см. в разделе [Параметры класса моментальных снимков тома](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Создание моментального снимка тома

Чтобы проиллюстрировать эту возможность, создайте [класс моментальных снимков томов](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) с помощью команды [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Теперь создадим [моментальный снимок тома](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) из постоянной виртуальной цепи, [созданной динамически в начале этого руководства](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes), `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Проверьте, правильно ли создан моментальный снимок:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Создание новой PVC на основе моментального снимка тома

Вы можете создать новую виртуальную цепь на основе моментального снимка тома. Используйте моментальный снимок, созданный на предыдущем шаге, и создайте [новую виртуальную цепь](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) и [новый модуль](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) для его использования.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Наконец, убедитесь, что это та же виртуальная цепь, созданная перед проверкой содержимого.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Как и ожидалось, мы по-прежнему можем увидеть созданный ранее `test.txt` файл.

## <a name="clone-volumes"></a>Клонирование томов

Клонированный том определяется как дубликат существующего тома Kubernetes. Дополнительные сведения о клонировании томов в Kubernetes см. в концептуальной документации по [клонированию томов](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning).

Драйвер CSI для дисков Azure поддерживает клонирование томов. Чтобы продемонстрировать, создайте [клонированный том](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) [ранее созданного](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) `azuredisk-pvc` и [нового Pod для использования](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).



```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Теперь можно проверить содержимое клонированного тома, запустив ниже и убедившись, что мы по-прежнему видим `test.txt` созданный файл.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume-pv"></a>Изменение размера постоянного тома (ПС)

Вместо этого можно запросить больший объем для PVC. Измените объект PVC и укажите больший размер. Это изменение активирует расширение базового тома, который производит резервное копирование Персистентволуме. 

> [!NOTE] 
> Новый Персистентволуме никогда не будет создан для удовлетворения утверждения. Вместо этого размер существующего тома изменяется.

В AKS встроенный `managed-csi` класс хранения уже допускает расширение, поэтому используйте [PVC, созданный ранее с этим классом хранения](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes). Виртуальная цепь запросила постоянный том 10Gi. мы можем подтвердить это, выполнив команду:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```
> [!IMPORTANT]
> В настоящее время драйвер CSI диска Azure поддерживает только изменение размера PVC без связанных модулей Pod (и том, не подключенный к определенному узлу).

Таким образом можно удалить созданный ранее модуль:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Развернем PVC, увеличив `spec.resources.requests.storage` поле:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Давайте подтверждаем, что том теперь больше:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE] 
> Виртуальная цепь не будет отражать новый размер, пока не будет снова связан с модулем Pod.

Давайте создадим новый Pod:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

И, наконец, подтвердите размер PVC и внутри модуля: 
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

<!--- ## Shared disk

[Azure shared disks](../virtual-machines/windows/disks-shared.md) is an Azure managed disks feature that enables attaching an Azure disk to agent nodes simultaneously. Attaching a managed disk to multiple agent nodes allows you, for example, to deploy new or migrate existing clustered applications to Azure.

> [!IMPORTANT] Currently, only raw block device (`volumeMode: Block`) is supported by the Azure disk CSI driver. Applications should manage the coordination and control of writes, reads, locks, caches, mounts and fencing on the shared disk which is exposed as raw block device.

Let's create file called `shared-disk.yaml` by copying the below that contains the shared disk storage class and PVC:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Create the storage class with the [kubectl apply][kubectl-apply] command and specify your `shared-disk.yaml` file:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Now let's create a file called `deployment-shared.yml` by copying the below:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: nginx
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Create the deployment with the [kubectl apply][kubectl-apply] command and specify your `deployment-shared.yml` file:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Finally, let's check the block device inside the pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp bash
root@deployment-sharedisk-7454978bc6-xh7jp:/# dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 0.0502999 s, 2.1 GB/s
```
-->

## <a name="windows-containers"></a>Контейнеры Windows

Драйвер CSI диска Azure также поддерживает узлы и контейнеры Windows. Если вы хотите использовать контейнеры Windows, следуйте указаниям в [учебнике по контейнерам Windows](windows-container-cli.md) , чтобы добавить пул узлов Windows.

После создания пула узлов Windows теперь можно просто использовать встроенные классы хранения, такие как `managed-csi` . Вы можете развернуть пример [набора с отслеживанием состояния на основе Windows](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) , который сохраняет метки времени в файл `data.txt` , развертывая приведенную ниже команду с помощью команды [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Теперь можно проверить содержимое тома, выполнив команду:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Дальнейшие действия

- Сведения об использовании драйвера CSI для службы файлов Azure см. в статье [использование файлов Azure с](azure-files-csi.md)драйверами CSI.
- Дополнительные сведения о рекомендациях по хранению см. в статье рекомендации [по хранению и резервному копированию в службе Azure Kubernetes Service (AKS)][operator-best-practices-storage] .


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

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