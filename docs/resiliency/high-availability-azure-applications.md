---
title: "Обеспечение высокой доступности для приложений Azure"
description: "Технический обзор и подробные сведения о проектировании и разработке приложений c высоким уровнем доступности на платформе Microsoft Azure."
author: adamglick
ms.date: 05/31/2017
ms.openlocfilehash: 46b7b802326a8de03546528aaeb1a1c6419d41db
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2017
---
[!INCLUDE [header](../_includes/header.md)]
# <a name="high-availability-for-applications-built-on-microsoft-azure"></a>Обеспечение высокой доступности для приложений на платформе Microsoft Azure
Приложения с высоким уровнем доступности выдерживают колебания доступности, нагрузки и временных сбоев в зависимых службах и оборудовании. Приложение продолжает работать в правильном режиме, соответствующем бизнес-требованиям или положениям соглашений об уровне обслуживания.

## <a name="azure-high-availability-features"></a>Возможности обеспечения высокой доступности Azure
Платформа Azure предусматривает множество встроенных возможностей для поддержки приложений с высоким уровнем доступности. В этом разделе описаны некоторые из этих основных возможностей.

### <a name="fabric-controller"></a>Контроллер структуры
Контроллер структуры Azure подготавливает ресурсы и осуществляет мониторинг состояния вычислительных операций Azure. Он отслеживает состояние оборудования и программного обеспечения экземпляров гостевого и хост-компьютера. При обнаружении сбоя контроллер структуры применяет соглашения об уровне обслуживания, автоматически перемещая экземпляры виртуальной машины. Домены обновления и сбоя обеспечивают дальнейшую поддержку соглашения об уровне обслуживания в отношении вычислений.

При развертывании нескольких экземпляров роли облачной службы Azure развертывает их в разных доменах сбоя. По факту граница домена сбоя расположена в аппаратной стойке в том же регионе. Домены сбоя снижают вероятность прерывания работы приложения после локализованного сбоя оборудования. Вы не можете управлять количеством доменов сбоя рабочих или веб-ролей. Контроллер структуры использует выделенные ресурсы, независимые от размещенных в Azure приложений. Он всегда должен находиться в работоспособном состоянии, так как от него зависит работа системы Azure. Контроллер структуры отслеживает и управляет экземплярами ролей в доменах сбоя.

На схеме ниже показаны общие ресурсы Azure, развернутые контроллером структуры на разных доменах сбоя и управляемые им.

![Упрощенное представление изоляции доменов сбоя](./images/high-availability-azure-applications/fault-domain-isolation.png)

В то время как домены сбоя являются физическими единицами разделения для устранения неполадки, домены обновления — это логические единицы разделения экземпляров, которые определяют, какие экземпляры службы будут обновлены в определенный момент времени. По умолчанию для развертывания размещенной службы определено пять доменов обновления. Но это количество можно изменить в файле определения службы. Например, если у вас есть восемь экземпляров веб-роли, два экземпляра будут находиться в трех доменах обновления и два экземпляра — в одном домене обновления. Azure определяет последовательность обновления на основе количества доменов обновления. Дополнительные сведения см. в статье [Обновление облачной службы](/azure/cloud-services/cloud-services-update-azure-service/).

### <a name="features-in-other-services"></a>Возможности в других службах
Помимо этих возможностей платформы, обеспечивающих поддержку высокого уровня доступности вычислительных ресурсов, Azure внедряет возможности обеспечения доступности в другие службы. Например, служба хранилища Azure поддерживает по крайне мере три реплики для всех данных в учетной записи хранения Azure. Она также предоставляет возможность георепликации для хранения копий данных в дополнительном регионе. Сеть доставки содержимого (CDN) Azure позволяет кэшировать большие двоичные объекты в регионах по всему миру для обеспечения избыточности, масштабируемости и низкой задержки. База данных SQL Azure также поддерживает несколько реплик.

Более подробные сведения о возможностях обеспечения доступности платформы Azure см. в статье [Проектирование устойчивых приложений для Azure](index.md). Ознакомьтесь также со статьей [Best Practices for Designing Large-Scale Services on Windows Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) (Рекомендации по разработке крупномасштабных служб в Windows Azure).

Хотя Azure и предоставляет много возможностей, обеспечивающих поддержку высокой доступности, важно знать их ограничения.

