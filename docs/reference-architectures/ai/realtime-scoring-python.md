---
title: Оценка в реальном времени моделей Python scikit-learn и глубокого обучения в Azure
description: Эта эталонная архитектура демонстрирует, как развернуть модель Python в качестве веб-службы в Azure для прогнозирования в реальном времени.
author: njray
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 860eb83f248c2a9f2a96065c6c4cdd02715e7ce1
ms.sourcegitcommit: cc234a522b7fc35af3bcacdc044c2e2b529e54ed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51347774"
---
# <a name="real-time-scoring-of-python-scikit-learn-and-deep-learning-models-on-azure"></a>Оценка в реальном времени моделей Python scikit-learn и глубокого обучения в Azure

Эта эталонная архитектура демонстрирует, как развернуть модель Python в качестве веб-службы в Azure для прогнозирования в реальном времени. Рассматриваются два сценария: развертывание обычных моделей Python и развертывание моделей глубокого обучения с определенными требованиями. Оба сценария используют показанную на рисунке архитектуру.

Два примера реализации этой архитектуры можно найти на сайте GitHub: один для [обычных моделей Python][github-python] и один для [моделей глубокого обучения][github-dl].

![](./_images/python-model-architecture.png)

## <a name="scenarios"></a>Сценарии

Примеры реализации демонстрируют два сценария использования этой архитектуры.

**Сценарий 1: сопоставление часто задаваемых вопросов**. Этот сценарий демонстрирует, как развернуть модель сопоставления часто задаваемых вопросов в качестве веб-службы для прогнозирования вопросов пользователей. В этом сценарии входными данными на схеме архитектуры служат текстовые строки, содержащие вопросы пользователей, сопоставляемые со списком часто задаваемых вопросов. В этом сценарии используется библиотека машинного обучения [scikit-learn][scikit] для Python, но также поддерживаются любые сценарии работы с моделями Python для прогнозирования в реальном времени.

В этом сценарии используется набор данных вопросов Stack Overflow , которые содержат исходные вопросы, помеченные тегами JavaScript, дублирующиеся вопросы и ответы на них. В сценарии конвейер scikit-learn обучается прогнозировать возможность соответствия дублирующихся и исходных вопросов. Прогнозирование осуществляется в реальном времени с использованием конечной точки REST API.

Архитектура предполагает такой алгоритм работы:

1.  Клиент отправляет запрос HTTP POST с закодированными данными вопроса.

2.  Приложение Flask извлекает вопрос из запроса.

3.  Вопрос отправляется в модель конвейера scikit-learn для извлечения признаков и оценки.

4.  Соответствующие часто задаваемые вопросы и их оценки преобразуются в объект JSON и возвращаются клиенту.

Ниже приведен пример приложения, использующего полученные результаты:

![](./_images/python-faq-matches.png)

**Сценарий 2: классификация изображений.** Этот сценарий демонстрирует развертывание модели сверточной нейронной сети (CNN) в качестве веб-службы для прогнозной обработки изображений. В этом сценарии входными данными на схеме архитектуры служат файлы изображений. Сети CNN весьма эффективны для выполнения таких задач компьютерного зрения, как классификация изображений и обнаружение объектов. В этом сценарии используются платформы TensorFlow, Keras (с серверной частью TensorFlow) и PyTorch. Но также поддерживаются сценарии работы с моделью глубокого обучения для прогнозирования в реальном времени.

В этом сценарии используется модель ResNet-152, обученная с помощью набора данных ImageNet -1K (1000 классов), для прогнозирования категории изображения (см. рисунок ниже). Прогнозирование осуществляется в реальном времени с использованием конечной точки REST API.

![](./_images/python-example-predictions.png)

Модель глубокого обучения предполагает такой алгоритм работы:

1.  Клиент отправляет запрос HTTP POST с закодированными данными изображения.

2.  Приложение Flask извлекает изображение из запроса.

3.  Изображение предварительно обрабатывается и отправляется в модель для оценки.

4.  Результаты оценки преобразуются в объект JSON и возвращаются клиенту.

## <a name="architecture"></a>Архитектура

Архитектура состоит из следующих компонентов.

**[Виртуальная машина][vm]**. Виртуальная машина показана в качестве примера локального или облачного устройства, способного отправлять запрос HTTP.

**[Служба Azure Kubernetes][aks]** (AKS) используется для развертывания приложения в кластере Kubernetes. AKS упрощает развертывание и использование Kubernetes. Кластер можно настроить с помощью виртуальных машин с обычным ЦП для обычных моделей Python или виртуальных машин с графическим процессором для моделей глубокого обучения.

