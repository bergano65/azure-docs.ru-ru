---
title: Как войти в журналы запросов от Azure Monitor для VMs
description: Решение Azure Monitor для VMs собирает метрические данные и данные журналов, и эта статья описывает записи и включает в себя примерные запросы.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 61a71539dc034a216689eafd8991df60db96d2a4
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396923"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Как запрашивать журналы из Azure Monitor для виртуальных машин

Azure Monitor для VMs собирает показатели производительности и соединения, данные о инвентаризации компьютеров и процессов, а также сведения о состоянии работоспособности и перенаправляет ее в рабочее пространство журнала Analytics в Azure Monitor.  Эти данные доступны для [запроса](../../azure-monitor/log-query/log-query-overview.md) в Azure Monitor. Эти данные используются в различных сценариях, таких как планирование миграции, анализ емкости, обнаружение и устранение проблем с производительностью по требованию.

## <a name="map-records"></a>Сопоставление записей

Для каждого уникального компьютера и процесса создается одна запись в час. Кроме того, записи создаются во время запуска компьютера или процесса, а также при подключении к функции "Схема" в Azure Monitor для виртуальных машин. В таблице ниже приведены свойства этих записей. Поля и значения в событиях ServiceMapComputer_CL сопоставляются с полями ресурса Machine (Компьютер) в API ServiceMap Azure Resource Manager. Поля и значения в событиях ServiceMapProcess_CL сопоставляются с полями ресурса Process (Процесс) в API ServiceMap Azure Resource Manager. Поле ResourceName_s совпадает с полем имени в соответствующем ресурсе Resource Manager. 

На основе созданных в системе свойств можно определить уникальные компьютеры или процессы:

- Для уникальной идентификации компьютера в рабочей области Log Analytics используйте свойство *ResourceId* или *ResourceName_s*.
- Для уникальной идентификации процесса в рабочей области Log Analytics используйте свойство *ResourceId*. Свойство *ResourceName_s* уникально в контексте компьютера, на котором выполняется процесс (MachineResourceName_s). 

Так как для указанных процесса и компьютера в заданном диапазоне времени могут существовать несколько записей, то запросы могут возвращать несколько записей для одного и того же компьютера или процесса. Чтобы включить только самую `| summarize arg_max(TimeGenerated, *) by ResourceId` самую самую полотную запись, добавьте в запрос.

### <a name="connections-and-ports"></a>Соединения и порты

Функция «Метрика соединения» вводит две новые таблицы в журналах Azure Monitor - VMConnection и VMBoundPort. В этих таблицах предоставляется информация о соединениях для машины (входящих и исходящих), а также о открытых/активных на них портах серверов. ConnectionMetrics также подвергаются через AI, которые обеспечивают средства для получения конкретной метрики в течение временного окна. Соединения TCP, возникающие в результате *принятия* на прослушиваемом *connecting* гнезде, входят в переплет, в то время как соединения с данным IP и портом исходят. Направление подключения указано в свойстве Direction (Направление), которое может иметь значение **inbound** (Входящее) или **outbound** (Исходящее). 

Записи в этих таблицах генерируются на данных, сообщаемых агентом зависимости. Каждая запись представляет собой наблюдение с интервалом в 1 минуту. Свойство TimeGenerated указывает начало интервала времени. Каждая запись содержит сведения для определения соответствующего объекта, то есть подключения или порта, а также метрики, связанные с этим объектом. На данный момент система собирает данные, касающиеся только действий в сети, связанных с протоколом TCP/IPv4. 

#### <a name="common-fields-and-conventions"></a>Общие поля и конвенции 

Следующие поля и конвенции применяются как к VMConnection, так и к VMBoundPort: 

- Компьютер: Полностью квалифицированное доменное имя машины отчетности 
- AgentId: Уникальный идентификатор для машины с агентом Log Analytics  
- Машина: Имя ресурса Azure Resource Manager для машины, выставленной ServiceMap. Это форма *m-GUID*, где *GUID* является такой же GUID, как AgentId  
- Процесс: Название ресурса Azure Manager для процесса, выставленного ServiceMap. Это форма *p-'hex строки.* Процесс уникален в рамках машины и для создания уникального идентификатора процесса на разных машинах, объединения полей машины и процесса. 
- ProcessName: Исполняемое название процесса отчетности.
- Все IP-адреса находятся в каноническом формате IPv4, например *13.107.3.160* 

