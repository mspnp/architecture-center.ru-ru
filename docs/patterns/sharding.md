---
title: "Сегментирование"
description: "Вы можете разделить хранилище данных на несколько горизонтальных секций, которые называются сегментами."
keywords: "конструктивный шаблон"
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- data-management
- performance-scalability
ms.openlocfilehash: 328483e24c75137f07576104d50dc59d426b8ac4
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2017
---
# <a name="sharding-pattern"></a>Шаблон сегментирования

[!INCLUDE [header](../_includes/header.md)]

Вы можете разделить хранилище данных на несколько горизонтальных секций, которые называются сегментами. Это повышает масштабируемость при хранении больших объемов данных и обращении к ним.

## <a name="context-and-problem"></a>Контекст и проблема

На хранилище данных, размещенное на обособленном сервере, могут распространятся следующие ограничения.

- **Дисковое пространство**. Хранилища данных для крупных облачных приложений обычно содержат очень много информации, объем которой со временем увеличивается. Обычно объем дискового пространства на сервере ограничен. Хотя вы можете заменять существующие диски более емкими или добавлять дополнительные диски по мере накопления данных. Но рано или поздно система достигнет некоторого предела, сверх которого емкость хранилища на этом сервере увеличить будет невозможно.

- **Вычислительные ресурсы**. Облачные приложения должны поддерживать большое число одновременно работающих пользователей, каждый из которых выполняет запросы на получение сведений из хранилища данных. Один сервер, на котором хранятся эти данные, не всегда готов предоставить достаточную вычислительную мощность для такой загрузки. Для пользователей это приводит к увеличению времени ожидания и частым сбоям, вызванным превышением времени ожидания при операциях сохранения и извлечения данных. Иногда можно добавить память или обновить процессор, но система скоро достигнет предела, выше которого вычислительные ресурсы наращивать невозможно.

- **Пропускная способность сети**. Производительность хранилища данных, запущенного на обособленном сервере, определяется еще и скоростью, с которой сервер может получать запросы и отправлять ответы. Когда объем сетевого трафика превышает пропускную способность сети, к которой подключен сервер, завершение некоторых запросов сбоями — реальная ситуация.

- **География**. Иногда нужно, чтобы создаваемые пользователями данные хранились в том же регионе, где находятся сами пользователи. Это может быть связано с законодательными ограничениями, стандартами или требованиями к производительности и допустимой задержке при доступе к данным. Если при этом пользователи распределены по разным странам и регионам, вы не сможете хранить все данные приложения в одном хранилище данных.

Вертикальное масштабирование, например увеличение емкости дисков, вычислительной мощности, объема памяти или числа сетевых адаптеров, позволит обойти некоторые из этих ограничений, но, скорее всего, только временно. Коммерческое облачное приложение, поддерживающее большое число пользователей и оперирующее огромными объемами данных, должно масштабироваться практически без ограничений, поэтому вертикальное масштабирование не всегда будет наилучшим решением.

## <a name="solution"></a>Решение

Вы можете разделить хранилище данных на горизонтальные секции (сегменты). Все сегменты имеют одинаковую схему данных, но хранят разные подмножества данных. Каждый сегмент сам по себе является хранилищем данных, которое может содержать данные для нескольких сущностей разных типов. Сегменты выполняются на сервере, который используется как узел хранения.

Такой подход связан со следующими преимуществами:

- можно масштабировать систему, добавляя новые сегменты, запущенные на дополнительных узлах хранения;

- система может работать на стандартном оборудовании, не требуя специализированных и дорогих компьютеров для каждого узла хранения;

- можно минимизировать конфликты и повысить производительность, правильно распределяя рабочую нагрузку между сегментами;

- сегменты, размещенные в облаке, могут физически располагаться рядом с пользователями, которые будут обращаться к данным.