* Azure гарантирует постоянную доступность и работу ролей для вычислительных операций, но не отслеживает работу приложений или наличие перегрузки.
* Данные Базы данных SQL Azure синхронно реплицируются в регионе. Можно выбрать активную георепликацию, при которой в одном или в разных регионах может храниться четыре дополнительных копии базы данных. Хотя эти реплики базы данных не являются резервными копиями на определенный момент времени, база данных SQL обеспечивает возможность резервного копирования на определенный момент времени. Дополнительные сведения см. в разделе [Восстановление до точки во времени](/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore).
* Данные таблиц и больших двоичных объектов службы хранилища Azure по умолчанию реплицируются в другой регион. Несмотря на это доступ к репликам можно получить только после того, как Майкрософт выполнит обработку отказа на другом сайте. Отработка отказа региона выполняется во время длительных нарушений в работе служб на уровне региона и отсутствия соглашения об уровне обслуживания на время географической отработки отказа. Кроме того, важно помнить, что любое повреждение данных быстро распространяется на реплики. Поэтому рекомендуется тщательно изучить возможности обеспечения доступности платформ, в том числе зависящие от приложений, включая возможности создания моментальных снимков больших двоичных объектов, что позволяет создавать резервные копии данных соответствующих объектов на определенный момент времени.

### <a name="availability-sets-for-azure-virtual-machines"></a>Группы доступности для виртуальных машин Azure
В этом документе основное внимание уделяется облачным службам, которые используют модель PaaS (платформа как услуга). Есть также определенные возможности обеспечения доступности для виртуальных машин Azure, которые используют модель IaaS (инфраструктура как услуга). Чтобы обеспечить высокий уровень доступности для виртуальных машин, необходимо использовать группы доступности, которые выполняют ту же функцию, что и домены сбоя и обновления. Azure помещает виртуальные машины в группы доступности, предотвращая нарушение работы всех машин в группе и ограничивая влияние локализованных сбоев оборудования, а также действий по обслуживанию. Группы доступности требуются в рамках соглашения об уровне обслуживания Azure для обеспечения доступности виртуальных машин.

На схеме ниже показаны две группы доступности для виртуальных машины в сети и SQL Server соответственно.

![Группы доступности для виртуальных машин Azure](./images/high-availability-azure-applications/availability-set-for-azure-virtual-machines.png)

> [!NOTE]
> На схеме выше предполагается, что на виртуальных машинах установлен и запущен SQL Server. В этом состоит отличие от базы данных SQL Azure, которая предоставляет возможность управления базой данных.
> 
> 

## <a name="application-strategies-for-high-availability"></a>Стратегии по обеспечению высокой доступности приложений
Большинство стратегий по обеспечению высокой доступности приложений основаны либо на избыточности, либо на удалении строгих зависимостей между компонентами приложений. Архитектура приложения должна предусматривать отказоустойчивость на случай разового простоя Azure или поддержку сторонних служб. В следующих разделах рассматриваются шаблоны приложений, которые позволяют повысить уровень доступности облачных служб.

### <a name="asynchronous-communication-and-durable-queues"></a>Асинхронная передача данных и долгосрочные очереди
Для увеличения доступности приложений Azure рекомендуется использовать асинхронную передачу данных между слабосвязанными службами. В этом шаблоне сообщения записываются для последующей обработки либо в очереди хранилища, либо в очереди служебной шины Azure. После записи сообщения в очередь управление получает его отправитель. За обработку сообщения отвечает другая служба приложения (как правило, реализуется в виде рабочей роли). Если служба обработки перестает работать, сообщения накапливаются в очередь до ее восстановления. Между внешним отправителем и обработчиком сообщений нет прямой зависимости. Эта позволяет избежать выполнения синхронных вызовов службы, которые могут стать причиной возникновения узких мест в распределенных приложениях.

Как вариант, шаблон может хранить сведения о неудачных вызовах базы данных в очереди службы хранилища Azure (большие двоичные объекты, таблицы, очереди) или служебной шины. Например, синхронные вызовы другой службы (такой как База данных SQL Azure) из приложения постоянно завершаются сбоем. Вы можете сериализовать этот запрос в долговременном хранилище. На более позднем этапе после восстановления работоспособности службы или базы данных приложение может повторно отправить запрос из хранилища. Эта модель отличается тем, что промежуточное расположение не используется постоянно в рабочем процессе приложения, а только во время сбоев.

