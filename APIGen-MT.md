# Технический обзор статьи APIGen-MT: Агентный конвейер для генерации многоходовых данных через симулированное взаимодействие агента и человека

Представленная статья от исследовательской группы Salesforce AI Research вносит значительный вклад в решение проблемы создания высококачественных данных для обучения интеллектуальных агентов многоходовому взаимодействию. Авторы разработали двухфазовый фреймворк APIGen-MT, который позволяет генерировать верифицируемые и разнообразные наборы данных для многоходовых агентных диалогов.

## Проблематика и мотивация исследования

Авторы статьи выделяют несколько ключевых проблем в области разработки интеллектуальных агентов:

- Существующие языковые модели испытывают трудности в многоходовых взаимодействиях, особенно при выполнении сложных функциональных вызовов, отслеживании долгосрочных зависимостей и запросе недостающей информации[2](https://arxiv.org/abs/2504.03601)[12](https://arxiv.org/abs/2402.14762).
    
- Наблюдается острая нехватка высококачественных обучающих данных, отражающих реалистичную динамику взаимодействия человека и агента[4](https://arxiv.org/html/2504.03601v3).
    
- Ручной сбор и разметка таких данных требует значительных временных и финансовых затрат, особенно для доменно-специфичных приложений[2](https://arxiv.org/abs/2504.03601).
    

Существующие подходы в основном фокусировались на одноходовых взаимодействиях (например, APIGen[6](https://arxiv.org/abs/2406.18518)) или не учитывали аспекты реалистичного взаимодействия человека и агента[10](https://arxiv.org/abs/2401.15843). Предлагаемый авторами метод APIGen-MT призван преодолеть эти ограничения.

## Математическая формализация многоходового взаимодействия

Авторы формализуют многоходовое взаимодействие как частично наблюдаемый марковский процесс принятия решений (POMDP), определяемый кортежем (𝒰,𝒮,𝒜,𝒪,𝒯,R)(𝒰, 𝒮, 𝒜, 𝒪, 𝒯, ℛ)(U,S,A,O,T,R), где:

- 𝒰𝒰U - пространство инструкций, содержащее возможные намерения пользователя
    
- 𝒮𝒮S - пространство состояний среды и истории беседы
    
- 𝒜={tool_call,response}𝒜 = \{tool\_call, response\}A={tool_call,response} - пространство действий, доступных помощнику
    
- 𝒪=𝒪E∪𝒪H𝒪 = 𝒪_E ∪ 𝒪_HO=OE∪OH - пространство наблюдений из среды 𝒪E𝒪_EOE и от человека 𝒪H𝒪_HOH
    
- 𝒯:𝒮×𝒜→𝒮×𝒪𝒯: 𝒮 × 𝒜 → 𝒮 × 𝒪T:S×A→S×O - функция перехода
    
- RℛR - функция вознаграждения, оценивающая успех взаимодействия[4](https://arxiv.org/html/2504.03601v3)[11](https://apigen-mt.github.io/)
    

На каждом шаге ttt ассистент прогнозирует действие at∈𝒜a_t ∈ 𝒜at∈A на основе истории взаимодействия. Когда ata_tat является функциональным вызовом tool_calltool\_calltool_call, происходит переход состояния (stE,tool_call)→(st+1E,oE)(s^E_t, tool\_call) → (s^E_{t+1}, o_E)(stE,tool_call)→(st+1E,oE), где oE∈𝒪Eo_E ∈ 𝒪_EoE∈OE - результат выполнения инструмента. При ответе человеку responseresponseresponse происходит переход (stH,response)→(st+1H,oH)(s^H_t, response) → (s^H_{t+1}, o_H)(stH,response)→(st+1H,oH), где oH∈𝒪Ho_H ∈ 𝒪_HoH∈OH - следующее сообщение человека[4](https://arxiv.org/html/2504.03601v3).

Целью ассистента является максимизация вознаграждения R(Δ𝒮E,a)ℛ(Δ𝒮_E, a)R(ΔSE,a), рассчитываемого на основе совокупного изменения состояния среды и последовательности ответов агента[4](https://arxiv.org/html/2504.03601v3).

## Методология APIGen-MT: двухфазовый подход

Ключевой инновацией работы является двухфазовый фреймворк для генерации проверяемых многоходовых данных:

## Фаза 1: Конфигурация задачи и генерация эталонных решений

В этой фазе создаются детализированные "чертежи" задач, включающие:

- Инструкцию пользователя qqq
    
- Последовательность верифицируемых эталонных действий agta_{gt}agt
    
- Ожидаемые финальные выходные данные ogto_{gt}ogt[2](https://arxiv.org/abs/2504.03601)[4](https://arxiv.org/html/2504.03601v3)
    

Процесс включает следующие этапы:

1. **Подготовка контекста**: сбор информации о доступных API, правилах домена и справочных данных.
    
2. **LLM-генератор данных**: использование языковой модели для создания начальных конфигураций задач.
    
3. **Проверка формата и исполняемости**: техническая валидация, включающая проверку структурной корректности сгенерированных действий и их выполнимости в симулированной среде.
    
4. **Комитет рецензентов**: семантическая оценка несколькими LLM-рецензентами с использованием мажоритарного голосования для достижения более стабильной оценки[4](https://arxiv.org/html/2504.03601v3).
    
5. **Генерация обратной связи и улучшение**: если задача не проходит валидацию, генератор обратной связи агрегирует причины неудачи и создает план улучшения для следующей итерации[4](https://arxiv.org/html/2504.03601v3)[5](https://huggingface.co/datasets/Salesforce/APIGen-MT-5k).
    

## Фаза 2: Сбор траекторий взаимодействия

На основе проверенных конфигураций задач из Фазы 1 генерируются реалистичные многоходовые взаимодействия между:

- Симулированным пользователем (основанным на LLM)
    
- Тестовым агентом, работающим в исполняемой среде[4](https://arxiv.org/html/2504.03601v3)
    

Симуляция создает полные траектории взаимодействия, фиксирующие диалоговые ходы, действия агента и ответы среды. Каждая траектория проверяется путем сравнения результата с эталонными действиями и ожидаемыми выходными данными из Фазы 1. Только те траектории, которые верифицируемо достигают цели задачи, принимаются в набор данных[2](https://arxiv.org/abs/2504.03601)[5](https://huggingface.co/datasets/Salesforce/APIGen-MT-5k).

## Реализация на примере τ-bench

Авторы детально описывают реализацию APIGen-MT на примере τ-bench, включая:

## Моделирование графа зависимостей API

API представлены как направленный граф, где узлы - это API, а рёбра - зависимости между ними. Ребро существует от API AAA к API BBB, если входные аргументы BBB могут зависеть от выходных данных AAA и совместное использование этой пары инструментов разрешено доменными политиками[4](https://arxiv.org/html/2504.03601v3).

## Специализированные сэмплеры контекста

Для обеспечения разнообразия и реалистичности задач используются:

- **API Sampler**: различает API для исследования состояния ('read') и изменяющие состояние ('write')
    
- **Policy Sampler**: извлекает политики и правила из каждого домена τ-bench
    
- **Domain Data Sampler**: использует реалистичные доменные данные с метаданными
    
- **Persona Sampler**: включает описания пользовательских персон для обеспечения реализма
    
- **Example Sampler**: предоставляет примеры хорошо сформированных задач[4](https://arxiv.org/html/2504.03601v3)
    

## Многоступенчатая валидация

Авторы реализуют трехэтапный процесс валидации:

1. **Валидация действий**:
    
    - Проверка формата
        
    - Проверка исполнения (симуляция каждого действия в среде τ-bench)
        
    - Проверка соответствия политикам (использование исполняемых юнит-тестов для проверки соответствия доменным правилам)
        
2. **Валидация согласованности**:
    
    - Оценка соответствия эталонных действий намерению пользователя
        
    - Использование комитета из нескольких LLM-судей с мажоритарным голосованием
        
3. **Итоговая семантическая проверка**:
    
    - Консолидация обратной связи от комитета
        
    - Для задач, не прошедших проверку, запуск механизма обратной связи[4](https://arxiv.org/html/2504.03601v3)
        

## Обратная рекомбинация задач

Для создания более сложных задач авторы разработали технику Reverse Task Recombination, которая:

1. Выбирает несколько простых, проверенных задач
    
2. Объединяет их действия и ожидаемые результаты
    
3. Повторно проверяет соответствие политикам
    
4. Синтезирует новую комплексную инструкцию
    
5. Повторно проводит семантическую валидацию[4](https://arxiv.org/html/2504.03601v3)
    

## Стабилизация симулированного человека

Для поддержания стабильности и достоверности симулированного человека применяется:

- Стратегия Best-of-N (N=4) для выборки ответов
    
- Механизм самокритики для ответов LLM человека, позволяющий более точно придерживаться инструкции задачи[4](https://arxiv.org/html/2504.03601v3)
    

## Эксперименты и результаты

## Детали обучения

Авторы выполняют отфильтрованное поведенческое клонирование (Behavioral Cloning) с использованием собранных траекторий на моделях Llama 3.1/3.2 Instruct и Qwen 2.5 Instruct. Обучение проводится с использованием библиотеки LLama-Factory, DeepSpeed ZeRO stage 3, Flash Attention 2 в точности bfloat16 с оптимизатором AdamW[4](https://arxiv.org/html/2504.03601v3).

## Результаты на BFCL v3

Модели xLAM-2-fc-r демонстрируют исключительную производительность:

- xLAM-2-70b-fc-r и xLAM-2-32b-fc-r занимают первые 2 позиции в рейтинге с общей точностью 78,19% и 75,83% соответственно
    
- Наибольшее преимущество в многоходовых сценариях: xLAM-2-70b-fc-r достигает 75,12% точности в многоходовых задачах
    
- Даже меньшие модели показывают выдающиеся результаты: xLAM-2-8b-fc-r (69,25%), xLAM-2-3b-fc-r (56,00%) и xLAM-2-1b-fc-r (43,12%)
    
- Все модели существенно превосходят o1 (36%) и gpt-4o в режиме вызова функций (41%)[4](https://arxiv.org/html/2504.03601v3)
    

## Результаты на τ-bench

Модели также демонстрируют превосходные результаты на τ-bench:

- xLAM-2-70b-fc-r достигает общего показателя успешности 56,2%, что значительно превосходит базовую модель Llama 3.1 70B Instruct (38,2%) и другие модели с открытым исходным кодом, такие как DeepSeek v3 (40,6%)
    
- Модель превосходит проприетарные модели, такие как gpt-4o (52,9%)
    
- Меньшие модели (xLAM-2-32b-fc-r и xLAM-2-8b-fc-r) также показывают примечательную производительность (54,6% и 46,7% соответственно)[4](https://arxiv.org/html/2504.03601v3)
    

## Анализ поведения моделей

## Стабильность и согласованность

Авторы построили кривые pass^k на τ-bench, демонстрирующие вероятность успешного выполнения всех k независимых попыток задачи:

- С увеличением k наблюдается меньшее падение успешности для моделей авторов
    
- В более сложном авиационном домене xLAM-2-70b-fc-r имеет более высокий показатель pass^5, чем Claude, несмотря на немного более низкий показатель pass^1[4](https://arxiv.org/html/2504.03601v3)
    

Применение симуляции пользователя Best-of-N (BoN) повышает коэффициент успешности и снижает дисперсию результатов, что указывает на более стабильную оценку[4](https://arxiv.org/html/2504.03601v3).

## Углубленный анализ по категориям задач

При категоризации задач на "короткие", "средние" и "длинные" в зависимости от количества ходов:

- В категории "длинных" задач коэффициент успеха для xLAM-2-70b-fc-r значительно выше, чем у gpt-4o, но отстает от Claude
    
- xLAM-2-70b-fc-r находится на уровне gpt-4o по эффективности, но требует больше взаимодействий по сравнению с Claude для полного понимания намерения[4](https://arxiv.org/html/2504.03601v3)
    

## Статистика собранных данных

Авторы представляют статистику собранных данных:

- Коэффициент успеха конфигурации задач (Фаза 1): 70%
    
- Коэффициент успеха без агентной обратной связи: 28%
    
- Коэффициент успеха симуляции траектории (Фаза 2): 67%
    
- Всего проверенных траекторий: 3 820
    
- Минимальное количество ходов на траекторию: 1
    
- Максимальное количество ходов на траекторию: 29
    
- Среднее количество вызовов инструментов на траекторию: 7
    
- Среднее количество ходов пользователя на траекторию: 6[4](https://arxiv.org/html/2504.03601v3)
    

## Выводы и ограничения

## Основные достижения

- APIGen-MT представляет собой эффективный двухфазовый подход для генерации качественных многоходовых данных через симуляцию взаимодействия человека и агента
    
- Разделение создания "чертежей" задач от симуляции диалоговых траекторий обеспечивает как структурную корректность, так и естественную динамику диалога
    
- Модели, обученные на синтетических данных, превосходят существующие базовые линии, даже при меньших размерах моделей[2](https://arxiv.org/abs/2504.03601)[4](https://arxiv.org/html/2504.03601v3)
    

## Ограничения и направления будущих исследований

1. Несмотря на механизмы выборки Best-of-N и самокритики, некоторая стохастичность в поведении симулированного человека сохраняется
    
2. Текущий подход отбрасывает неудачные траектории во второй фазе, хотя эти случаи могли бы предоставить ценные контрастные сигналы для обучения
    
3. Многоступенчатый процесс валидации, хоть и эффективен, требует существенных вычислительных ресурсов
    
4. Перспективными направлениями являются расширение подхода на дополнительные домены и включение самосовершенствования через обучение с подкреплением[4](https://arxiv.org/html/2504.03601v3)
    

## Заключение

Работа APIGen-MT представляет собой значительный прогресс в области генерации данных для многоходовых агентных взаимодействий. Предложенный авторами подход позволяет создавать высококачественные, проверяемые данные, что является критически важным для развития интеллектуальных агентов, способных к естественному многоходовому взаимодействию с людьми. Открытый исходный код моделей xLAM-2-fc-r и синтетического набора данных способствует дальнейшему продвижению исследований в области AI-агентов и многоходового диалога.

### Citations:

1. [https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/54300082/900deca8-7bc2-4550-8f11-0e5fe025b665/paste.txt](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/54300082/900deca8-7bc2-4550-8f11-0e5fe025b665/paste.txt)
2. [https://arxiv.org/abs/2504.03601](https://arxiv.org/abs/2504.03601)
3. [https://flycode.ru/%D0%B7%D0%B0%D0%BF%D1%83%D1%81%D0%BA-%D0%BC%D0%BE%D0%B4%D0%B5%D0%BB%D0%B5%D0%B9-apigen-mt-%D0%B8-xlam-2-fc-r-%D0%B4%D0%BB%D1%8F-%D0%BE%D0%B1%D1%83%D1%87%D0%B5%D0%BD%D0%B8%D1%8F-%D0%B0%D0%B3%D0%B5-ai/?query-15-page=5&cst](https://flycode.ru/%D0%B7%D0%B0%D0%BF%D1%83%D1%81%D0%BA-%D0%BC%D0%BE%D0%B4%D0%B5%D0%BB%D0%B5%D0%B9-apigen-mt-%D0%B8-xlam-2-fc-r-%D0%B4%D0%BB%D1%8F-%D0%BE%D0%B1%D1%83%D1%87%D0%B5%D0%BD%D0%B8%D1%8F-%D0%B0%D0%B3%D0%B5-ai/?query-15-page=5&cst)
4. [https://arxiv.org/html/2504.03601v3](https://arxiv.org/html/2504.03601v3)
5. [https://huggingface.co/datasets/Salesforce/APIGen-MT-5k](https://huggingface.co/datasets/Salesforce/APIGen-MT-5k)
6. [https://arxiv.org/abs/2406.18518](https://arxiv.org/abs/2406.18518)
7. [https://www.semanticscholar.org/paper/be1eee056b0412f4f2adac754c29c5e4cf1bc68b](https://www.semanticscholar.org/paper/be1eee056b0412f4f2adac754c29c5e4cf1bc68b)
8. [https://arxiv.org/abs/2306.05685](https://arxiv.org/abs/2306.05685)
9. [https://www.semanticscholar.org/paper/0ac06843cd8b14ff4ec8bf33f160540f59f6f65c](https://www.semanticscholar.org/paper/0ac06843cd8b14ff4ec8bf33f160540f59f6f65c)
10. [https://arxiv.org/abs/2401.15843](https://arxiv.org/abs/2401.15843)
11. [https://apigen-mt.github.io](https://apigen-mt.github.io/)
12. [https://arxiv.org/abs/2402.14762](https://arxiv.org/abs/2402.14762)
13. [https://arxiv.org/html/2504.03601v2](https://arxiv.org/html/2504.03601v2)
14. [https://pubmed.ncbi.nlm.nih.gov/36883458/](https://pubmed.ncbi.nlm.nih.gov/36883458/)
15. [https://huggingface.co/papers/2504.03601](https://huggingface.co/papers/2504.03601)
16. [https://www.reddit.com/r/machinelearningnews/comments/1juz7is/salesforce_ai_released_apigenmt_and_xlam2fcr/](https://www.reddit.com/r/machinelearningnews/comments/1juz7is/salesforce_ai_released_apigenmt_and_xlam2fcr/)
17. [https://www.novareviewhub.com/en/papers/681aa51c86107b49d23c6e25](https://www.novareviewhub.com/en/papers/681aa51c86107b49d23c6e25)
18. [https://x.com/_akhaliq/status/1909119292920021123](https://x.com/_akhaliq/status/1909119292920021123)
19. [https://arxiv.org/abs/2009.09025](https://arxiv.org/abs/2009.09025)
20. [https://www.semanticscholar.org/paper/f4af3fe736b616452424d50cbd47d52f0a210582](https://www.semanticscholar.org/paper/f4af3fe736b616452424d50cbd47d52f0a210582)
21. [https://arxiv.org/abs/2104.08212](https://arxiv.org/abs/2104.08212)
22. [https://www.semanticscholar.org/paper/e29202ab392606c414910d5cdaa59e5c8b69bf31](https://www.semanticscholar.org/paper/e29202ab392606c414910d5cdaa59e5c8b69bf31)
23. [https://itinai.ru/%D0%BD%D0%BE%D0%B2%D1%8B%D0%B5-%D0%BC%D0%BE%D0%B4%D0%B5%D0%BB%D0%B8-salesforce-ai-apigen-mt-%D0%B8-xlam-2-fc-r-%D0%B4%D0%BB%D1%8F-%D0%BC%D0%BD%D0%BE%D0%B3%D0%BE%D0%BF%D0%BE%D0%B2%D0%BE%D1%80%D0%BE](https://itinai.ru/%D0%BD%D0%BE%D0%B2%D1%8B%D0%B5-%D0%BC%D0%BE%D0%B4%D0%B5%D0%BB%D0%B8-salesforce-ai-apigen-mt-%D0%B8-xlam-2-fc-r-%D0%B4%D0%BB%D1%8F-%D0%BC%D0%BD%D0%BE%D0%B3%D0%BE%D0%BF%D0%BE%D0%B2%D0%BE%D1%80%D0%BE)
24. [https://arxiv.org/html/2504.03601v1](https://arxiv.org/html/2504.03601v1)
25. [https://www.chatpaper.ai/dashboard/paper/464d32fa-3db9-4bcb-8b92-868ecc15cc96](https://www.chatpaper.ai/dashboard/paper/464d32fa-3db9-4bcb-8b92-868ecc15cc96)
26. [https://x.com/sfresearch?lang=en](https://x.com/sfresearch?lang=en)
27. [https://www.chatpaper.ai/ru/papers/2025-04-07](https://www.chatpaper.ai/ru/papers/2025-04-07)
28. [https://www.salesforce.com/blog/xlam-large-action-models-v2/](https://www.salesforce.com/blog/xlam-large-action-models-v2/)
29. [https://twitter.com/SFResearch/status/1920616561406009487](https://twitter.com/SFResearch/status/1920616561406009487)
30. [https://github.com/phpstan/phpstan-src/tree/2.1.x/apigen](https://github.com/phpstan/phpstan-src/tree/2.1.x/apigen)
31. [https://www.linkedin.com/posts/asifrazzaq_salesforce-ai-released-apigen-mt-and-xlam-activity-7315619996540301313-Z-PO](https://www.linkedin.com/posts/asifrazzaq_salesforce-ai-released-apigen-mt-and-xlam-activity-7315619996540301313-Z-PO)
32. [https://www.marktechpost.com/2025/04/08/salesforce-ai-released-apigen-mt-and-xlam-2-fc-r-model-series-advancing-multi-turn-agent-training-with-verified-data-pipelines-and-scalable-llm-architectures/](https://www.marktechpost.com/2025/04/08/salesforce-ai-released-apigen-mt-and-xlam-2-fc-r-model-series-advancing-multi-turn-agent-training-with-verified-data-pipelines-and-scalable-llm-architectures/)
33. [https://flycode.ru/%D0%BA%D0%B0%D0%BA-%D0%BE%D1%82%D0%BA%D1%80%D1%8B%D1%82%D1%8C-%D1%83%D1%81%D0%BF%D0%B5%D1%88%D0%BD%D1%8B%D0%B9-%D1%81%D0%B0%D0%BB%D0%BE%D0%BD-%D0%BA%D1%80%D0%B0%D1%81%D0%BE%D1%82%D1%8B-%D0%BA%D0%B5-ai/?query-15-page=180&cst&query-8-page=7](https://flycode.ru/%D0%BA%D0%B0%D0%BA-%D0%BE%D1%82%D0%BA%D1%80%D1%8B%D1%82%D1%8C-%D1%83%D1%81%D0%BF%D0%B5%D1%88%D0%BD%D1%8B%D0%B9-%D1%81%D0%B0%D0%BB%D0%BE%D0%BD-%D0%BA%D1%80%D0%B0%D1%81%D0%BE%D1%82%D1%8B-%D0%BA%D0%B5-ai/?query-15-page=180&cst&query-8-page=7)
34. [https://www.linkedin.com/posts/mervinpraison_apigen-mt-agentic-pipeline-for-multi-turn-activity-7315283703016951809-T4IX](https://www.linkedin.com/posts/mervinpraison_apigen-mt-agentic-pipeline-for-multi-turn-activity-7315283703016951809-T4IX)
35. [https://www.xueshuxiangzi.com/downloads/2025_4_7/2504.03601.pdf](https://www.xueshuxiangzi.com/downloads/2025_4_7/2504.03601.pdf)
36. [https://github.com/SalesforceAIResearch/xLAM/blob/main/README.md](https://github.com/SalesforceAIResearch/xLAM/blob/main/README.md)
37. [https://www.novareviewhub.com/en/papers/681aa51c86107b49d23c6e25](https://www.novareviewhub.com/en/papers/681aa51c86107b49d23c6e25)
38. [https://www.linkedin.com/posts/umar-iftikhar-1b7458135_apigen-mt-agentic-pipeline-for-multi-turn-activity-7315799284862361600-cLAf](https://www.linkedin.com/posts/umar-iftikhar-1b7458135_apigen-mt-agentic-pipeline-for-multi-turn-activity-7315799284862361600-cLAf)
39. [https://papers.cool/arxiv/2504.03601](https://papers.cool/arxiv/2504.03601)
40. [https://www.aimodels.fyi/papers/arxiv/apigen-mt-agentic-pipeline-multi-turn-data](https://www.aimodels.fyi/papers/arxiv/apigen-mt-agentic-pipeline-multi-turn-data)
41. [https://paperswithcode.com/paper/apigen-mt-agentic-pipeline-for-multi-turn](https://paperswithcode.com/paper/apigen-mt-agentic-pipeline-for-multi-turn)
42. [https://nips.cc/virtual/2024/poster/95263](https://nips.cc/virtual/2024/poster/95263)
43. [https://x.com/Marktechpost/status/1909854524061499677](https://x.com/Marktechpost/status/1909854524061499677)
44. [https://huggingface.co/Salesforce/xLAM-2-3b-fc-r-gguf](https://huggingface.co/Salesforce/xLAM-2-3b-fc-r-gguf)
45. [https://neurips.cc/virtual/2024/session/108365](https://neurips.cc/virtual/2024/session/108365)
46. [https://www.getmaxim.ai/blog/apigen-mt-structured-multi-turn-training-data-for-agents/](https://www.getmaxim.ai/blog/apigen-mt-structured-multi-turn-training-data-for-agents/)
47. [https://github.com/tmgthb/Autonomous-Agents](https://github.com/tmgthb/Autonomous-Agents)
48. [https://huggingface.co/papers/2504.03601](https://huggingface.co/papers/2504.03601)
49. [https://www.aquax.com.au/salesforce-ai-released-apigen-mt-and-xlam-2-fc-r-model-series-advancing-multi-turn-agent-training-wi-1006.html](https://www.aquax.com.au/salesforce-ai-released-apigen-mt-and-xlam-2-fc-r-model-series-advancing-multi-turn-agent-training-wi-1006.html)
50. [https://www.linkedin.com/posts/vineet-moroney-6834b99_genai-salesforceai-multiturnagents-activity-7315943464976703488-O6FL](https://www.linkedin.com/posts/vineet-moroney-6834b99_genai-salesforceai-multiturnagents-activity-7315943464976703488-O6FL)
51. [https://sierra.ai/resources/research/tau-bench](https://sierra.ai/resources/research/tau-bench)
52. [https://huggingface.co/papers?q=hybrid+approaches](https://huggingface.co/papers?q=hybrid+approaches)

---

Answer from Perplexity: [pplx.ai/share](https://www.perplexity.ai/search/pplx.ai/share)