Чтобы можно было управлять затратами и сложностью, записи о подключениях не представляют отдельные физические сетевые подключения. Система группирует несколько физических сетевых подключений в логическое подключение, которое затем отображается в соответствующей таблице.  Это означает, что запись в таблице *VMConnection* представляет логическую группу, а не отдельные отслеживаемые физические подключения. Физические сетевые подключения, для которых используется одно и то же общее значение для указанных ниже атрибутов в течение заданного одноминутного интервала, агрегированы в одну логическую запись в таблице *VMConnection*. 

| Свойство | Описание |
|:--|:--|
|Направление |Направление подключения. Возможные значения: *inbound* (Входящее) или *outbound* (Исходящее). |
|Компьютер |Полное доменное имя компьютера |
|Процесс |Удостоверение процесса или группы процессов, создающей или принимающей подключение. |
|SourceIp |IP-адрес источника. |
|DestinationIp |IP-адрес назначения. |
|DestinationPort |Номер порта назначения. |
|Протокол |Протокол, используемый для подключения.  Значение: *tcp*. |

Чтобы учесть влияние группирования, в указанных ниже свойствах записи приведены сведения о количестве сгруппированных физических подключений.

| Свойство | Описание |
|:--|:--|
|LinksEstablished |Количество физических сетевых подключений, созданных в течение отчетного периода. |
|LinksTerminated |Количество физических сетевых подключений, разорванных в течение отчетного периода. |
|LinksFailed |Количество физических сетевых подключений, которые не удалось создать во время отчетного периода. На данный момент эта информация доступна только для исходящих подключений. |
|LinksLive |Количество открытых физических сетевых подключений на конец отчетного периода.|

#### <a name="metrics"></a>Метрики

В дополнение к метрикам количества подключений в указанных ниже свойствах записи содержатся сведения об объемах отправленных и принятых данных для заданного логического подключения или порта.

| Свойство | Описание |
|:--|:--|
|BytesSent |Общее количество байтов, отправленных в течение отчетного периода. |
|BytesReceived |Общее количество байтов, принятых в течение отчетного периода. |
|Ответы |Количество откликов, наблюдаемых в течение отчетного периода. 
|ResponseTimeMax |Наибольшее время отклика (в миллисекундах), наблюдаемое во время отчетного периода. Если значение не указано, свойство будет пустым.|
|ResponseTimeMin |Наименьшее время отклика (в миллисекундах), наблюдаемое во время отчетного периода. Если значение не указано, свойство будет пустым.|
|ResponseTimeSum |Сумма всех значений времени отклика (в миллисекундах), наблюдаемых во время отчетного периода. Если значение не указано, свойство будет пустым.|

Третий тип собираемых данных — время отклика. Это время, в течение которого вызывающая сторона ожидает обработки запроса, отправленного через подключение, и отклика на него от удаленной конечной точки. Полученное время отклика — это оценочное значение истинного времени отклика базового протокола приложений. Для его вычисления используется эвристика на основе наблюдения за потоком данных между источником и точкой назначения физического сетевого подключения. По сути, это разница между временем, когда последний байт запроса покидает отправителя, и временем, когда последний байт отклика возвращается к нему. Эти две метки времени используются для регистрации событий запроса и отклика для заданного физического подключения. Разность этих значений представляет собой время отклика для одного запроса. 

В первом выпуске данной функции мы используем алгоритм аппроксимации, который может работать с разными степенями успешности, которые зависят от того, какой протокол фактически используется для заданного сетевого подключения. Например, текущий подход хорошо работает для протоколов, основанных на запросах и откликах, например протокола HTTP(S), но не работает с однонаправленными или основанными на очереди сообщений протоколами.

Необходимо учитывать указанные ниже важные моменты.