Когда вы разделяете хранилище данных на сегменты, вам нужно определить, какие данные в какой сегмент будут помещены. Сегмент обычно содержит элементы, относящиеся к определенному диапазону по одному или нескольким атрибутам. Эти атрибуты формируют ключ сегмента (иногда его называют ключом секции). Ключ сегмента должен быть статическим. Он не должен основываться на данных, которые могут измениться.

Сегментирование определяет физическую структуру данных. Когда приложение сохраняет или извлекает данные, логика сегментирования определяет, к какому сегменту нужно обратиться. Эта логика сегментирования может быть реализована как в самом приложении, то есть в коде доступа к данным, так и в системе хранения данных, если хранилище прозрачно поддерживает сегментирование.

Решая вопросы физического расположения данных с использованием логики сегментирования, вы получаете возможность точно управлять распределением данных между сегментами. Если потребуется, вы сможете легко переносить данные между сегментами без необходимости изменять бизнес-логику приложения впоследствии (например, если изменится баланс распределения данных по сегментам). Единственный недостаток такой схемы — дополнительная нагрузка при доступе к данным, связанная с определением расположения каждого элемента данных при его получении.

Чтобы оптимально сбалансировать производительность и масштабируемость, очень важно правильно разделить данные в соответствии с тем, какие запросы выполняет приложение. Во многих случаях схемы сегментирования не смогут точно соответствовать требованиям каждого запроса. Например, приложение в многопользовательской системе может запрашивать данные о клиенте либо по идентификатору клиента, либо по значению какого-либо атрибута данных, включая имя или расположение клиента и т. п. В таких ситуациях желательно использовать такую стратегию сегментирования, при которой ключ сегмента соответствует наиболее часто выполняемым запросам.

Если нужно регулярно получать данные, используя сочетание значений некоторых атрибутов, вы можете определить составной ключ сегмента, связав нужные атрибуты. Можно использовать и другой подход — применить шаблон [таблицы индексов](index-table.md), который позволит вам быстро находить данные по значениям атрибутов, не входящих в ключ сегмента.

## <a name="sharding-strategies"></a>Стратегии сегментирования

При выборе ключа сегмента и правил распределения данных по сегментам обычно используется одна из трех стратегий. Обратите внимание, что число сегментов и серверов, на которых они размещены, может не совпадать — один сервер может размещать несколько сегментов. Давайте рассмотрим эти стратегии.

**Стратегия поиска**. В этом варианте логика сегментирования использует карту сопоставления по ключу сегмента и направляет запросы в тот сегмент, который содержит нужные данные. Например, в многопользовательском приложении все данные каждого клиента можно хранить в одном сегменте, тогда идентификатор клиента можно использовать как ключ сегментирования. Данные нескольких клиентов могут храниться в одном сегменте, но данные одного клиента не могут оказаться в нескольких сегментах. На рисунке показан вариант сегментирования данных по идентификаторам клиентов.

   ![Рис. 1. Сегментирование данных по идентификаторам клиентов](./_images/sharding-tenant.png)


   Для сопоставления значений ключа сегмента и физического хранилища можно использовать физические сегменты, тогда каждый ключ сегмента будет сопоставлен с физической секцией. Есть и более гибкий способ распределения сегментов — виртуальное секционирование. В этом случае ключи сегмента сопоставляются с таким же числом виртуальных сегментов, которые в свою очередь сопоставляются с меньшим числом физических секций. В этом варианта приложение находит данные с использованием ключа сегмента, который ссылается на виртуальный сегмент, а система прозрачно сопоставляет виртуальные сегменты с физическими секциями. Такое сопоставление между виртуальными сегментами и физическими секциями можно в любой момент изменить, не прибегая к программным методам, а просто применив другой набор ключей сегмента.

