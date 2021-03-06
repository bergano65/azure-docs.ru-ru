---
title: Настройка проверки актуальности в экземпляре контейнера
description: Подробные сведения о настройке проб активности для перезагрузки неработоспособных контейнеров в службе "Экземпляры контейнеров Azure"
ms.topic: article
ms.date: 06/08/2018
ms.openlocfilehash: 96d98d18a3f0ac666fb2c057216f7844b176d177
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481681"
---
# <a name="configure-liveness-probes"></a>Настройка проб активности

Контейнерные приложения могут выполняться в течение продолжительного периода времени, что приводит к повреждению состояний, которые, возможно, потребуется восстановить путем перезапуска контейнера. Служба "экземпляры контейнеров Azure" поддерживает проверки актуальности, чтобы можно было настроить контейнеры в группе контейнеров для перезапуска, если критические функции не работают. Проверка актуальности ведет себя так же, как проверка [Kubernetesности в режиме реального времени](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

В этой статье описывается развертывание группы контейнеров, содержащей пробу активности. В рамках этого примера демонстрируется автоматический перезапуск имитированного неработоспособного контейнера.

Служба "экземпляры контейнеров Azure" также поддерживает [проверки готовности](container-instances-readiness-probe.md), которые можно настроить так, чтобы трафик достиг контейнера только в том случае, если он готов к работе.

## <a name="yaml-deployment"></a>Развертывание файла YAML

Создайте файл `liveness-probe.yaml` со следующим фрагментом кода. Этот файл определяет группу контейнера, содержащую контейнер NGNIX, который в конечном итоге становится неработоспособным.

```yaml
apiVersion: 2018-10-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Выполните следующую команду, чтобы развернуть эту группу контейнеров с представленной выше конфигурацией YAML:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Команда "Запуск"

Развертывание определяет запускаемую команду, которая запускается при первом запуске контейнера, определяемого свойством `command`, которое принимает массив строк. В этом примере передается следующая команда, которая запускает сеанс Bash и создает файл с именем `healthy` в каталоге `/tmp`:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Затем он будет находиться в спящем режиме на 30 секунд перед удалением файла, а затем войдет в 10-минутный спящий режим.

### <a name="liveness-command"></a>Команда активности

Это развертывание определяет `livenessProbe`, который поддерживает команду `exec` Live, которая действует как проверка на актуальность. Если в результате выполнения этой команды возвращается ненулевое значение, работа контейнера будет завершена и перезапущена, после чего отобразится сообщение о том, что не удалось найти файл `healthy`. Если эта команда завершается успешно с кодом выхода 0, никакие действия выполняться не будут.

Свойство `periodSeconds` определяет команду активности, которая должна выполняться каждые 5 секунд.

## <a name="verify-liveness-output"></a>Проверка выходных данных активности

В течение первых 30 секунд завершается работа файла `healthy`, созданного с помощью команды запуска. Если команда активности проверяет наличие файла `healthy`, код состояния возвращает нулевое значение, что свидетельствует об успешном выполнении. Таким образом, перезагрузка не нужна.

Через 30 секунд работа `cat /tmp/healthy` завершится сбоем, что приведет к неработоспособному состоянию и завершению возникающих событий.

Эти события можно просмотреть на портале Azure или в Azure CLI.

![Неработоспособные события на портале][portal-unhealthy]

Просматривая события на портале Azure, вы увидите, что события типа `Unhealthy` будут запускаться после сбоя работы команды активности. Далее будет выполняться событие типа `Killing`, сообщающее об удалении контейнера и необходимости перезагрузки системы. Счетчик перезапусков для контейнера увеличивается каждый раз, когда происходит это событие.

Перезагрузка выполняется "на месте", а ресурсы (например, общедоступные IP-адреса и содержимое конкретного узла) будут сохранены.

![Счетчик перезагрузки на портале][portal-restart]

Если проба активности постоянно завершается сбоем и активирует слишком много перезагрузок, контейнер вводит экспоненциальную задержку.

## <a name="liveness-probes-and-restart-policies"></a>Пробы активности и политики перезагрузки

Политики перезагрузки заменяют поведение перезагрузки, активируемое пробами активности. Например, если задать `restartPolicy = Never` *и* проверку на динамическую проверку, Группа контейнеров не будет перезапущена из-за неудачной проверки на активное время. Вместо этого группа контейнеров будет соответствовать политике перезагрузки группы контейнеров `Never`.

## <a name="next-steps"></a>Дополнительная информация

Для выполнения сценариев на основе задачи может понадобиться, чтобы пробы активности включали функцию автоматической перезагрузки в случае, если необходимая функция не работает должным образом. Дополнительные сведения о запуске контейнеров на основе задач см. статье [Выполнение задачи-контейнера в службе "Экземпляры контейнеров Azure"](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