1. Если процесс принимает подключения на один IP-адрес, но через несколько сетевых интерфейсов, то для каждого интерфейса будет создана отдельная запись. 
2. В записях с диапазонами IP-адресов не будет никаких сведений о действиях. Такие записи используются для обозначения того факта, что какой-либо порт компьютера открыт для входящего трафика.
3. Чтобы снизить уровень детализации и уменьшить объем данных, система пропускает записи с диапазонами IP-адресов, если имеется совпадающая запись (для того же процесса, порта и протокола) с указанным IP-адресом. Если запись с диапазоном IP-адресов пропущена, свойству IsWildcardBind записи с определенным IP-адресом будет присвоено значение True (Истина), указывающее, что порт доступен через любой интерфейс соответствующего компьютера.
4. Порты, которые связаны только на определенном интерфейсе имеют IsWildcardBind набор *для ложных*.

#### <a name="naming-and-classification"></a>Именование и классификация

Для удобства IP-адрес удаленной стороны подключения включается в свойство RemoteIp. Для входящих подключений свойство RemoteIp имеет то же значение, что и свойство SourceIp, а для исходящих подключений у него будет такое же значение, что и у свойства DestinationIp. Свойство RemoteDnsCanonicalNames представляет канонические имена DNS, переданные компьютером для свойства RemoteIp. Свойства RemoteDnsQuestions и RemoteClassification зарезервированы для использования в будущем. 

#### <a name="geolocation"></a>Географическое положение

*VMConnection* для каждой записи подключения содержит указанные ниже свойства со сведениями о географическом положении для удаленной стороны. 

| Свойство | Описание |
|:--|:--|
|RemoteCountry |Название страны/региона, принимающей RemoteIp.  Пример: *United States* (США). |
|RemoteLatitude |Широта географического положения. Пример: *47,68*. |
|RemoteLongitude |Долгота географического положения. Пример: *–122,12*. |

#### <a name="malicious-ip"></a>Вредоносный IP-адрес

Система проверяет, не внесен ли адрес, указанный в свойстве RemoteIp в таблице *VMConnection*, в список IP-адресов, связанных с вредоносными действиями. Если система обнаружит, что адрес, указанный в свойстве RemoteIp, вредоносный, будут заполнены указанные ниже свойства записи (если IP-адрес не считается вредоносным, эти свойства пусты).

| Свойство | Описание |
|:--|:--|
|MaliciousIp |Адрес, указанный в свойстве RemoteIp. |
|IndicatorThreadType |Обнаруженный индикатор угроз является одним из следующих значений: *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
|Описание |Описания наблюдаемой угрозы. |
|TLPLevel |Уровень протокола Traffic Light (TLP) является одним из определенных значений: *White*, *Green*, *Amber*, *Red*. |
|Достоверность |Значения: *0–100*. |
|Severity |Значения: *0–5*, где *5* является самым серьезным, а *0* не является серьезным вообще. Значение по умолчанию — *3*.  |
|FirstReportedDateTime |Впервые поставщик сообщил об этом индикаторе. |
|LastReportedDateTime |В последний раз индикатор был просмотрен Interflow. |
|IsActive |Указывает, что индикаторы деактивированы со значением *True* или *False*. |
|ReportReferenceLink |Ссылки на отчеты, связанные с данным наблюдаемым. |
|AdditionalInformation |Предоставляет дополнительную информацию, если применимо, о наблюдаемой угрозе. |

### <a name="ports"></a>порты; 

Порты на машине, которые активно принимают входящий трафик или потенциально могут принимать трафик, но простаивают во время окна времени отчетности, записываются в таблицу VMBoundPort.  

Каждая запись в VMBoundPort идентифицируется по следующим полям: 

| Свойство | Описание |
|:--|:--|
|Процесс | Идентичность процесса (или групп процессов), с которыми связан порт.|
|Ip | Порт IP-адрес (может быть подстановочный IP, *0.0.0.0)* |
|Порт |Номер порта |
|Протокол | Протокол.  Пример, *tcp* или *udp* (только *tcp* в настоящее время поддерживается).|
 
Идентификация порта получена из вышеуказанных пяти полей и хранится в собственности PortId. Это свойство может быть использовано для быстрого поиска записей для конкретного порта во времени. 

#### <a name="metrics"></a>Метрики 

Записи портов включают метрики, представляющие связанные с ними соединения. В настоящее время сообщается о следующих метриках (детали для каждой метрики описаны в предыдущем разделе): 