**Стратегия диапазонов**. В этом варианте связанные элементы группируются в один сегмент, а затем упорядочиваются по ключу сегмента — то есть значения ключа сегмента располагаются последовательно. Это полезно для приложений, которые часто получают наборы элементов из некоторого диапазона значений (запросы к данным возвращают набор элементов, для которых значения ключа сегмента попадают в заданный диапазон). Например если приложению нужно регулярно получать список всех заказов, размещенных в определенном месяце, эти данные можно получать быстрее, разместив заказы за каждый месяц в одном сегменте и упорядочив их по дате и времени. Если заказы окажутся в разных сегментах, их придется извлекать по отдельности, то есть выполнять большое количество точечных запросов (возвращающих один элемент данных). На следующем рисунке показана схема хранения последовательных наборов (диапазонов) данных в сегменте.

   ![Рис. 2. Хранение последовательных наборов (диапазонов) данных в сегментах](./_images/sharding-sequential-sets.png)

В этом примере используется составной ключ сегмента, наиболее значимым элементом которого является месяц заказа, затем которым следуют день и время заказа. При создании новых заказов и размещении их по сегментам данные естественным образом сортируются. Некоторые хранилища данных поддерживают ключи сегмента, состоящие из двух элементов — ключа секции, который определяет сегмент, и ключа строки, который однозначно определяет элемент в пределах сегмента. Обычно данные в сегменте хранятся в порядке значений ключа строки. Элементы, для которых применяются запросы по диапазонам значений и которые нужно хранить в одном сегменте, будут иметь одинаковые значения ключа секции и уникальные значения ключа строки.

**Стратегия хэширования**. Эта стратегия позволяет снизить вероятность возникновения высокоактивных сегментов (сегментов с непропорционально высокой нагрузкой). Данные в этом варианте распределяются между сегментами так, чтобы соблюдался баланс между размером каждого сегмента и средней нагрузкой, которая приходится на этот сегмент. Логика сегментирования вычисляет, в каком сегменте хранить каждый элемент данных, по значению хэш-индекса по одному или нескольким атрибутам данных. Функция хэширования должна быть выбрана так, чтобы равномерно распространять данные между сегментами. Возможно, для этого придется добавить в формулу вычисления некоторый случайный элемент. На следующем рисунке изображено сегментирование данных по хэшу идентификаторов клиента.

   ![Рис. 3. Сегментирование данных по хэшу идентификаторов клиента.](./_images/sharding-data-hash.png)

Чтобы понять преимущества стратегии хэширования в сравнении с другими стратегиями сегментирования, рассмотрим следующий пример. Многопользовательское приложение поочередно регистрирует новых клиентов и распределяет полученные данные между сегментами в хранилище данных. Если использовать стратегию диапазонов, все данные о клиентах с идентификаторами от 1 до n сохраняются в сегменте A, с идентификаторами от n+1 до m — в сегменте B и т. д. Если предположить, что наибольшую активность проявляют недавно зарегистрированные клиенты, наибольшая нагрузка по работе с данными придется на небольшое число сегментов, что создаст высокоактивный сегмент. В этой же ситуации стратегия хэширования будет распределять клиентов по сегментам на основе хэша идентификатора. Это означает, что клиенты с последовательными идентификаторами, скорее всего, попадут в разные сегменты, что позволит равномерно распределить нагрузку. На рисунке выше показано такое поведение для клиентов с идентификаторами 55 и 56.

Далее описаны преимущества и особенности каждой из трех основных стратегий сегментирования.

- **Поиск**. Обеспечивает максимальный контроль над настройкой и использованием сегментов. Используя виртуальные сегменты, можно устранить проблемы с балансировкой данных благодаря возможности добавлять новые физические секции для распределения рабочей нагрузки. Сопоставление виртуальных сегментов и физических секций, на которых они реализованы, можно изменять, не прибегая к программным методам, то есть не меняя код приложения, который сохраняет и извлекает данные по значению ключа сегмента. Поиск расположения сегмента может создавать дополнительную нагрузку.