В обоих сценариях асинхронная передача данных и использование промежуточного хранилища предотвращают прерывание работы всего приложения после сбоя серверной службы. Очереди используются в качестве логического посредника. Дополнительные сведения о выборе служб очередей см. в статье [Очереди службы хранилища и очереди служебной шины: сходства и различия](/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted/).

### <a name="fault-detection-and-retry-logic"></a>Логика обнаружения сбоев и логический алгоритм повтора
Важным фактором в создании высокодоступных приложений является использование в коде логики повтора, которая позволяет соответствующим образом управлять временно недоступной службой. Последние версии пакетов SDK для службы хранилища Azure и служебной шины Azure имеют встроенный механизм повторных попыток. Дополнительные сведения о предоставлении пользовательской логики повторных попыток для приложения см. в статье о [шаблоне повторов](../patterns/retry.md).

### <a name="reference-data-pattern-for-high-availability"></a>Шаблон ссылочных данных для обеспечения высокого уровня доступности
Ссылочные данные — это данные приложения только для чтения. Они содержат бизнес-контекст, на основе которого приложение создает данные о транзакциях во время бизнес-операции. Целостность данных о транзакциях зависит от моментального снимка эталонных данных в момент выполнения транзакции.

Эталонные данные необходимы для корректной работы приложения. В разных приложениях создание и обслуживание ссылочных данных зачастую выполняют системы управления основными данными. Эти системы отвечают за жизненный цикл ссылочных данных. К ссылочным данным относятся каталоги продукции, данные о сотрудниках, деталях и оборудовании. Эти данные (например, почтовые индексы или налоговые ставки) также могут поступать из внешних источников. Обычно стратегии по увеличению доступности ссылочных данных не такие сложные, как стратегии данных о транзакциях. Преимущество ссылочных данных состоит в том, что в основном они неизменяемые.

Веб-роли и рабочие роли Azure, которые используют эталонные данные, можно создать в автономном режиме во время выполнения, развернув эти данные вместе с приложением. Этот подход идеально подходит, если размера локального хранилища достаточно для выполнения этого развертывания. Встроенные базы данных SQL и NoSQL, а также XML-файлы, развернутые локально, позволяют обеспечить автономию единиц масштабирования вычислительных ресурсов Azure. Но для каждой роли вам понадобится механизм обновления данных, для которого не нужно будет повторное развертывание. Для этого разместите обновления ссылочных данных в конечной точке облачного хранилища (например, в хранилище BLOB-объектов Azure или базе данных SQL). Добавьте код для каждой роли, которая при запуске загружает обновления данных в вычислительные узлы. Кроме того, можно добавить код, который позволит администратору выполнять принудительную загрузку в экземпляры ролей.

Чтобы увеличить доступность, в роли следует включить набор ссылочных данных на случай прерывания работы хранилища. Роли можно запускать с основным набором ссылочных данных, пока ресурсы хранилища не станут доступными для обновления.

![Обеспечение высокого уровня доступности приложения за счет автономных вычислительных узлов](./images/high-availability-azure-applications/application-high-availability-through-autonomous-compute-nodes.png)

С использованием этого шаблона время запуска новых развертываний или экземпляров ролей может увеличиться, если вы развертываете или скачиваете большой объем эталонных данных. Это может быть приемлемо, если необходимо автономно использовать ссылочные данные для каждой роли и не зависеть от внешних служб хранилищ.

### <a name="transactional-data-pattern-for-high-availability"></a>Шаблон данных о транзакциях для обеспечения высокого уровня доступности
Данные о транзакциях — это данные, созданные приложением в бизнес-контексте. Они представляют собой сочетание реализованных приложением бизнес-процессов и ссылочных данных, необходимых для выполнения этих процессов. К ним могут относиться заказы, предварительные уведомления о доставке, счета-фактуры и возможности по управлению отношениями с клиентами. Данные о транзакциях передаются во внешние системы для ведения учета или последующей обработки.

Эталонные данные могут изменяться в системах, которые отвечают за них. Поэтому в данных о транзакциях должен быть контекст эталонных данных в определенный момент времени, чтобы свести к минимуму внешние зависимости и обеспечить семантическую согласованность. Например, продукт может быть удален из каталога через несколько месяцев после выполнения заказа. Рекомендуется хранить как можно больше контекста эталонных данных в транзакциях. Это позволит сохранить связанную с транзакцией семантику даже при изменении эталонных данных после записи транзакции.