- БайтсСент и БайтсПолучен 
- СсылкиУстановлены, СсылкиУничтоженые, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Необходимо учитывать указанные ниже важные моменты.

- Если процесс принимает подключения на один IP-адрес, но через несколько сетевых интерфейсов, то для каждого интерфейса будет создана отдельная запись.  
- В записях с диапазонами IP-адресов не будет никаких сведений о действиях. Такие записи используются для обозначения того факта, что какой-либо порт компьютера открыт для входящего трафика. 
- Чтобы снизить уровень детализации и уменьшить объем данных, система пропускает записи с диапазонами IP-адресов, если имеется совпадающая запись (для того же процесса, порта и протокола) с указанным IP-адресом. Когда IP-запись подстановочного знака опущена, свойство *IsWildcardBind* для записи с конкретным IP-адресом будет установлено в *True.*  Это означает, что порт разоблачается над каждым интерфейсом машины отчетности. 
- Порты, которые связаны только на определенном интерфейсе имеют IsWildcardBind набор *для ложных*. 

### <a name="vmcomputer-records"></a>Записи VMComputer

Записи с типом *VMComputer* имеют данные инвентаризации для серверов с агентом зависимости. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | Описание |
|:--|:--|
|TenantId | Уникальный идентификатор рабочего пространства |
|SourceSystem | *Аналитика* | 
|TimeGenerated | Отметка времени записи (UTC) |
|Компьютер | Полное доменное имя компьютера | 
|AgentId | Уникальный идентификатор агента Log Analytics |
|Компьютер | Название ресурса Azure Resource Manager для машины, выставленной ServiceMap. Это форма *m-ГУИД*, где *GUID* является тем же GUID, как AgentId. | 
|DisplayName | Отображаемое имя | 
|FullDisplayName | Полное имя дисплея | 
|HostName | Название машины без доменного имени |
|BootTime | Время загрузки машины (UTC) |
|TimeZone | Нормализованный часовой пояс |
|ВиртуализацияГосударство | *виртуальный,* *гипервизор*, *физический* |
|Ipv4Адреса | Массив адресов IPv4 | 
|Ipv4SubnetМаски | Массив подсетевых масок IPv4 (в том же порядке, что и Ipv4Addresses). |
|Ipv4DefaultGateways | Массив шлюзов IPv4 | 
|Ipv6Адреса | Массив адресов IPv6 | 
|Макадресы | Массив адресов MAC | 
|DnsNames | Массив имен DNS, связанных с машиной. |
|ЗависимостьAgentVersion | Версия агента зависимости, работая на машине. | 
|ОперационнаяСистемаСемья | *Linux*, *Windows* |
|ОперационнаясистемаFullName | Полное имя операционной системы | 
|ФизическаяпамятьМБ | Физическая память в мегабайтах | 
|Процессоров | Число процессоров | 
|CpuSpeed | Скорость ЦП в МГц | 
|VirtualMachineType | *hyperv*, *vmware*, *xen* |
|VirtualMachineNativeId | Идентификатор виртуальной машины, назначенный ее гипервизором | 
|VirtualMachineNativeName | Имя виртуальной машины |
|ВиртуальнаямашинаГипервизорИД | Уникальный идентификатор гипервизора, принимающего VM |
|ГипервизорТип | *гиперв* |
|ГипервизорИд | Уникальный идентификатор гипервизора | 
|Хостинг-провайдер | *Azure* |
|_ResourceId | Уникальный идентификатор ресурса Azure |
|AzureSubscriptionId | Уникальный в глобальном масштабе идентификатор, идентифицируя вашу подписку | 
|AzureResourceGroup | Имя группы ресурсов Azure, в которую входит машина. |
|AzureResourceName | Название ресурса Azure |
|Лазурная локация | Расположение ресурса Azure |
|AzureUpdateDomain | Имя домена обновления Azure |
|AzureFaultDomain | Имя домена разлома Azure |
|AzureVmId | Уникальный идентификатор виртуальной машины Azure |
|AzureSize | Размер виртуальной машины Azure |
|AzureImagePublisher | Название издателя Azure VM |
|AzureImageПредложение | Название типа предложения Azure VM | 
|AzureImageSku | SKU изображения Azure VM | 
|AzureImageВерсия | Версия изображения Azure VM | 
|AzureCloudServiceName | Название облачного сервиса Azure |
|AzureCloudServiceDeployment | Идентификатор развертывания для облачной службы |
|AzureCloudServiceRoleName | Имя роли облачного сервиса |
|AzureCloudServiceRoleType | Тип роли облачного сервиса: *работник* или *веб* |
|AzureCloudServiceInstanceId | Идентификатор роли облачного сервиса |
|AzureVmScaleSetName | Название набора масштабов виртуальной машины |
|AzureVmScaleSetDeployment | Идентификатор развертывания виртуального набора машинного уровня |
|AzureVmScaleSetResourceId | Уникальный идентификатор ресурса набора масштабов виртуальной машины.|
|AzureVmScaleSetInstanceId | Уникальный идентификатор набора виртуальной машины |
|AzureServiceFabricClusterId | Уникальный идентификатор кластера Azure Service Fabric | 
|AzureServiceFabricClusterName | Название кластера Azure Service Fabric |