- **Диапазон**. Легко реализуется и хорошо работает с запросами по диапазонам, так как часто позволяет с помощью одной операции получить несколько элементов данных из одного сегмента. Эта стратегия упрощает процессы управления данными. Например, если включить в один сегмент пользователей из одного региона, можно запланировать обновления отдельно для каждого часового пояса на основе локальных особенностей использования нагрузки и запросов. Но эта стратегия не позволяет обеспечить оптимальную балансировку между сегментами. Перераспределение данных между сегментами будет сложным и не решит проблему неравномерной нагрузки, если основная часть нагрузки создается для данных с близкими значениями ключа сегмента.

- **Хэш**. Позволяет распределять данные и нагрузку наиболее равномерно. Маршрутизация запросов выполняется прямо из приложения с использованием хэш-функции. Нет необходимости использовать карту сопоставления. Обратите внимание, что вычисление хэша может создать дополнительную нагрузку. Кроме того, это осложнит перераспределение нагрузки между сегментами.

Чаще всего в системах сегментирования используется один из описанных выше подходов, но вам нужно учитывать бизнес-требования и сценарии использования данных для своего приложения. Для примера рассмотрим мультитенантное приложение.

- Вы можете сегментировать данные в зависимости от рабочей нагрузки. Вы можете распределить данные клиентов с очень нестабильным уровнем нагрузки в отдельные сегменты. Это может ускорить доступ к данным для других клиентов.

- Вы можете сегментировать данные по расположению клиентов. Это позволит выполнять резервное копирование и обслуживание данных для клиентов из определенного географического региона в такие периоды, когда нагрузка в этом регионе будет минимальной. При этом данные клиентов из других регионов, где в это время продолжается рабочий день, останутся доступными.

- Для особо ценных клиентов можно выделить отдельные сегменты с высокой производительностью и низкой загрузкой, а менее важные клиенты будут совместно использовать более плотно загруженные сегменты.

- Если отдельным клиентам требуется высокий уровень изоляции данных и конфиденциальности, их данные можно разместить на отдельном сервере.

## <a name="scaling-and-data-movement-operations"></a>Операции масштабирования и перемещения данных

Каждая стратегия сегментирования создает разные возможности и разные уровни сложности в контексте изменения масштаба, перемещения данных и поддержания состояния.

Стратегия поиска позволяет выполнять масштабирование и перемещение данных на уровне пользователей, и для этого не нужно отключать систему. Вам достаточно временно приостановить операции пользователей или некоторую их часть (это можно сделать в периоды низкой нагрузки), затем переместить данные в другую виртуальную секцию или другой физический сегмент, изменить карту сопоставлений и объявить недействительными или обновить все кэшированные данные. После этого можно возобновить работу пользователей. Операции такого типа часто можно выполнять централизованно. Стратегия поиска требует, чтобы состояние данных хорошо согласовывалось с кэшированием и реплицированием.

Стратегия диапазонов накладывает некоторые ограничения на операции масштабирования и перемещения данных. Обычно их нужно выполнять, отключив от сети хранилище данных или его часть, так как данные нужно делить и объединять во многих сегментах. Перемещение данных для балансирования сегментов не всегда позволит решить проблему неравномерной нагрузки, если основная часть нагрузки создается для данных с близкими значениями ключа сегмента или с идентификаторами из одного диапазона. Стратегия диапазонов также может включать действия по поддержанию состояния, чтобы диапазоны можно было сопоставить с физическими секциями.

Стратегия хэширования существенно осложняет операции масштабирования и перемещения данных, так как ключи секций — это хэш-индексы ключей сегментов или идентификаторов данных. Нужно определить новое расположение для каждого сегмента на основе результатов выполнения хэш-функции или изменить эту функцию для правильного сопоставления. Но зато стратегия хэширования не требует поддержания состояния.

## <a name="issues-and-considerations"></a>Проблемы и рекомендации

При принятии решения о реализации этого шаблона необходимо учитывать следующие моменты.