**[Подсистема балансировки нагрузки][lb]**. Подсистема балансировки нагрузки, предоставляемая AKS, используется для взаимодействия с внешними приложениями. Трафик из подсистемы балансировки нагрузки направляется в модули pod серверной части.

**[Центр Docker][docker]** используется для хранения образа Docker, который развертывается в кластере Kubernetes. Центр Docker выбран для этой архитектуры, так как он прост в использовании и является репозиторием образов по умолчанию для пользователей Docker. [Реестр контейнеров Azure][acr] также можно использовать в этой архитектуре.

## <a name="performance-considerations"></a>Рекомендации по производительности

Пропускная способность является важным параметром для архитектур оценки в реальном времени. Принято считать, что для рабочей нагрузки обычных моделей Python достаточно обычных ЦП. 

Но для рабочих нагрузок моделей глубокого обучения, когда скорость имеет решающее значение, графические процессоры обеспечивают более высокую [производительность][gpus-vs-cpus] по сравнению с обычными ЦП. Чтобы добиться производительности графического процессора, необходим кластер с большим количеством обычных ЦП.

Обычные ЦП можно использовать в любом сценарии для этой архитектуры, но для моделей глубокого обучения графические процессоры обеспечивают гораздо более высокие значения пропускной способности по сравнению с кластером с обычными ЦП в рамках одного бюджета. Для AKS реализована поддержка GPU, что является одним из преимуществ использования этой службы в описанной архитектуре. Кроме того, модели глубокого обучения обычно имеют большое количество параметров. Использование GPU устраняет конкуренцию за ресурсы между моделью и веб-службой, что является проблемой при развертывании с обычными ЦП.

## <a name="scalability-considerations"></a>Вопросы масштабируемости

Для обычных моделей Python (кластер AKS содержит подготовленные виртуальные машины с обычными ЦП) особое внимание следует уделить [масштабированию количества модулей pod][manually-scale-pods]. Целью является максимальное использование возможностей кластера. Масштабирование зависит от запросов ЦП, а для модулей pod устанавливаются ограничения. Kubernetes также поддерживает [автомасштабирование][autoscale-pods] модулей pod, регулируя количество модулей в развертывании в зависимости от использования ЦП или других выбранных метрик. [Служба автомасштабирования кластеров][autoscaler] (доступна в предварительной версии) позволяет масштабировать узлы агентов на основе ожидающих модулей pod.

Ограничения на ресурсы для сценариев глубокого обучения, в которых используются виртуальные машины с графическими процессорами, предусматривают назначение каждому модулю pod одного графического процессора. В зависимости от типа используемой виртуальной машины, вам нужно выполнить [масштабирование узлов кластера][scale-cluster] в соответствии с требованиями службы. Это делается с помощью Azure CLI и kubectl.

## <a name="monitoring-and-logging-considerations"></a>Мониторинг и ведение журнала запросов

### <a name="aks-monitoring"></a>Мониторинг AKS

Чтобы отслеживать производительность AKS, используйте службу [Azure Monitor для контейнеров][monitor-containers]. Она позволяет собирать данные метрик памяти и процессора из контроллеров, узлов и контейнеров, доступных в Kubernetes, с помощью API метрик.

При развертывании приложения отслеживайте кластер AKS, чтобы убедиться в том, что он работает должным образом, все узлы исправны, а модули pod запущены. Хотя можно использовать средство командной строки [kubectl][kubectl] для получения сведений о состоянии модулей pod, доступная в Kubernetes веб-панель мониторинга позволяет выполнять базовый мониторинг состояния кластера и управлять им.

![](./_images/python-kubernetes-dashboard.png)

Чтобы проверить общее состояние кластера и узлов, перейдите в раздел **Узлы** панели мониторинга Kubernetes. Если узел неактивен или произошел сбой, можно просмотреть журналы ошибок на этой странице. В разделах **Объекты pod** и **Развертывания** содержатся сведения о количестве модулей и состоянии развертывания.

### <a name="aks-logs"></a>Журналы AKS 

Все потоки stdout и stderr в AKS автоматически регистрируются в журналах модулей pod, содержащихся в кластере. Используйте kubectl, чтобы просмотреть их, а также журналы и события на уровне узла. Дополнительные сведения см. в шагах по развертыванию.

Используйте [Azure Monitor для контейнеров][monitor-containers] для сбора метрик и журналов с помощью контейнерной версии агента Log Analytics для Linux, который хранится в рабочей области Log Analytics.

## <a name="security-considerations"></a>Вопросы безопасности