### <a name="vmprocess-records"></a>Записи VMProcess

Записи с типом *VMProcess* имеют данные инвентаризации для процессов, подключенных к TCP, на серверах с агентом зависимости. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | Описание |
|:--|:--|
|TenantId | Уникальный идентификатор рабочего пространства |
|SourceSystem | *Аналитика* | 
|TimeGenerated | Отметка времени записи (UTC) |
|Компьютер | Полное доменное имя компьютера | 
|AgentId | Уникальный идентификатор агента Log Analytics |
|Компьютер | Название ресурса Azure Resource Manager для машины, выставленной ServiceMap. Это форма *m-ГУИД*, где *GUID* является тем же GUID, как AgentId. | 
|Процесс | Уникальный идентификатор процесса «Карта обслуживания». Он находится в виде *p-'GUID .* 
|ИсполняемоеИмя | Имя исполняемого файла процесса | 
|DisplayName | Имя отображения процесса |
|Роль | Роль процесса: *веб-сервер,* *appServer*, *база данныхServer*, *ldapServer*, *smbServer* |
|Группа | Название группы процесса. Процессы в одной группе логически связаны между собой, например, частью одного и того же компонента продукта или системы. |
|StartTime | Время запуска пула процессов |
|FirstPid | Первый идентификатор процесса в пуле процессов |
|Описание | Описание процесса |
|CompanyName | Название организации |
|InternalName | Внутреннее имя |
|ProductName | Имя продукта |
|ProductVersion | Версия продукта |
|FileVersion | Версия файла |
|Исполняемый Путь |Путь исполняемого |
|Командная строка | Командная строка |
|WorkingDirectory | Рабочий каталог |
|Службы | Массив услуг, в соответствии с которыми процесс выполнения |
|UserName | Учетная запись, с которой выполняется процесс |
|UserDomain | Домен, в котором выполняется процесс |
|_ResourceId | Уникальный идентификатор для процесса в рабочей области |


## <a name="sample-map-queries"></a>Примеры картографических запросов

### <a name="list-all-known-machines"></a>Список всех известных компьютеров

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Время последней перезагрузки виртуальной машины

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Сводка по виртуальным машинам Azure с информацией об образе, расположении и номере SKU

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Перечислите физическую емкость памяти всех управляемых компьютеров

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Список именем компьютера, DNS, IP и ОС

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Поиск всех процессов с "sql" в командной строке

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Поиск компьютера (самой последней записи) по имени ресурса

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Поиск компьютера (самой последней записи) по IP-адресу

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Вывод списка всех известных процессов на определенном компьютере

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Вывод списка всех компьютеров, на которых выполняется SQL Server

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Вывод списка всех уникальных версий продукта cURL в центре обработки данных

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Создание группы, объединяющей все компьютеры, на которых выполняется CentOS

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>Тренды объема отправленных и полученных данных в байтах

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Виртуальные машины Azure, передающие наибольшее количество байтов

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Тренды состояния канала

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Тренд сбоев подключения

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Связанные порты

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Количество открытых портов между машинами

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Оценка процессов в рабочей области по количеству открытых портов

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Агрегированное поведение для каждого порта

