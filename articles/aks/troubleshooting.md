---
title: Устранение распространенных проблем со Службой Azure Kubernetes
description: Узнайте, как устранить распространенные проблемы при использовании Службы Azure Kubernetes (AKS).
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 7bdabf2ec109fe96c28185bd1a2a680ce19c2650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368338"
---
# <a name="aks-troubleshooting"></a>Устранение неполадок с AKS

При создании кластеров Azure Kubernetes Service (AKS) или управлении ими иногда вы можете сталкиваться с проблемами. В этой статье описаны некоторые распространенные проблемы и действия по устранению неполадок.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Где найти общие сведения об отладке проблем Kubernetes?

[Вот официальное руководство по устранению неполадок с кластерами Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
[Это руководство по устранению неполадок](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) модулей pod, узлов, кластеров и т. д., опубликованное инженером Майкрософт.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Я получаю ошибку превышения квоты во время создания или обновления. Что делать? 

Вам необходимо [запросить ядра](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Каково максимальное число модулей pod на узел, установленное для AKS?

Если вы развертываете кластер AKS на портале Azure, максимальное число модулей pod на узел по умолчанию составляет 30.
Если вы развертываете кластер AKS в Azure CLI, максимальное число модулей pod на узел по умолчанию составляет 110. Убедитесь, что используется последняя версия Azure CLI. Этот параметр по умолчанию можно изменить с помощью флага `–-max-pods` в команде `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Я получаю ошибку insufficientSubnetSize при развертывании кластера AKS с использованием расширенного сетевого взаимодействия. Что делать?

Если используется Azure CNI (продвинутая сеть), AKS выделяет IP-адреса на основе "макс-стручков" на узел, настроенный. На основе настроенных максимальных стручков на узла, размер подсети должен быть больше, чем продукт количества узлов и максимальный стручок на узлы настройки. Следующее уравнение описывает следующее:

Размер подсети > количество узлов в кластере (с учетом будущих требований к масштабированию) - max стручки на набор узлов.

Дополнительные сведения см. в разделе [Планирование назначения IP-адресов для кластера](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Мой модуль pod завис в режиме CrashLoopBackOff. Что делать?

Модуль pod может зависнуть в этом режиме по различным причинам. Вы можете просмотреть:

* сведения о самом модуле pod с помощью команды `kubectl describe pod <pod-name>`;
* сведения в журналах с помощью команды `kubectl logs <pod-name>`.

Дополнительные сведения об устранении неполадок модуля pod см. в статье об [отладке приложений](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Я пытаюсь включить RBAC в существующем кластере. Как это сделать?

К сожалению, включение RBAC в существующих кластерах в настоящее время не поддерживается. Необходимо явно создать кластеры. Если вы используете CLI, RBAC включается по умолчанию. Если вы используете портал AKS, в рабочем процессе создания будет доступен переключатель для включения RBAC.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Кластер создан с поддержкой RBAC помощью Azure CLI со значениями по умолчанию или на портале Azure, при этом на панели мониторинга Kubernetes появились многочисленные предупреждения. Ранее на панели мониторинга не возникало никаких предупреждений. Что делать?

Причина отображения предупреждений на панели мониторинга заключается в том, что теперь в кластере включена функция RBAC, в которой доступ к этой панели отключен по умолчанию. В целом этот подход является рекомендуемым, так как раскрытие панели мониторинга по умолчанию для всех пользователей кластера может привести к угрозам безопасности. Если вы по-прежнему хотите включить панель мониторинга, следуйте указаниям в этом [блоге](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Мне не удается подключиться к панели мониторинга. Что делать?

Самый простой способ получить доступ к службе за пределами кластера — запустить `kubectl proxy`, который будет запрашивать ваш локальный порт 8001 для подключения к серверу API Kubernetes. После этого сервер API может установить прокси-подключение к вашей службе: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Если вы не видите панель мониторинга Kubernetes, проверьте, работает ли `kube-proxy` стручок в пространстве `kube-system` имен. Если модуль pod находится в нерабочем состоянии, удалите его, и он перезапустится.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Мне не удается получить журналы kubectl, или подключение к серверу API завершается сбоем. Я получаю "Ошибка с сервера: ошибка набора бэкэнда: набрать tcp ...". Что делать?

Убедитесь, что группа безопасности сети по умолчанию не изменена и что порт 22 и 9000 открыты для подключения к серверу API. Проверьте, `tunnelfront` работает ли стручок в пространстве `kubectl get pods --namespace kube-system` имен *kube-системы* с помощью команды. Если он не запущен, принудительно удалите его, и он перезапустится.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Я пытаюсь обновить или масштабировать, и я получаю "сообщение: Изменение свойства'imageReference' не допускается" ошибка. Как устранить эту проблему?

Возможно, вы получаете эту ошибку, потому что изменили теги в узлах агента внутри кластера AKS. Изменение и удаление тегов и других свойств ресурсов в группе ресурсов MC_* может привести к непредвиденным результатам. Изменение ресурсов в группе MC_* в кластере AKS нарушает цель уровня обслуживания (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Я получаю ошибки, что мой кластер находится в неработаем состоянии и модернизации или масштабирования не будет работать, пока он не будет исправлен, пока он не будет исправлен

*Эта помощь в устранении неполадок направляется изhttps://aka.ms/aks-cluster-failed*

Эта ошибка возникает, когда кластеры входят в неисправное состояние по нескольким причинам. Следуйте ниже, чтобы разрешить неудавшую состояние кластера, прежде чем повторить ранее сбойную операцию:

1. До тех пор, `failed` пока `upgrade` `scale` кластер не выйдет из состояния, и операции не увенчаются успехом. Общие коренные проблемы и резолюции включают в себя:
    * Масштабирование с **недостаточной вычислительной (CRP) квотой.** Чтобы решить, сначала масштабируйте кластер обратно в состояние стабильной цели в пределах квоты. Затем выполните следующие [действия, чтобы запросить увеличение квоты вычислений,](../azure-portal/supportability/resource-manager-core-quotas-request.md) прежде чем пытаться снова увеличить масштаб за пределы первоначальных квот.
    * Масштабирование кластера с помощью передовых сетей и **недостаточного количества ресурсов подсети (сетевых) ресурсов.** Чтобы решить, сначала масштабируйте кластер обратно в состояние стабильной цели в пределах квоты. Затем выполните [следующие шаги, чтобы запросить увеличение квоты ресурсов,](../azure-resource-manager/templates/error-resource-quota.md#solution) прежде чем пытаться снова расширить масштаб за пределы первоначальных квот.
2. После устранения основной причины сбоя обновления кластер должен находиться в успешном состоянии. После проверки успешного состояния повторите исходную операцию.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Я получаю ошибки при попытке обновить или масштабировать, что состояние моего кластера в настоящее время обновляется или не удалось обновить

*Эта помощь в устранении неполадок направляется изhttps://aka.ms/aks-pending-upgrade*

Обновление и масштабирование операций в кластере с единым пулом узлов или кластером с [несколькими пулами узлов](use-multiple-node-pools.md) являются взаимоисключающими. Нельзя одновременно обновить и масштабировать кластер или пул узлов. Вместо этого каждый тип операции должен быть завершен на целевом ресурсе до следующего запроса на том же ресурсе. В результате операции ограничены при проведении или попытке активного обновления или операции масштаба, а затем и о сбое. 

Чтобы помочь диагностировать запуск `az aks show -g myResourceGroup -n myAKSCluster -o table` проблемы для получения подробного состояния кластера. На основе результата:

* Если кластер активно обновляется, подождите, пока операция не прекратится. Если это удалось, повторите повторную попытку ранее неудачной операции снова.
* Если кластер не прошел обновление, выполните последующие шаги, изложенные в предыдущем разделе.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Могу ли я переместить свой кластер в другую подписку или подписку с помощью кластера в новый арендатор?

Если вы перевели кластер AKS в другую подписку или кластер, владеющий подпиской на нового клиента, кластер потеряет функциональность из-за потери назначений ролей и прав директоров служб. **AKS не поддерживает перемещение кластеров между подписками или арендаторами** из-за этого ограничения.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Я получаю ошибки, пытаясь использовать функции, которые требуют виртуальных наборов масштаба машины

*Эта помощь в устранении неполадок направляется от aka.ms/aks-vmss-enablement*

Вы можете получать ошибки, указывающие на то, что кластер AKS не находится в наборе виртуальной шкалы машин, например, в следующем примере:

**AgentPool 'agentpool' установил автоматическое масштабирование как включено, но не на виртуальных наборов шкалы машин**

Для использования таких функций, как автоскейлер кластера или несколько пулов узлов, необходимо создать кластеры AKS, которые используют виртуальные наборы масштабов машин. Ошибки возвращаются, если вы пытаетесь использовать функции, которые зависят от виртуальных наборов масштаба машины, и вы ориентируетесь на регулярный, невиртуальный набор шкалы АКС кластера.

Следуйте за шагами *перед началом* в соответствующем документе, чтобы правильно создать кластер AKS:

* [Используйте кластерный автоскейлер](cluster-autoscaler.md)
* [Создание и использование нескольких пулов узлов](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Какие ограничения именования применяются для ресурсов и параметров AKS?

*Эта помощь в устранении неполадок направляется от aka.ms/aks-naming-rules*

Ограничения именования реализуются как платформой Azure, так и AKS. Если имя ресурса или параметр нарушают одно из этих ограничений, возвращается ошибка, которая запрашивает у вас другой вход. Применяются следующие общие руководящие принципы именования:

* Названия кластеров должны быть 1-63 символами. Только разрешенные символы буквы, цифры, тире, и подчеркивает. Первым и последним символом должно быть письмо или номер.
* Название группы ресурсов AKS *MC_* объединяет имя группы ресурсов и имя ресурса. Автоматически генерируемый `MC_resourceGroupName_resourceName_AzureRegion` синтаксис должен быть не более 80 chars. При необходимости уменьшите длину имени группы ресурсов или имя кластера AKS.
* *DnsPrefix* должен начинаться и заканчиваться с буквенно-цифровыми значениями и должен быть между 1-54 символами. Действительные символы включают алфавитные значения и дефисы (-). *DnsPrefix* не может включать специальные символы, такие как период (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Я получаю ошибки при попытке создания, обновления, масштабирования, удаления или обновления кластера, что операция не допускается, как другая операция в процессе.

*Эта помощь в устранении неполадок направляется от aka.ms/aks-pending-operation*

Операции кластера ограничены, когда предыдущая операция все еще выполняется. Чтобы получить подробное состояние кластера, `az aks show -g myResourceGroup -n myAKSCluster -o table` используйте команду. При необходимости используйте собственную группу ресурсов и название кластера AKS.

В зависимости от вывода статуса кластера:

* Если кластер находится в любом состоянии подготовки, кроме *успешного* или *неудачного,* подождите, пока операция *(Обновление / Обновление / Создание / Масштабирование / Уничтожение / Миграция*) прекращается. Когда предыдущая операция завершена, повторите последнюю операцию кластера.

* Если кластер имеет неудачное обновление, выполните изложенные шаги, которые [я получаю ошибки, которые мой кластер находится в неработаем состоянии, и обновление или масштабирование не будет работать до тех пор, пока он не будет исправлен.](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Я получаю ошибки, которые мой директор службы не был найден, когда я пытаюсь создать новый кластер, не проходя в существующем.

При создании кластера AKS требуется директор службы для создания ресурсов от вашего имени. AKS предлагает возможность создания нового во время создания кластера, но для этого требуется активный каталог Azure, чтобы полностью распространить новый основной сервис в разумные сроки, чтобы кластер преуспел в создании. Когда это распространение занимает слишком много времени, кластер не сможет получить подтверждение для создания, так как он не может найти для этого принцип доступных услуг. 

Используйте следующие обходные пути для этого:
1. Используйте существующий принцип обслуживания, который уже распространяется в разных регионах и существует для передачи в AKS во время создания кластера.
2. При использовании скриптов автоматизации добавляйте временные задержки между созданием основного сервиса и созданием кластера AKS.
3. При использовании портала Azure вернитесь к настройкам кластера во время создания и повторной попытки страницы проверки через несколько минут.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Я получаю ошибки после ограничения моего трафика на выезд

При ограничении трафика из кластера AKS имеются [обязательные и необязательные рекомендуемые](limit-egress-traffic.md) исходящие порты/правила сети и правила применения для AKS. Если настройки противоречат любому из этих правил, вы не `kubectl` сможете запускать определенные команды. Вы также можете увидеть ошибки при создании кластера AKS.

Убедитесь, что ваши настройки не противоречат ни с каким из требуемых или необязательных рекомендуемых исходящих портов/ сетевых правил и правил F-DN/приложения.

## <a name="azure-storage-and-aks-troubleshooting"></a>Лазурное хранение и устранение проблем AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Каковы рекомендуемые стабильные версии Kubernetes для диска Azure? 

| Версия Кубернете | Рекомендуемая версия |
| -- | :--: |
| 1.12 | 1.12.9 или позже |
| 1.13 | 1.13.6 или позже |
| 1,14 | 1.14.2 или позже |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Какие версии Kubernetes имеют поддержку Azure Disk в Суверенном Облаке?

| Версия Кубернете | Рекомендуемая версия |
| -- | :--: |
| 1.12 | 1.12.0 или позже |
| 1.13 | 1.13.0 или позже |
| 1,14 | 1.14.0 или позже |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach не удалось для Azure Disk: разбор "/dev/disk/azure/scsi1/lun1": недействительный синтаксис

В версии Kubernetes 1.10 MountVolume.WaitForAttach может выйти из строя при ререйзе Azure Disk.

На Linux вы можете увидеть неправильную ошибку формата DevicePath. Пример:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

В Windows может возникнуть ошибка номера DevicePath (LUN). Пример:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Эта проблема была исправлена в следующих версиях Kubernetes:

| Версия Кубернете | Фиксированная версия |
| -- | :--: |
| 1,10 | 1.10.2 или позже |
| 1.11 | 1.11.0 или позже |
| 1.12 и позже | Недоступно |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Сбой при настройке uid и gid в маунтОпции для Azure Disk

Azure Disk использует файловую систему ext4,xfs по умолчанию и монтироватьОпции, такие как uid'x, не могут быть установлены во время монтажа. Например, если вы попытались установить mountOptions uid-999,gid-999, то увидели бы ошибку как:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Вы можете смягчить проблему, сделав одну из следующих проблем:

* [Настроили контекст безопасности для стручка,](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) установив uid в runAsUser и gid в fsGroup. Например, при следующем параметре стручок будет запущен в качестве корня, что сделает его доступным для любого файла:

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
  > Так как gid и uid монтируются как корень или 0 по умолчанию. Если gid или uid установлены как некорневые, например 1000, Kubernetes будет использовать `chown` для изменения всех каталогов и файлов под этим диском. Эта операция может занять много времени и может сделать монтаж диска очень медленным.

* Используйте `chown` initContainers для установки головокружения и uid. Пример:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Ошибка при удалянии Azure Disk PersistentVolumeClaim, используемая капсулой

Если вы попытаетесь удалить Azure Disk PersistentVolumeClaim, который используется капсулой, вы можете увидеть ошибку. Пример:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

В версии Kubernetes 1.10 и позже есть функция защиты PersistentVolumeClaim, включенная по умолчанию для предотвращения этой ошибки. Если вы используете версию Kubernetes, которая не имеет исправления этой проблемы, вы можете смягчить эту проблему, удалив стручок с помощью PersistentVolumeClaim перед удалянием PersistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Ошибка "Не может найти Лун для диска" при подключении диска к узлу

При подключении диска к уему можно увидеть следующую ошибку:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Эта проблема была исправлена в следующих версиях Kubernetes:

| Версия Кубернете | Фиксированная версия |
| -- | :--: |
| 1,10 | 1.10.10 или позже |
| 1.11 | 1.11.5 или позже |
| 1.12 | 1.12.3 или позже |
| 1.13 | 1.13.0 или позже |
| 1.14 и позже | Недоступно |

Если вы используете версию Kubernetes, которая не имеет решения этой проблемы, вы можете смягчить проблему, подянее несколько минут и повторного пробы.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Сбой присоединения/отсоединить azure Disk, проблемы с креплением или ошибками ввоза/овоза во время нескольких операций присоединения/отъема

Начиная с версии Kubernetes 1.9.2, при параллельном выполнении нескольких операций присоединения/отвода можно увидеть следующие проблемы с диском из-за грязного кэша VM:

* Сбои дискового атташе/отсоединить
* Ошибки ввилики диска/O
* Неожиданное отслоение диска от VM
* VM работает в несуществующем состоянии из-за присоединения несуществующего диска

Эта проблема была исправлена в следующих версиях Kubernetes:

| Версия Кубернете | Фиксированная версия |
| -- | :--: |
| 1,10 | 1.10.12 или позже |
| 1.11 | 1.11.6 или позже |
| 1.12 | 1.12.4 или позже |
| 1.13 | 1.13.0 или позже |
| 1.14 и позже | Недоступно |

Если вы используете версию Kubernetes, которая не имеет исправления этой проблемы, вы можете смягчить проблему, попробовав ниже:

* Если диск ждет, чтобы отделить в течение длительного периода времени, попробуйте отсоединить диск вручную

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Azure Disk ждет, чтобы отсоединиться на неопределенный срок

В некоторых случаях, если операция отсоединить Azure Disk не справляется с первой попытки, она не будет повторно пытать операцию отсоединить и останется прикрепленной к исходному узлу VM. Эта ошибка может возникнуть при перемещении диска из одного узла в другой. Пример:

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

Эта проблема была исправлена в следующих версиях Kubernetes:

| Версия Кубернете | Фиксированная версия |
| -- | :--: |
| 1.11 | 1.11.9 или позже |
| 1.12 | 1.12.7 или позже |
| 1.13 | 1.13.4 или позже |
| 1.14 и позже | Недоступно |

Если вы используете версию Kubernetes, которая не имеет решения этой проблемы, вы можете смягчить проблему, вручную отсоединив диск.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Сбой отсоединить Azure Disk, что привело к потенциальной проблеме состояния расы и недействительному списку дисков данных

Когда Azure Disk не отсоединится, он будет повторно пытаться до шести раз, чтобы отделить диск, используя экспоненциальный обратно. Он также будет держать блокировку уровня узла в списке дисков данных в течение примерно 3 минут. Если список дисков обновляется вручную в течение этого периода времени, например, ручной прикрепить или отсоединить операцию, это приведет к тому, что список дисков, удерживаемый блокировкой уровня узла, устареет и вызовет нестабильность на узле VM.

Эта проблема была исправлена в следующих версиях Kubernetes:

| Версия Кубернете | Фиксированная версия |
| -- | :--: |
| 1.12 | 1.12.9 или позже |
| 1.13 | 1.13.6 или позже |
| 1,14 | 1.14.2 или позже |
| 1.15 и позже | Недоступно |

Если вы используете версию Kubernetes, которая не имеет исправления для этой проблемы и ваш узел VM имеет устаревший список дисков, вы можете смягчить проблему, отделяя все несуществующие диски от VM как одной, объемной операции. **Индивидуальное отсоединение несуществующих дисков может вывести из строя.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Большое количество дисков Azure вызывает медленный прикрепляйтесь/отсоединяется

Когда количество дисков Azure, прикрепленных к узлу VM, превышает 10, прикрепляйте и отсоединяйте операции могут быть медленными. Эта проблема является известной проблемой, и в настоящее время нет обходных пути.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Сбой Azure Disk, приводящий к потенциальному узлу VM в несостоявшемся состоянии

В некоторых случаях отсоединиться от Azure Disk может частично выйти из строя и оставить узел VM в неисправном состоянии.

Эта проблема была исправлена в следующих версиях Kubernetes:

| Версия Кубернете | Фиксированная версия |
| -- | :--: |
| 1.12 | 1.12.10 или позже |
| 1.13 | 1.13.8 или позже |
| 1,14 | 1.14.4 или позже |
| 1.15 и позже | Недоступно |

Если вы используете версию Kubernetes, которая не имеет исправления для этой проблемы, и ваш узел VM находится в неверном состоянии, вы можете смягчить проблему, вручную обновив статус VM, используя один из приведенных ниже:

* Для кластера, основанного на наборе доступности:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Для кластера на основе VMSS:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Лазурные файлы и устранение проблем AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Каковы рекомендуемые стабильные версии Kubernetes для файлов Azure?
 
| Версия Кубернете | Рекомендуемая версия |
| -- | :--: |
| 1.12 | 1.12.6 или позже |
| 1.13 | 1.13.4 или позже |
| 1,14 | 1.14.0 или позже |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Какие версии Kubernetes поддерживают Azure Files в Суверенном Облаке?

| Версия Кубернете | Рекомендуемая версия |
| -- | :--: |
| 1.12 | 1.12.0 или позже |
| 1.13 | 1.13.0 или позже |
| 1,14 | 1.14.0 или позже |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Что такое параметры крепления по умолчанию при использовании файлов Azure?

Рекомендуемые настройки:

| Версия Кубернете | значение fileMode и dirMode|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 и позже | 0777 |

При использовании кластера с версией Kubernetes 1.8.5 или больше и динамически мнимой создании постоянного объема с классом хранения параметры крепления могут быть указаны на объекте класса хранения. В следующем примере задается значение *0777*.

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

Некоторые дополнительные полезные настройки *монтажаOptions:*

* *mfsymlinks* сделает Azure Файлы крепления (cifs) поддержку символических ссылок
* *nobrl* предотвратит отправку запросов на блокировку диапазона байт на сервер. Эта настройка необходима для некоторых приложений, которые порвать с cifs стиль обязательных байт диапазон замков. Большинство серверов cifs пока не поддерживают запрос ыконсультативных блокировок диапазона байт. Если не использовать *nobrl*, приложения, которые порвать с cifs стиль обязательных байт диапазон замков может привести к ошибкам сообщения похожи на:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Ошибка "не может изменить разрешения" при использовании файлов Azure

При запуске плагина PostgreS'L на плагине Azure Files может возникнуть ошибка, аналогичная:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Эта ошибка вызвана плагином Azure Files с использованием протокола cifs/SMB. При использовании протокола cifs/SMB разрешения файла и каталога не могут быть изменены после монтажа.

Чтобы решить эту проблему, используйте *subPath* вместе с плагином Azure Disk. 

> [!NOTE] 
> Для типа диска ext3/4 после отформатированного диска — потерянный каталог.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Файлы Azure имеет высокую задержку по сравнению с Azure Disk при обработке многих небольших файлов

В некоторых случаях, например, при обработке большого количества небольших файлов при использовании файлов Azure могут возникнуть высокая задержка по сравнению с Azure Disk.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Ошибка при включении "Разрешить доступ разрешить доступ из выбранной сети" настройки на учетной записи хранения

Если вы включите *доступ из выбранной сети* на учетную запись хранения, которая используется для динамического подготовки в AKS, вы получите ошибку, когда AKS создает общую копию файла:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Эта ошибка из-за Kubernetes *стойкий контроллер* не в сети, выбранной при настройке *позволяют получить доступ из выбранной сети.*

Можно смягчить проблему, используя [статическое обеспечение с помощью файлов Azure.](azure-files-volume.md)

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Файлы Azure не могут восстановиться в моде Windows

Если стручок Windows с креплением Azure Files удаляется, а затем планируется воссоздать на том же узеле, то крепление выйдет из строя. Этот сбой происходит `New-SmbGlobalMapping` из-за сбоя команды, так как крепление файлов Azure уже установлено на узел.

Например, вы можете увидеть ошибку, похожую на:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Эта проблема была исправлена в следующих версиях Kubernetes:

| Версия Кубернете | Фиксированная версия |
| -- | :--: |
| 1.12 | 1.12.6 или позже |
| 1.13 | 1.13.4 или позже |
| 1.14 и позже | Недоступно |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Монтировка файлов Azure выходит из строя из-за изменения ключа учетной записи хранилища

Если ключ учетной записи хранилища изменился, можно увидеть сбои в работе Azure Files.

Вы можете смягчить проблему, вручную обновляя поле *azurestorageaccountkey* вручную в тайне файла Azure с вашим базовым ключом учетной записи хранилища.

Для кодирования ключа учетной записи хранилища `base64`в base64 можно использовать. Пример:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Для обновления секретного файла `kubectl edit secret`Azure используйте: Пример:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Через несколько минут узел агента повторно пропытается крепление лазурного файла с обновленным ключом хранения.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Кластерный автосккластер не может масштабироваться с ошибкой не удалось исправить размеры группы узлов

Если автоскалатор кластера не масштабируется вверх/вниз, и вы видите ошибку, как ниже, на [журналах кластера autoscaler.][view-master-logs]

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Эта ошибка обусловлена состоянием гонки автоскалатора кластера, при котором кластерный автоскейлк заканчивается с другим значением, чем тот, который находится на самом деле в кластере. Чтобы выйти из этого состояния, просто отключить и повторно включить [кластера autoscaler][cluster-autoscaler].

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Медленное вложение диска, GetAzureDiskLun занимает от 10 до 15 минут, и вы получите ошибку

На версиях Kubernetes **старше 1.15.0** вы можете получить ошибку, такую как **Ошибка WaitForAttach Не может найти Lun для диска.**  Решение для этого заключается в том, чтобы ждать около 15 минут и повторить попытку.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