Благодаря [центру безопасности Azure][security-center] можно получить полное представление о состоянии безопасности ваших ресурсов Azure. Центр безопасности отслеживает потенциальные проблемы безопасности, а также дает полное представление о работоспособности системы безопасности развертывания, хотя и не отслеживает состояние узлов агентов AKS. Центр безопасности настраивается на уровне подписки Azure. Включите сбор данных безопасности, как описано в кратком руководстве [Переход подписки Azure на ценовую категорию центра безопасности "Стандартный"][get-started]. Когда сбор данных включен, центр безопасности автоматически проверяет все виртуальные машины, созданные для этой подписки.

**Эксплуатация**. Чтобы входить в кластер AKS с помощью маркера проверки подлинности Azure Active Directory (Azure AD), настройте в AKS [проверку подлинности пользователей][aad-auth] с помощью Azure AD. Администраторы кластера также могут настроить управление доступом на основе ролей (RBAC) для Kubernetes в зависимости от членства в группе каталогов или удостоверения пользователей.

Контролируйте доступ к развертываемым ресурсам Azure с помощью [RBAC][rbac]. RBAC позволяет назначить роли авторизации участникам команды DevOps. Пользователю можно назначить несколько ролей. Можно также создать пользовательские роли, чтобы выборочно настроить [разрешения].

**HTTPS**. Из соображений безопасности следует принудительно применять в приложении протокол HTTPS, перенаправляя все HTTP-запросы. Используйте [контроллер входящего трафика][ingress-controller], чтобы развернуть обратный прокси-сервер, который завершает SSL и перенаправляет HTTP-запросы. Дополнительные сведения см. в статье [Создание контроллера входящего трафика HTTPS в Службе Azure Kubernetes (AKS)][https-ingress].

**Проверка подлинности**. Это решение не ограничивает доступ к конечным точкам. Чтобы развернуть эту архитектуру в среде предприятия, защитите конечные точки с помощью ключей API и добавьте в клиентское приложение предпочитаемую процедуру аутентификации пользователей.

**Реестр контейнеров**. Это решение использует общедоступный реестр для хранения образа Docker. Код, от которого зависит приложение, и модель содержатся в этом образе. Корпоративные приложения должны использовать частный реестр, обеспечивающий защиту от выполнения вредоносного кода и компрометации хранящейся в контейнере информации.

**Защита от атак DDoS.** Рассмотрите возможность подключения [Защиты от атак DDoS ценовой категории "Стандартный"][ddos]. Хотя платформа Azure и обеспечивает базовую защиту от атак DDoS, возможности Защиты Azure от атак DDoS ценовой категории "Стандартный" по снижению рисков атак специально оптимизированы для виртуальных сетевых ресурсов Azure.

**Ведение журналов.** Следуйте рекомендациям при сохранении данных журнала, например, выполняйте очистку пользовательских паролей и других данных, которые могут использоваться в мошеннических целях.

## <a name="deployment"></a>Развертывание

Для развертывания этой эталонной архитектуры, выполните действия, описанные в репозитории GitHub: 

  - [Обычные модели Python][github-python];
  - [Модели машинного обучения][github-dl].

[aad-auth]: /azure/aks/aad-integration
[acr]: /azure/container-registry/
[something]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/
[aks]: /azure/aks/intro-kubernetes
[autoscaler]: /azure/aks/autoscaler
[autoscale-pods]: /azure/aks/tutorial-kubernetes-scale#autoscale-pods
[azcopy]: /azure/storage/common/storage-use-azcopy-linux
[ddos]: /azure/virtual-network/ddos-protection-overview
[docker]: https://hub.docker.com/
[get-started]: /azure/security-center/security-center-get-started
[github-python]: https://github.com/Azure/MLAKSDeployment
[github-dl]: https://github.com/Microsoft/AKSDeploymentTutorial
[gpus-vs-cpus]: https://azure.microsoft.com/en-us/blog/gpus-vs-cpus-for-deployment-of-deep-learning-models/
[https-ingress]: /azure/aks/ingress-tls
[ingress-controller]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[kubectl]: https://kubernetes.io/docs/tasks/tools/install-kubectl/
[lb]: /azure/load-balancer/load-balancer-overview
[manually-scale-pods]: /azure/aks/tutorial-kubernetes-scale#manually-scale-pods
[monitor-containers]: /azure/monitoring/monitoring-container-insights-overview
[разрешения]: /azure/aks/concepts-identity
[rbac]: /azure/active-directory/role-based-access-control-what-is
[scale-cluster]: /azure/aks/scale-cluster
[scikit]: https://pypi.org/project/scikit-learn/
[security-center]: /azure/security-center/security-center-intro
[vm]: /azure/virtual-machines/
