---
title: Устранение распространенных проблем со Службой Azure Kubernetes
description: Узнайте, как устранить распространенные проблемы при использовании Службы Azure Kubernetes (AKS).
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 2c25069ce5231a1f89027dea69579231f0fe4bcd
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517078"
---
# <a name="aks-troubleshooting"></a>Устранение неполадок с AKS

При создании кластеров Azure Kubernetes Service (AKS) или управлении ими иногда вы можете сталкиваться с проблемами. В этой статье описаны некоторые распространенные проблемы и действия по устранению неполадок.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Где найти общие сведения об отладке проблем Kubernetes?

[Вот официальное руководство по устранению неполадок с кластерами Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
[Это руководство по устранению неполадок](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) модулей pod, узлов, кластеров и т. д., опубликованное инженером Майкрософт.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Я получаю ошибку превышения квоты во время создания или обновления. Что мне делать? 

Вам необходимо [запросить ядра](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Каково максимальное число модулей pod на узел, установленное для AKS?

Если вы развертываете кластер AKS на портале Azure, максимальное число модулей pod на узел по умолчанию составляет 30.
Если вы развертываете кластер AKS в Azure CLI, максимальное число модулей pod на узел по умолчанию составляет 110. Убедитесь, что используется последняя версия Azure CLI. Этот параметр по умолчанию можно изменить с помощью флага `–-max-pods` в команде `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Я получаю ошибку insufficientSubnetSize при развертывании кластера AKS с использованием расширенного сетевого взаимодействия. Что мне делать?

Если используется Azure CNI (расширенная сеть), AKS предварительно выделяет IP-адреса на основе настроенного максимального числа pod для каждого узла. В кластере AKS может быть любое число узлов в диапазоне от 1 до 110. На основе настроенного максимального числа модулей pod для каждого узла, размер подсети должен быть больше "произведения количества узлов и максимального числа модулей pod на узел". Следующее основное выражение описывает:

Размер подсети > количество узлов в кластере (с учетом требований к масштабированию в будущем) * максимальное число модулей pod на узел.

Дополнительные сведения см. в разделе [Планирование назначения IP-адресов для кластера](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Мой модуль pod завис в режиме CrashLoopBackOff. Что мне делать?

Модуль pod может зависнуть в этом режиме по различным причинам. Вы можете просмотреть:

* сведения о самом модуле pod с помощью команды `kubectl describe pod <pod-name>`;
* сведения в журналах с помощью команды `kubectl log <pod-name>`.

Дополнительные сведения об устранении неполадок модуля pod см. в статье об [отладке приложений](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Я пытаюсь включить RBAC в существующем кластере. Как это сделать?

К сожалению, включение RBAC в существующих кластерах в настоящее время не поддерживается. Необходимо явно создать кластеры. Если вы используете CLI, RBAC включается по умолчанию. Если вы используете портал AKS, в рабочем процессе создания будет доступен переключатель для включения RBAC.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Кластер создан с поддержкой RBAC помощью Azure CLI со значениями по умолчанию или на портале Azure, при этом на панели мониторинга Kubernetes появились многочисленные предупреждения. Ранее на панели мониторинга не возникало никаких предупреждений. Что мне делать?

Причина отображения предупреждений на панели мониторинга заключается в том, что теперь в кластере включена функция RBAC, в которой доступ к этой панели отключен по умолчанию. В целом этот подход является рекомендуемым, так как раскрытие панели мониторинга по умолчанию для всех пользователей кластера может привести к угрозам безопасности. Если вы по-прежнему хотите включить панель мониторинга, следуйте указаниям в этом [блоге](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Мне не удается подключиться к панели мониторинга. Что мне делать?

Самый простой способ получить доступ к службе за пределами кластера — запустить `kubectl proxy`, который будет запрашивать ваш локальный порт 8001 для подключения к серверу API Kubernetes. После этого сервер API может установить прокси-подключение к вашей службе: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Если вы не видите панель мониторинга Kubernetes, проверьте, запущен ли модуль pod `kube-proxy` в пространстве имен `kube-system`. Если модуль pod находится в нерабочем состоянии, удалите его, и он перезапустится.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Мне не удается получить журналы kubectl, или подключение к серверу API завершается сбоем. Я получаю сообщение об ошибке "сервер: ошибка при вызове внутреннего сервера: вызов TCP...". Что мне делать?

Убедитесь, что группа безопасности сети по умолчанию не изменена и что порт 22 и 9000 открыты для подключения к серверу API. Проверьте, выполняется ли модуль `tunnelfront` в пространстве имен *KUBE-System* с помощью команды `kubectl get pods --namespace kube-system`. Если он не запущен, принудительно удалите его, и он перезапустится.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Я пытаюсь обновить или масштабировать и получаю сообщение об ошибке: изменение свойства "imageReference" недопустимо. Как устранить эту проблему?

Возможно, вы получаете эту ошибку, потому что изменили теги в узлах агента внутри кластера AKS. Изменение и удаление тегов и других свойств ресурсов в группе ресурсов MC_* может привести к непредвиденным результатам. Изменение ресурсов в группе MC_* в кластере AKS нарушает цель уровня обслуживания (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Я получаю ошибки, когда кластер находится в состоянии сбоя, и обновление или масштабирование не будут работать, пока оно не будет исправлено

*Эта помощь по устранению неполадок направляется с https://aka.ms/aks-cluster-failed*

Эта ошибка возникает, когда кластеры перейдет в состояние сбоя по нескольким причинам. Выполните следующие действия, чтобы устранить сбой кластера перед повторным выполнением ранее выполненной операции.

1. Пока кластер не выходит из состояния `failed`, операции `upgrade` и `scale` не будут выполнены. Ниже приведены общие основные проблемы и способы их устранения.
    * Масштабирование с **недостаточной квотой Compute (CRP)** . Чтобы устранить эту проблему, сначала выполните масштабирование кластера до стабильного состояния цели в пределах квоты. Затем выполните следующие [действия, чтобы запросить увеличение квоты вычислений,](../azure-supportability/resource-manager-core-quotas-request.md) прежде чем пытаться увеличить масштаб после превышения квоты на начальные значения.
    * Масштабирование кластера с расширенными сетевыми возможностями и **недостаточными сетевыми ресурсами**. Чтобы устранить эту проблему, сначала выполните масштабирование кластера до стабильного состояния цели в пределах квоты. Затем выполните следующие [действия, чтобы запросить увеличение квоты ресурсов](../azure-resource-manager/resource-manager-quota-errors.md#solution) перед попыткой увеличения масштаба после превышения квоты на начальные значения.
2. После устранения проблемы с обновлением кластер должен находиться в состоянии "успешно". После проверки состояния "успех" повторите исходную операцию.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Возникли ошибки при попытке обновления или масштабирования состояния, в котором выполняется обновление кластера или произошел сбой обновления

*Эта помощь по устранению неполадок направляется с https://aka.ms/aks-pending-upgrade*

Операции обновления и масштабирования в кластере с одним пулом узлов или кластером с [несколькими пулами узлов](use-multiple-node-pools.md) являются взаимоисключающими. Нельзя одновременно обновлять и масштабировать кластер или пул узлов. Вместо этого каждый тип операции должен быть завершен в целевом ресурсе до следующего запроса к этому же ресурсу. В результате операции ограничены при выполнении активных операций обновления или масштабирования, а также при последующей попытке сбоя. 

Чтобы помочь в диагностике проблем, выполняемых `az aks show -g myResourceGroup -n myAKSCluster -o table`, чтобы получить подробные сведения о состоянии кластера. В зависимости от результата:

* Если кластер активно обновляется, дождитесь завершения операции. В случае успеха повторите операцию, которая была выполнена ранее.
* Если произошел сбой обновления кластера, выполните действия, описанные в предыдущем разделе.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Можно ли переместить кластер в другую подписку или подписку с кластером в новый клиент?

Если вы переместили кластер AKS в другую подписку или подписку на другой клиент, кластер потеряет функциональные возможности из-за потери назначений ролей и прав субъектов служб. **AKS не поддерживает перемещение кластеров между подписками или клиентами** из-за этого ограничения.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>При попытке использования функций, требующих масштабируемых наборов виртуальных машин, возникают ошибки

*Эта помощь по устранению неполадок направляется от aka.ms/aks-vmss-enablement*

Могут возникать ошибки, указывающие, что кластер AKS не находится в масштабируемом наборе виртуальных машин, как показано в следующем примере:

**Для Ажентпул "ажентпул" задано автоматическое масштабирование "включено", но оно отсутствует в масштабируемых наборах виртуальных машин**

Чтобы использовать такие функции, как Автомасштабирование кластера или пулы с несколькими узлами, необходимо создать кластеры AKS, использующие масштабируемые наборы виртуальных машин. Ошибки возвращаются при попытке использовать функции, которые зависят от масштабируемых наборов виртуальных машин, и вы используете обычный кластер AKS, не являющийся масштабируемым набором виртуальных машин.

*Перед началом* действий в соответствующем документе выполните действия, чтобы правильно создать кластер AKS:

* [Использование автомасштабирования кластера](cluster-autoscaler.md)
* [Создание и использование пулов с несколькими узлами](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Какие ограничения именования применяются к AKS ресурсам и параметрам?

*Эта помощь по устранению неполадок направляется от aka.ms/aks-naming-rules*

Ограничения именования реализуются как платформой Azure, так и AKS. Если имя или параметр ресурса нарушает одно из этих ограничений, возвращается сообщение об ошибке, предлагающее указать другие входные данные. Применяются следующие общие рекомендации по именованию.

* Имя группы ресурсов AKS *MC_* объединяет имя группы ресурсов и имя ресурса. Автоматически созданный синтаксис `MC_resourceGroupName_resourceName_AzureRegion` не должен превышать 80 символов. При необходимости Сократите длину имени группы ресурсов или имени кластера AKS.
* *DnsPrefix* должен начинаться и заканчиваться буквенно-цифровыми значениями. Допустимые символы включают буквенно-цифровые значения и дефисы (–). *DnsPrefix* не может содержать специальные символы, такие как точка (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>При попытке создать, обновить, масштабировать, удалить или обновить кластер возникают ошибки. Эта операция запрещена, так как выполняется другая операция.

*Эта помощь по устранению неполадок направляется от aka.ms/aks-pending-operation*

Операции с кластером ограничены, если предыдущая операция все еще выполняется. Чтобы получить подробные сведения о состоянии кластера, используйте команду `az aks show -g myResourceGroup -n myAKSCluster -o table`. При необходимости используйте собственную группу ресурсов и имя кластера AKS.

На основе выходных данных состояния кластера:

* Если кластер находится в состоянии подготовки, отличном от " *успешно* " или " *сбой*", дождитесь завершения операции (обновление, обновление,*Создание, масштабирование, удаление или миграция*). После завершения предыдущей операции повторите последнюю операцию с кластером.

* Если в кластере произошел сбой обновления, выполните действия, описанные в разделе " [ошибки", когда кластер находится в состоянии сбоя, а обновление или масштабирование не будет работать до исправления](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Я получаю ошибки, когда субъект-служба не был найден при попытке создать новый кластер без передачи существующего.

При создании кластера AKS требуется субъект-служба для создания ресурсов от вашего имени. AKS предлагает возможность создания нового, созданного во время создания кластера, но это требует Azure Active Directory для полного распространения нового субъекта-службы в разумное время, чтобы кластер был успешно создан. Если распространение занимает слишком много времени, кластер не сможет выполнить проверку, так как не сможет найти доступный субъект-службу. 

Для этого используйте следующие обходные пути.
1. Используйте существующий субъект-службу, который уже распространяется между регионами и существует для передачи в AKS во время создания кластера.
2. При использовании скриптов автоматизации добавьте временные задержки между созданием субъекта-службы и созданием кластера AKS.
3. Если используется портал Azure, вернитесь к параметрам кластера во время создания и повторите страницу проверки через несколько минут.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Я получаю ошибки после того, как ограничит трафик исходящего трафика

При изменении исходящего трафика из кластера AKS есть [обязательные и необязательные рекомендованные](limit-egress-traffic.md) исходящие порты, правила сети и правила полного доменного имени и правил приложения для AKS. Если ваши параметры конфликтуют с любым из этих правил, выполнение определенных `kubectl` команд может оказаться невозможным. При создании кластера AKS могут появиться ошибки.

Убедитесь, что параметры не конфликтуют с какими-либо обязательными или дополнительными правилами для исходящих портов/правил сети, а также с полным доменным именем и правилом приложения.

## <a name="azure-storage-and-aks-troubleshooting"></a>Устранение неполадок службы хранилища Azure и AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Каковы рекомендуемые стабильные версии Kubernetes для диска Azure? 

| Версия Kubernetes | Рекомендуемая версия |
| -- | :--: |
| 1,12 | 1.12.9 или более поздняя версия |
| 1,13 | 1.13.6 или более поздняя версия |
| 1,14 | 1.14.2 или более поздняя версия |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Какие версии Kubernetes имеют поддержку дисков Azure в облаке независимых?

| Версия Kubernetes | Рекомендуемая версия |
| -- | :--: |
| 1,12 | 1.12.0 или более поздняя версия |
| 1,13 | 1.13.0 или более поздняя версия |
| 1,14 | 1.14.0 или более поздняя версия |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Сбой Ваитфораттач для диска Azure: синтаксический анализ "/dev/disk/Azure/SCSI1/lun1": недопустимый синтаксис

В Kubernetes версии 1,10 Маунтволуме. Ваитфораттач может завершиться ошибкой с помощью повторного подключения диска Azure.

В Linux может появиться сообщение об ошибке формата DevicePath. Пример.

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

В Windows может появиться неверный номер устройства DevicePath (LUN). Пример.

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Эта проблема исправлена в следующих версиях Kubernetes:

| Версия Kubernetes | Фиксированная версия |
| -- | :--: |
| 1,10 | 1.10.2 или более поздняя версия |
| 1.11 | 1.11.0 или более поздняя версия |
| 1,12 и более поздние версии | Н/Д |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Сбой при настройке UID и GID в Маунтоптионс для диска Azure

По умолчанию диск Azure использует ext4, XFS FileSystem, а Маунтоптионс, например UID = x, GID = x, не может быть задано во время монтирования. Например, если вы попытались задать Маунтоптионс UID = 999, GID = 999, отобразится следующее сообщение об ошибке:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Вы можете устранить эту ошибку, выполнив одно из следующих действий.

* [Настройте контекст безопасности для Pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) , задав UID в runAsUser и GID в фсграуп. Например, следующий параметр задает запуск модуля Pod от имени root, делает его доступным для любого файла:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Поскольку GID и UID монтируются по умолчанию как root или 0. Если GID или UID задан как не являющийся корневым, например 1000, Kubernetes будет использовать `chown` для изменения всех каталогов и файлов на этом диске. Эта операция может занять много времени и может привести к очень низкому подключению диска.

* Используйте `chown` в Инитконтаинерс, чтобы задать GID и UID. Пример.

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Ошибка при удалении диска Azure Персистентволумеклаим, используемого в Pod

При попытке удаления дискового Персистентволумеклаима Azure, используемого в Pod, может появиться сообщение об ошибке. Пример.

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

В Kubernetes версии 1,10 и более поздних для предотвращения этой ошибки по умолчанию включена функция защиты Персистентволумеклаим. Если вы используете версию Kubernetes, которая не имеет исправления для этой проблемы, можно устранить эту проблему, удалив модуль Pod с помощью Персистентволумеклаим перед удалением Персистентволумеклаим.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Ошибка "не удается найти LUN для диска" при присоединении диска к узлу

При присоединении диска к узлу может появиться следующая ошибка:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Эта проблема исправлена в следующих версиях Kubernetes:

| Версия Kubernetes | Фиксированная версия |
| -- | :--: |
| 1,10 | 1.10.10 или более поздняя версия |
| 1.11 | 1.11.5 или более поздняя версия |
| 1,12 | 1.12.3 или более поздняя версия |
| 1,13 | 1.13.0 или более поздняя версия |
| 1,14 и более поздние версии | Н/Д |

Если вы используете версию Kubernetes, которая не имеет исправления для этой проблемы, можно устранить проблему, добавив несколько минут и повторив повторную попытку.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Сбой подключения или отключения диска Azure, проблемы с подключением или ошибки ввода-вывода во время нескольких операций присоединения или отсоединения

Начиная с Kubernetes версии 1.9.2 при параллельном выполнении нескольких операций присоединения и отсоединения вы можете столкнуться со следующими проблемами с дисками из-за "грязного" кэша виртуальных машин:

* Сбои при присоединении или отсоединении диска
* Ошибки дискового ввода-вывода
* Непредвиденная отсоединение диска от виртуальной машины
* Виртуальная машина работает в состоянии сбоя из-за присоединения несуществующего диска

Эта проблема исправлена в следующих версиях Kubernetes:

| Версия Kubernetes | Фиксированная версия |
| -- | :--: |
| 1,10 | 1.10.12 или более поздняя версия |
| 1.11 | 1.11.6 или более поздняя версия |
| 1,12 | 1.12.4 или более поздняя версия |
| 1,13 | 1.13.0 или более поздняя версия |
| 1,14 и более поздние версии | Н/Д |

Если вы используете версию Kubernetes, которая не имеет исправления для этой проблемы, можно устранить проблему, выполнив следующие действия.

* Если диск ожидает отключения в течение длительного периода времени, попробуйте отключить диск вручную.

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Диск Azure, ожидающий отсоединения бессрочно

В некоторых случаях, если операция отсоединения диска Azure завершается сбоем при первой попытке, она не будет повторять операцию отсоединения и останется подключенной к виртуальной машине исходного узла. Эта ошибка может возникать при перемещении диска с одного узла на другой. Пример.

```console
[Warning] AttachVolume.Attach failed for volume “pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9” : Attach volume “kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance “/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0” failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code=“ConflictingUserInput” Message=“Disk ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9’ cannot be attached as the disk is already owned by VM ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1’.”
```

Эта проблема исправлена в следующих версиях Kubernetes:

| Версия Kubernetes | Фиксированная версия |
| -- | :--: |
| 1.11 | 1.11.9 или более поздняя версия |
| 1,12 | 1.12.7 или более поздняя версия |
| 1,13 | 1.13.4 или более поздняя версия |
| 1,14 и более поздние версии | Н/Д |

Если вы используете версию Kubernetes, которая не имеет исправления для этой проблемы, можно устранить проблему, выполнив отсоединение диска вручную.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Сбой отключения диска Azure при возникновении проблемы с потенциальным условием гонки и списком дисков недопустимых данных

При сбое отключения диска Azure будет выполнена повторная попытка до шести раз, чтобы отсоединить диск с помощью экспоненциального выключения. Он также будет удерживать блокировку на уровне узла в списке дисков данных примерно через 3 минуты. Если список дисков обновляется вручную в течение этого периода времени, например при выполнении операции присоединения или отсоединения вручную, то список дисков, удерживаемый блокировкой на уровне узла, будет устаревшим и привести к нестабильной работе виртуальной машины узла.

Эта проблема исправлена в следующих версиях Kubernetes:

| Версия Kubernetes | Фиксированная версия |
| -- | :--: |
| 1,12 | 1.12.9 или более поздняя версия |
| 1,13 | 1.13.6 или более поздняя версия |
| 1,14 | 1.14.2 или более поздняя версия |
| 1,15 и более поздние версии | Н/Д |

Если вы используете версию Kubernetes, которая не имеет исправления для этой проблемы, а виртуальная машина узла имеет устаревший список дисков, вы можете устранить проблему, отключая все несуществующие диски из виртуальной машины как единую, массовыую операцию. **По отдельности отсоединение несуществующих дисков может завершиться ошибкой.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Большое количество дисков Azure приводит к снижению скорости подключения или отсоединения.

Если число дисков Azure, подключенных к виртуальной машине узла, превышает 10, операции присоединения и отсоединения могут выполняться медленнее. Эта проблема является известной проблемой, и в настоящее время решения не предлагаются.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Сбой отключения диска Azure, приводящий к потенциальной виртуальной машине узла в состоянии сбоя

В некоторых случаях отключение диска Azure может частично завершиться сбоем и оставить виртуальную машину узла в состоянии сбоя.

Эта проблема исправлена в следующих версиях Kubernetes:

| Версия Kubernetes | Фиксированная версия |
| -- | :--: |
| 1,12 | 1.12.10 или более поздняя версия |
| 1,13 | 1.13.8 или более поздняя версия |
| 1,14 | 1.14.4 или более поздняя версия |
| 1,15 и более поздние версии | Н/Д |

Если вы используете версию Kubernetes, которая не имеет исправления для этой проблемы, и виртуальная машина узла находится в неисправном состоянии, можно устранить проблему, обновив состояние виртуальной машины вручную, используя один из следующих способов.

* Для кластера на основе группы доступности:
    ```console
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Для кластера на основе VMSS:
    ```console
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Устранение неполадок в службе файлов Azure и AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Каковы рекомендуемые стабильные версии Kubernetes для службы файлов Azure?
 
| Версия Kubernetes | Рекомендуемая версия |
| -- | :--: |
| 1,12 | 1.12.6 или более поздняя версия |
| 1,13 | 1.13.4 или более поздняя версия |
| 1,14 | 1.14.0 или более поздняя версия |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Какие версии Kubernetes имеют поддержку файлов Azure в облаке независимых?

| Версия Kubernetes | Рекомендуемая версия |
| -- | :--: |
| 1,12 | 1.12.0 или более поздняя версия |
| 1,13 | 1.13.0 или более поздняя версия |
| 1,14 | 1.14.0 или более поздняя версия |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Что такое Маунтоптионс по умолчанию при использовании службы "файлы Azure"?

Рекомендуемые параметры:

| Версия Kubernetes | значение fileMode и Дирмоде|
| -- | :--: |
| 1.12.0 — 1.12.1 | 0755 |
| 1.12.2 и более поздние версии | 0777 |

Если вы используете кластер с Куберетес версии 1.8.5 или более поздней и динамически создаете постоянный том с классом хранения, параметры подключения можно указать в объекте класса хранения. В следующем примере задается значение *0777*.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Некоторые дополнительные полезные параметры *маунтоптионс* :

* *мфсимлинкс* сделает службы подключения файлов Azure (CIFS) поддержкой символьных ссылок
* *нобрл* предотвратит отправку запросов на блокировку диапазона байтов на сервер. Этот параметр необходим для некоторых приложений, которые нарушают работу с блокировками диапазона байтов в стиле CIFS. Большинство серверов CIFS пока не поддерживают запросы на блокировку диапазона байтов рекомендаций. Если не использовать *нобрл*, приложения, которые нарушают работу с блокировками диапазона байтов в стиле CIFS, могут вызвать сообщения об ошибках следующего вида:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Ошибка "не удалось изменить разрешения" при использовании файлов Azure

При запуске PostgreSQL в подключаемом модуле службы файлов Azure может появиться сообщение об ошибке следующего вида:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Эта ошибка вызвана тем, что подключаемый модуль службы файлов Azure использует протокол CIFS/SMB. При использовании протокола CIFS/SMB разрешения на доступ к файлам и каталогам не могут быть изменены после подключения.

Чтобы устранить эту проблему, используйте *вложенный путь* вместе с подключаемым модулем диска Azure. 

> [!NOTE] 
> Для диска типа ext3/4 существует потерянный и найденный каталог после форматирования диска.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Высокая задержка в службе файлов Azure по сравнению с диском Azure при обработке большого количества небольших файлов

В некоторых случаях, например при обработке большого количества небольших файлов, при использовании службы файлов Azure по сравнению с диском Azure может возникнуть высокая задержка.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Ошибка при включении параметра "разрешить доступ из выбранной сети" в учетной записи хранения

Если включить *разрешение доступа из выбранной сети* в учетной записи хранения, которая используется для динамической подготовки в AKS, то при AKS создает общую папку возникает ошибка.

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Эта ошибка вызвана тем, что Kubernetes *персистентволуме-Controller* не находится в сети, выбранной при настройке *разрешения доступа из выбранной сети*.

Вы можете устранить эту ошибку, используя [статическую подготовку с помощью службы файлов Azure](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Не удается повторно подключить службу файлов Azure в модуле Windows

Если удаляется модуль Windows с подключением к файлам Azure и запланировано его повторное создание на том же узле, подключение завершится ошибкой. Это происходит из-за сбоя команды `New-SmbGlobalMapping`, так как подключение файлов Azure уже подключено к узлу.

Например, может появиться сообщение об ошибке следующего вида:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Эта проблема исправлена в следующих версиях Kubernetes:

| Версия Kubernetes | Фиксированная версия |
| -- | :--: |
| 1,12 | 1.12.6 или более поздняя версия |
| 1,13 | 1.13.4 или более поздняя версия |
| 1,14 и более поздние версии | Н/Д |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Сбой подключения файлов Azure из-за изменения ключа учетной записи хранения

Если ключ учетной записи хранения изменился, вы можете столкнуться со сбоями при подключении файлов Azure.

Чтобы устранить эту ошибку, вручную обновите поле *азуресторажеаккаунткэй* вручную в секрете файла Azure с помощью ключа учетной записи хранения в кодировке Base64.

Чтобы закодировать ключ учетной записи хранения в Base64, можно использовать `base64`. Пример.

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Чтобы обновить секретный файл Azure, используйте `kubectl edit secret`. Пример.

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Через несколько минут узел агента повторит попытку подключения файла Azure к обновленному ключу хранилища.