Этот запрос может затем использоваться для оценки портов по активности, например, портов с наибольшим входящим/исходящим трафиком, портов с большинством соединений
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Создание сводки исходящих подключений для группы компьютеров

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="performance-records"></a>Записи производительности
Записи с типом *InsightsMetrics* имеют данные о производительности из гостевой операционной системы виртуальной машины. У этих записей есть свойства, приведенные в таблице ниже.


| Свойство | Описание |
|:--|:--|
|TenantId | Уникальный идентификатор для рабочего пространства |
|SourceSystem | *Аналитика* | 
|TimeGenerated | Время сбора значения (UTC) |
|Компьютер | Полное доменное имя компьютера | 
|Исходный домен | *vm.azm.ms* |
|Пространство имен | Категория счетчика производительности | 
|Имя | Имя счетчика производительности. |
|Val | Собранная стоимость | 
|Теги | Похожие подробности о записи. Смотрите таблицу ниже для тегов, используемых с различными типами записей.  |
|AgentId | Уникальный идентификатор для агента каждого компьютера |
|Тип | *ИнсайтсМетрики* |
|_ResourceId_ | Идентификатор ресурсов виртуальной машины |

Счетчики производительности, собранные в настоящее время в таблице *InsightsMetrics,* перечислены в следующей таблице:

| Пространство имен | Имя | Описание | Единицы | Теги |
|:---|:---|:---|:---|:---|
| Компьютер    | Пульс             | Компьютерное сердцебиение                        | | |
| Память      | Доступноmb           | Память доступна байтов                    | Байты          | memorySizeMB - Общий размер памяти|
| Сеть     | WriteBytesPerSecond   | Сеть Написать Байты в секунду            | Байт/с | NetworkDeviceId - Ид устройства<br>байты - Всего отправлено байтов |
| Сеть     | ReadBytesPerSecond    | Сеть Читать Байты в секунду             | Байт/с | networkDeviceId - Ид устройства<br>байты - Всего получено байт |
| Процессор   | ИспользованиеПроцентная доля | Процент использования процессоров          | Процент        | totalCpus - Всего процессоров |
| Логический диск | WritesPerSecond       | Логический диск пишет в секунду            | Число/с | mountId - Идентификатор горы устройства |
| Логический диск | WriteLatencyMs        | Логический диск Написать Задержка Миллисекунда    | MilliSeconds   | mountId - Идентификатор горы устройства |
| Логический диск | WriteBytesPerSecond   | Логический диск Написать Байты в секунду       | Байт/с | mountId - Идентификатор горы устройства |
| Логический диск | TransfersPerSecond    | Логические передачи диска в секунду         | Число/с | mountId - Идентификатор горы устройства |
| Логический диск | TransferLatencyMs     | Логическая задержка передачи диска Миллисекунда | MilliSeconds   | mountId - Идентификатор горы устройства |
| Логический диск | ReadsPerSecond        | Логический диск читает в секунду             | Число/с | mountId - Идентификатор горы устройства |
| Логический диск | ReadLatencyMs         | Логический диск Читать Задержка Миллисекунда     | MilliSeconds   | mountId - Идентификатор горы устройства |
| Логический диск | ReadBytesPerSecond    | Логический диск Читать Байты в секунду        | Байт/с | mountId - Идентификатор горы устройства |
| Логический диск | FreeSpaceПроцент   | Логический диск Свободное пространство Процент        | Процент        | mountId - Идентификатор горы устройства |
| Логический диск | FreeSpaceMB           | Логический диск Свободное пространство Байты             | Байты          | mountId - Идентификатор горы устройства<br>diskSizeMB - Общий размер диска |
| Логический диск | Байт/с        | Логический диск Байты в секунду             | Байт/с | mountId - Идентификатор горы устройства |


## <a name="next-steps"></a>Следующие шаги

* Если вы новичок в написании журналов в Azure Monitor, просмотрите, [как использовать аналитику журнала](../../azure-monitor/log-query/get-started-portal.md) на портале Azure для записи запросов журналов.

* Подробнее о [написании поисковых запросов.](../../azure-monitor/log-query/search-queries.md)
