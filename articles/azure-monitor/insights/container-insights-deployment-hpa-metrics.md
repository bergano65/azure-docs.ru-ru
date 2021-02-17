---
title: Развертывание & метрики HPA с Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье описывается, какие метрики развертывания & HPA (горизонтальный Автомасштабирование Pod) собираются с Azure Monitor для контейнеров.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: ee1f0d4849a8382a898aaca84956ff78166e138f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561585"
---
# <a name="deployment--hpa-metrics-with-azure-monitor-for-containers"></a>Развертывание & метрики HPA с Azure Monitor для контейнеров

Начиная с агента версии *ciprod08072020*, Azure Monitor для контейнеров — интегрированный агент теперь собирает метрики для развертываний & hPa.

## <a name="deployment-metrics"></a>Метрики развертывания

Azure Monitor для контейнеров автоматически запускает мониторинг развертываний, собирая следующие метрики через 60 с и сохраняя их в таблице **инсигхтметрикс** :

|Имя метрики |Измерение метрики (Теги) |Описание |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, Deployment, Деплойментстратеги, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated (Status. Упдатедрепликас) | Общее число готовых модулей Pod, предназначенных для данного развертывания (Status. Реадирепликас). Ниже приведены измерения этой метрики. <ul> <li> Deployment — имя развертывания </li> <li> пространство имен k8sNamespace-Kubernetes для развертывания </li> <li> Деплойментстратеги — стратегия развертывания, используемая для замены модулей Pod новыми (Spec. стратегий. Type)</li><li> creationTime — отметка времени создания развертывания </li> <li> spec_replicas-число требуемых модулей Pod (спец. реплики) </li> <li>status_replicas_available — общее количество доступных модулей Pod (готовых как минимум для Минреадисекондс), для которых предназначено это развертывание (Status. Аваилаблерепликас).</li><li>status_replicas_updated — общее число незавершенных модулей Pod, для которых предназначено это развертывание, с требуемой спецификацией шаблона (Status. Упдатедрепликас). </li></ul>|

## <a name="hpa-metrics"></a>Метрики HPA

Azure Monitor для контейнеров автоматически запускает мониторинг hPa, собирая следующие метрики через 60 с и сохраняя их в таблице **инсигхтметрикс** :

|Имя метрики |Измерение метрики (Теги) |Описание |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hPa, k8sNamespace, Ластскалетиме, spec_max_replicas, spec_min_replicas, status_desired_replicas, Таржеткинд, targetName | Текущее число реплик модулей Pod, управляемых этим автомасштабированием (Status. Куррентрепликас). Ниже приведены измерения этой метрики. <ul> <li> hPa — имя HPA </li> <li> пространство имен k8sNamespace-Kubernetes для HPA </li> <li> Ластскалетиме — Последнее время, в течение которого HPA масштабирует число модулей Pod (Status. Ластскалетиме)</li><li> Метка времени создания creationTime-HPA </li> <li> spec_max_replicas-верхний предел числа модулей Pod, которые могут быть заданы автомасштабированием (Spec. Максрепликас) </li> <li> spec_min_replicas нижнего предела для количества реплик, к которым может масштабироваться Автомасштабирование (Spec. Минрепликас) </li><li>status_desired_replicas необходимое количество реплик модулей управления доступом, управляемых этим автомасштабированием (Status. Десиредрепликас)</li><li>Таржеткинд — тип целевого объекта HPA (Spec. Скалетаржетреф. Kind) </li><li>targetName — имя целевого объекта HPA (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>HPAные диаграммы развертывания & 

Azure Monitor для контейнеров содержит предварительно настроенные диаграммы для метрик, перечисленных ранее в таблице в качестве книги для каждого кластера. Развертывания можно найти & развертываний книг HPA **& hPa** непосредственно из кластера AKS, выбрав пункт **книги** в левой области, а затем из раскрывающегося списка **Просмотр книг** в аналитической информации.

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать больше о метриках KUBE состояния, ознакомьтесь с [метриками KUBE-State в Kubernetes](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) .