Как упоминалось ранее, архитектуры, использующие слабые связи и асинхронную передачу данных, предоставляют более высокий уровень доступности. Это касается также данных о транзакциях, но их реализация носит более сложный характер. Обычно для традиционных шаблонов транзакций используется база данных. Это позволяет обеспечить выполнение транзакции. Чтобы обеспечить достаточный уровень согласованности и устойчивости, при внедрении промежуточных уровней код приложения должен соответствующим образом обрабатывать данные разных уровней.

Следующий список — это описание рабочего процесса, который разделяет сбор данных о транзакциях и их обработку.

1. Вычислительный веб-узел предоставляет ссылочные данные.
2. Внешнее хранилище сохраняет промежуточные данные о транзакциях.
3. Вычислительный веб-узел завершает пользовательскую транзакцию.
4. Вычислительный веб-узел отправляет полные данные о транзакциях и контекст эталонных данных во временное надежное хранилище с гарантией отправки прогнозируемого ответа.
5. Вычислительный веб-узел сигнализирует о завершении транзакции.
6. Фоновый вычислительный узел извлекает данные о транзакциях, при необходимости выполняет последующую обработку, а затем отправляет данные в окончательное место хранения в текущей системе.

На следующей схеме показан один из возможных вариантов реализации этой архитектуры в размещенной облачной службе Azure.

![Обеспечение высокой доступности за счет слабых связей](./images/disaster-recovery-high-availability-azure-applications/application-high-availability-through-loose-coupling.png)

Пунктирными линиями на схеме выше обозначена асинхронная обработка. Внутренняя веб-роль не учитывает асинхронную обработку. Эти пунктирные линии указывают на окончательное место хранения транзакции со ссылкой на текущую систему. Из-за задержки, созданной асинхронной моделью, данные о транзакциях не сразу становятся доступными для запроса. Поэтому каждую единицу данных о транзакциях необходимо сохранить в кэше или пользовательском сеансе, чтобы сразу же обеспечивать соответствие требованиям пользовательского интерфейса.

Веб-роль становится автономной и не зависит от остальной инфраструктуры. Ее доступность зависит от веб-роли и очереди Azure, а не от всей инфраструктуры. Помимо высокого уровня доступности такой подход позволяет веб-роли выполнять горизонтальное масштабирование независимо от серверного хранилища. Эта модель обеспечения высокого уровня доступности может влиять на затраты, связанные с операциями. Использование дополнительных компонентов, таких как очереди и рабочие роли Azure, может повлиять на ежемесячные затраты.

На предыдущей схеме показан метод обеспечения высокой доступности за счет слабых связей. Но существуют и другие способы реализации. Ниже приведены некоторые альтернативы:

* Рабочую роль можно разместить между веб-ролью и очередью хранилища.
* Вместо очереди службы хранилища Azure можно использовать очередь служебной шины.
* В качестве окончательного места хранения можно использовать службу хранилища Azure или другой поставщик базы данных.
* Кэш Azure можно использовать на веб-уровне, чтобы указать безотлагательные требования к кэшированию после выполнения транзакции.

### <a name="scalability-patterns"></a>Шаблоны масштабируемости
Важно отметить, что масштабируемость облачной службы напрямую влияет на доступность. Если из-за увеличения нагрузки служба перестанет отвечать, может сложиться впечатление, что приложение не работает. Следуйте проверенным рекомендациям по масштабированию в зависимости от необходимой нагрузки и ожидаемых возможностей приложения. При максимальном масштабировании необходимо учесть множество факторов, таких как количество используемых учетных записей хранения, общий доступ между несколькими базами данных и стратегии кэширования. Подробные сведения об этих шаблонах см. в статье [Best Practices for Designing Large-Scale Services on Windows Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) (Рекомендации по разработке крупномасштабных служб в Windows Azure).

## <a name="next-steps"></a>Дальнейшие действия
В этой серии статей рассматривается аварийное восстановление и высокая доступность для приложений на платформе Microsoft Azure. Следующая статья в этой серии — [Disaster recovery for Azure applications](disaster-recovery-azure-applications.md) (Аварийное восстановление для приложений на платформе Azure).