- Сегментирование можно использовать совместно с другими видами секционирования, такими как вертикальное или функциональное секционирование. Например, один сегмент может содержать сущности, которые секционированы по вертикали, а функциональное секционирование можно реализовать в виде нескольких сегментов. Дополнительные сведения о секционировании данных см. в [этом руководстве](https://msdn.microsoft.com/library/dn589795.aspx).

- Поддерживайте сбалансированное распределение нагрузки между сегментами, чтобы каждый из них обрабатывал сопоставимый объем операций ввода-вывода. По мере добавления и удаления данных нужно периодически перераспределять данные между сегментами, чтобы обеспечить равномерную нагрузку и избежать появления высокоактивных сегментов. Перераспределение может потребовать значительных ресурсов. Чтобы снизить потребность в перераспределении, учитывайте развитие системы при проектировании. Каждый сегмент должен иметь достаточно свободного места для обработки ожидаемого объема изменений. Также следует подготовить стратегии и планы для быстрого перераспределения данных, если это потребуется.

- Используйте для ключа сегментов неизменяемые данные. Если для какого-либо элемента данных изменится значение ключа сегмента, возможно, его придется переместить в другой сегмент. Это увеличит объем работы при операциях обновления. Поэтому следует избегать сегментирования с использованием таких данных, которые могут измениться. Вместо этого выберите те атрибуты, которые остаются постоянными или формируют естественный ключ.

- Обеспечьте уникальность значений для ключа сегмента. Например, в качестве ключа сегмента нежелательно использовать поля с автоприращением. Некоторые системы не позволяют скоординировать автоприращение между несколькими сегментами, и тогда в разных сегментах могут появиться элементы данных с одинаковым значением ключа сегмента.

    >  Автоприращение значений в других полях, не входящих в ключ сегмента, также может вызвать проблемы. Например, если поля с автоприращением используются для создания уникальных идентификаторов, в разных сегментах двум элементам данных могут быть назначены одинаковые идентификаторы.

- Не всегда будет возможность разработать такой ключ сегмента, который соответствует требованиям всех возможных запросов к данным. Выбирайте правила сегментирования так, чтобы они соответствовали самым часто выполняемым запросам, а при необходимости создайте дополнительные таблицы индексов для тех запросов, которые получают данные с использованием условий на основе других атрибутов, не входящих в ключ сегмента. Дополнительные сведения см. в статье [Index Table pattern](index-table.md) (Шаблон таблицы индексов).

- Запросы, которые обращаются только к одному сегменту, выполняются более эффективно, чем те, которые получают данные из нескольких сегментов. Поэтому не следует реализовывать системы сегментирования, вынуждающие приложения выполнять большое число запросов к данным, распределенным между несколькими сегментами. Помните, что один сегмент может содержать данные для нескольких типов сущностей. Также можно попробовать денормализовать данные, чтобы хранить в одном сегменте связанные сущности, которые часто запрашиваются вместе (например, данные о клиентах и о размещенных ими заказах). Это позволит снизить число отдельных операций считывания, выполняемых приложением.

    >  Если сущность, размещенная в одном сегменте, ссылается на сущность из другого сегмента, включите ключ сегмента для второй сущности в схему данных первой сущности. Это может повысить производительность запросов, которые обращаются к связанным данным из нескольких сегментов.

- Если приложение должно выполнять запросы к данным из нескольких сегментов, получить такие данных можно с использованием параллельных задач. Например, это применимо для размноженных запросов, где данные из нескольких сегментов извлекаются параллельно и затем объединяются в единый результат. Но такой подход неизбежно усложняет логику доступа к данным.

- Для многих приложений большое число мелких сегментов может оказаться более эффективной схемой, чем небольшое число больших сегментов. Это предоставляет больше возможностей для балансировки нагрузки. Это будет полезно и в том случае, если ожидается перенос сегментов из одного физического расположения в другое. Перемещение сегмента малого размера выполняется быстрее, чем перемещение большого сегмента.

- Убедитесь, что каждый узел сегмента имеет достаточно ресурсов для масштабируемости по таким параметрам, как объем данных и пропускная способность. Дополнительные сведения см. в разделе с требованиями к проектированию секций для обеспечения масштабируемости в [руководстве по секционированию данных](https://msdn.microsoft.com/library/dn589795.aspx).

- Возможно, стоит реплицировать ссылочные данные во все сегменты. Если операции получения сегментированных данных в том же запросе обращаются к статической или медленно изменяющейся информации, добавьте эту информацию в сегмент. Тогда приложение сможет быстро получать все данные по этому запросу без дополнительного обращения к отдельному хранилищу данных.

    >  Если изменятся ссылочные данные, хранимые в нескольких сегментах, система должна синхронизировать эти изменения между всеми сегментами. При такой синхронизации может возникать некоторая несогласованность. В таком случае приложение следует разрабатывать так, чтобы оно смогло справиться с ней.

- Целостность и согласованность ссылочных данных между сегментами поддерживать трудно, поэтому старайтесь как можно меньше использовать операции, которые влияют на данные в нескольких сегментах. Если приложению необходимо изменять данные в нескольких сегментах, отдельно оцените необходимость полной согласованности данных. Обычно стандартный подход в облаке заключается в реализации окончательной согласованности. Данные в каждой секции обновляются отдельно, а логика приложения отвечает как за успешное завершение обновлений, так и за обработку несоответствий, которые могут возникнуть при запросах к данным на фоне выполнения операции по согласованию. Сведения о реализации согласованности в конечном счете см. в [руководстве по обеспечению согласованности данных](https://msdn.microsoft.com/library/dn589800.aspx).

- Настройка большого числа сегментов и управление ими могут представлять существенные сложности. Задачи мониторинга, резервного копирования, проверки согласованности, ведения журналов и аудита придется выполнять сразу для нескольких сегментов и серверов, возможно даже размещенных в нескольких расположениях. Эти задачи обычно можно реализовать с помощью скриптов или других решений по автоматизации, но не всегда удастся полностью устранить необходимость в дополнительных операциях по администрированию.

- Сегменты могут быть привязаны к расположению, чтобы хранящиеся в них данные находились близко к тем экземплярам приложения, которые используют эти данные. Такой подход может значительно повысить производительность, но он также повысит требования к операциям, выполняемым для нескольких сегментов в разных расположениях.

## <a name="when-to-use-this-pattern"></a>Когда следует использовать этот шаблон

Используйте этот шаблон, если ожидаете, что требования к хранилищу данных могут превысить ресурсы, доступные для отдельного узла хранилища, или если хотите повысить производительность, избавляясь от конфликта ресурсов в хранилище данных.

>  Основная цель сегментирования — повысить производительность и масштабируемость системы. В качестве "побочного эффекта" вы можете также повысить доступность благодаря правильному распределению данных между сегментами. Сбой в одной секции не обязательно помешает приложению получать данные из других секций, а операции по обслуживанию или восстановлению данных в одной или нескольких секциях можно будет выполнять, не отключая для приложения доступ ко всем данным. Дополнительные сведения см. в [руководстве по секционированию данных](https://msdn.microsoft.com/library/dn589795.aspx).

## <a name="example"></a>Пример

В следующем примере на языке C# набор баз данных SQL Server используется в качестве сегментов. Каждая база данных содержит подмножество данных, используемых приложением. Приложение извлекает данные, распределенные между сегментами, используя собственную логику сегментирования (это пример размноженного запроса). Подробные сведения о данных, расположенных в каждом сегменте, возвращает метод с именем `GetShards`. Он возвращает перечисляемый список объектов `ShardInformation`, где тип `ShardInformation` содержит идентификатор для каждого сегмента и строку подключения к серверу SQL Server, которую приложение будет использовать для подключения к этому сегменту (строки подключения не показаны в примере кода).

```csharp
private IEnumerable<ShardInformation> GetShards()
{
  // This retrieves the connection information from a shard store
  // (commonly a root database).
  return new[]
  {
    new ShardInformation
    {
      Id = 1,
      ConnectionString = ...
    },
    new ShardInformation
    {
      Id = 2,
      ConnectionString = ...
    }
  };
}
```

В следующем примере кода показано, как приложение использует список объектов `ShardInformation` для выполнения запроса, который извлекает данные из каждого сегмента в параллельном режиме. Подробные сведения о запросе не отображаются, но в этом примере извлекаемые данные содержат строку с информацией, например именем клиента, если в сегментах есть подробные сведения о клиенте. Результаты объединяются в коллекцию `ConcurrentBag` для последующей обработки в приложении.

```csharp
// Retrieve the shards as a ShardInformation[] instance.
var shards = GetShards();

var results = new ConcurrentBag<string>();

// Execute the query against each shard in the shard list.
// This list would typically be retrieved from configuration
// or from a root/master shard store.
Parallel.ForEach(shards, shard =>
{
  // NOTE: Transient fault handling isn't included,
  // but should be incorporated when used in a real world application.
  using (var con = new SqlConnection(shard.ConnectionString))
  {
    con.Open();
    var cmd = new SqlCommand("SELECT ... FROM ...", con);

    Trace.TraceInformation("Executing command against shard: {0}", shard.Id);

    var reader = cmd.ExecuteReader();
    // Read the results in to a thread-safe data structure.
    while (reader.Read())
    {
      results.Add(reader.GetString(0));
    }
  }
});

Trace.TraceInformation("Fanout query complete - Record Count: {0}",
                        results.Count);
```

## <a name="related-patterns-and-guidance"></a>Связанные шаблоны и рекомендации

При реализации этого шаблона следует принять во внимание следующие шаблоны и рекомендации.
- [Data Consistency Primer](https://msdn.microsoft.com/library/dn589800.aspx) (Руководство по обеспечению согласованности данных). Возможно, вам потребуется поддерживать согласованность данных, распределенных между разными сегментами. Здесь кратко описаны проблемы, связанные с поддержанием согласованности распределенных данных, и описываются преимущества и недостатки различных моделей согласованности.
- [Data Partitioning Guidance](https://msdn.microsoft.com/library/dn589795.aspx) (Руководство по секционированию данных). Сегментирование хранилища данных может привести к некоторым сопутствующим проблемам. Здесь описаны проблемы, связанные с секционированием хранилища данных в облаке, которое позволяет повысить масштабируемость, сократить число конфликтов и оптимизировать производительность.
- [Index Table pattern](index-table.md) (Шаблон таблицы индексов). Иногда даже правильно выбранный ключ сегмента не позволяет выполнить все потребности запросов. Этот шаблон позволяет приложению быстро получать данные из хранилища большого объема благодаря использованию ключа, не совпадающего c ключом сегмента.
- [Materialized View Pattern](materialized-view.md) (Шаблон материализованного представления). Поддержать производительность некоторых запросов помогут материализованные представления, которые выполняют объединение и анализ данных. Это особенно полезно, если сводные данные основываются на информации, распределенной между несколькими сегментами. Здесь описано, как создать и заполнить такие представления.
- [Shard Lessons](http://www.addsimplicity.com/adding_simplicity_an_engi/2008/08/shard-lessons.html) (Уроки о сегментах) в блоге Adding Simplicity.
- [Database Sharding](http://dbshards.com/database-sharding/) (Сегментирование баз данных) на сайте CodeFutures.
- [Scalability Strategies Primer: Database Sharding](http://blog.maxindelicato.com/2008/12/scalability-strategies-primer-database-sharding.html) (Основные сведения о стратегиях масштабируемости. Сегментирование баз данных) в блоге Макса Инделикато (Max Indelicato).
- [Building Scalable Databases: Pros and Cons of Various Database Sharding Schemes](http://www.25hoursaday.com/weblog/2009/01/16/BuildingScalableDatabasesProsAndConsOfVariousDatabaseShardingSchemes.aspx) (Создание масштабируемых баз данных. Преимущества и недостатки разных схем сегментирования базы данных) в блоге Дэйра Обасанджо (Dare Obasanjo).