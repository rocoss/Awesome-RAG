# DataMosaic: Объяснимая и проверяемая мультимодальная аналитика данных через процесс извлечения-рассуждения-проверки

Перед основным обзором представим краткое резюме исследования. В данной статье авторы предлагают фреймворк DataMosaic, разработанный для преодоления двух ключевых ограничений больших языковых моделей (LLM) в аналитике данных: отсутствия объяснимости (непрозрачные процессы рассуждения) и проверяемости (склонность к галлюцинациям и ошибкам). Используя многоагентную архитектуру и итеративный процесс извлечения-рассуждения-проверки, DataMosaic обеспечивает прозрачный, структурированный анализ мультимодальных данных, динамически извлекая задачно-ориентированные структуры (таблицы, графы, деревья) и обеспечивая поэтапную верификацию результатов.

## Введение и постановка проблемы

В современном мире большинство информации существует в разнородных источниках – от текстовых документов и PDF до изображений и постов в социальных сетях. Эти источники содержат огромный потенциал для извлечения ценных инсайтов[2](https://arxiv.org/abs/2504.10036). Однако их анализ остается серьезной проблемой из-за разнообразия форматов, отсутствия предопределенной структуры и колоссальных объемов данных, генерируемых ежедневно[15](https://arxiv.org/html/2504.10036v1).

Мультимодальные большие языковые модели (LLM) стали мощными инструментами анализа разнотипных данных, предлагая беспрецедентные возможности для унифицированной обработки текстовой, визуальной и структурированной информации[15](https://arxiv.org/html/2504.10036v1). Подавая в мультимодальную LLM (например, GPT или DeepSeek) коллекцию данных различных форматов, пользователи могут формулировать аналитические задачи на естественном языке, не вникая в сложности обработки конкретных типов файлов. LLM выступает в роли универсального аналитика, интерпретирующего входные данные в контексте, применяющего соответствующие стратегии рассуждения и генерирующего полезные выводы[2](https://arxiv.org/abs/2504.10036).
![[Pasted image 20250509152350.png]]
Однако применение LLM для мультимодальной аналитики данных сталкивается с несколькими ключевыми проблемами:

## Ключевые ограничения LLM в аналитике данных

1. **Отсутствие объяснимости**: Мультимодальные LLM предоставляют непрозрачные процессы рассуждения при обработке различных типов данных (текст, изображения, таблицы), что затрудняет понимание, как модель пришла к своим выводам[2](https://arxiv.org/abs/2504.10036)[3](https://arxiv.org/pdf/2504.10036.pdf).
    
2. **Низкая точность**: Прямое использование LLM для анализа коллекций данных может давать неточные результаты, поскольку эти модели не оптимизированы для точных численных вычислений и специализированного анализа[2](https://arxiv.org/abs/2504.10036).
    
3. **Неполнота данных**: LLM часто не идентифицируют все необходимые информационные элементы в наборе данных или за его пределами, пропуская ключевые точки данных или генерируя "галлюцинации" – правдоподобную, но некорректную информацию[2](https://arxiv.org/abs/2504.10036).
    
4. **Несогласованность данных**: При использовании информации из разных источников возникают несоответствия в форматах, единицах измерения или противоречия, которые LLM могут не разрешать должным образом[2](https://arxiv.org/abs/2504.10036).
    
5. **Проблемы конфиденциальности**: Отправка приватных или конфиденциальных данных (персональная информация, финансовые данные) во внешние LLM создает риски утечки или неправильного использования информации[2](https://arxiv.org/abs/2504.10036).
    
6. **Низкая эффективность**: Использование крупномасштабных LLM (например, моделей с миллиардами параметров) приводит к высоким вычислительным затратам и значительным задержкам, делая их неэффективными для анализа данных в реальном времени[2](https://arxiv.org/abs/2504.10036).
    

## Требования к системам мультимодальной аналитики данных

Для эффективного решения вышеуказанных проблем авторы формулируют следующие требования к системам мультимодальной аналитики данных:

## Объяснимость

Система должна обеспечивать прозрачные и понятные объяснения, особенно через структурированное представление данных. Это требует использования явных, тщательно организованных структур данных, адаптированных к конкретным задачам, что позволяет ясно показывать, как отдельные элементы данных влияют на анализ[15](https://arxiv.org/html/2504.10036v1).

## Высокая точность

Система должна минимизировать ошибки и обеспечивать достоверность результатов. Это включает точные численные и статистические вычисления, особенно для задач, требующих детальных количественных выводов, и интеграцию доменно-специфических знаний для повышения качества анализа[15](https://arxiv.org/html/2504.10036v1).

## Полнота

Система должна гарантировать, что все релевантные точки данных идентифицированы и включены в анализ, что достигается через итеративный поиск и верификацию для предотвращения пропуска критической информации[15](https://arxiv.org/html/2504.10036v1).

## Согласованность

Система должна эффективно разрешать несоответствия между различными источниками данных, согласовывая форматы, единицы измерения и разрешая противоречия для обеспечения когерентности результатов[15](https://arxiv.org/html/2504.10036v1).

## Конфиденциальность данных

Система должна защищать чувствительную информацию, минимизируя её раскрытие внешним моделям, используя локально развертываемые решения и безопасные практики обработки данных[15](https://arxiv.org/html/2504.10036v1).

## Высокая эффективность

Система должна функционировать с минимальными вычислительными затратами и задержками, используя оптимизированные малые модели и эффективные процессы обработки масштабных данных[15](https://arxiv.org/html/2504.10036v1).

## Фреймворк DataMosaic

Для решения описанных проблем авторы предлагают DataMosaic – агентический фреймворк, разработанный для обеспечения объяснимой и проверяемой мультимодальной аналитики данных. Фреймворк основан на итеративном рабочем процессе извлечения-рассуждения-проверки и состоит из следующих ключевых модулей:

## Декомпозиция вопросов

Этот модуль разбивает сложный пользовательский вопрос $Q$ на более простые и управляемые подвопросы. Математически это представляется как:

$Q = \text{Decompose}(Q_1, Q_2, \ldots, Q_i)$

где $Q$ – исходный вопрос, а $Q_i$ – подвопросы, сфокусированные на конкретных аспектах исходного запроса[15](https://arxiv.org/html/2504.10036v1). Генерация подвопросов может зависеть от ответов на предыдущие подвопросы для оптимизации процесса анализа[15](https://arxiv.org/html/2504.10036v1).

Для декомпозиции используется LLM с соответствующим промптом, указывающим модели разбить сложный вопрос на набор подвопросов, каждый из которых должен быть решаем с помощью одношагового рассуждения[15](https://arxiv.org/html/2504.10036v1).

## Выбор структуры данных

Для каждого подвопроса $Q_i$ модуль выбирает наиболее подходящую структуру данных $S_i$ из множества возможных структур $S = {s_1, s_2, \ldots, s_n}$:

$S_i = \text{Select}(Q_i, S)$

Выбор основывается на характере вопроса, типе данных и методе анализа[15](https://arxiv.org/html/2504.10036v1). В рамках фреймворка рассматриваются структуры данных: таблицы, графы, деревья и описания на естественном языке.

## Поиск (Seek)

Модуль идентифицирует релевантные фрагменты данных $F$ из мультимодальных источников, соответствующие каждому подвопросу $Q_i$. Для этого используется векторное представление:

Для текстовых данных, разбитых на фрагменты $T = {t_1, t_2, \ldots, t_n}$:  
$v_{t_i} = \text{Encoder}(t_i), v_{t_i} \in \mathbb{R}^d$

Для изображений, преобразованных в текстовые описания $C = {c_1, c_2, \ldots, c_m}$:  
$v_{c_i} = \text{Encoder}(c_i), v_{c_i} \in \mathbb{R}^d$

Косинусное сходство между векторами вычисляется как:  
$\text{sim}(Q_i, F) = \frac{v_{Q_i} \cdot v_F}{|v_{Q_i}| |v_F|}$

Наиболее релевантные фрагменты отбираются для дальнейшей обработки[15](https://arxiv.org/html/2504.10036v1).

## Извлечение (Extraction)

Модуль преобразует отобранные фрагменты данных в структурированный формат согласно выбранной структуре:

$D_i' = \text{Extract}(Q_i, F_i, S_i)$

В зависимости от выбранной структуры применяются различные методы извлечения[15](https://arxiv.org/html/2504.10036v1):

- **Таблица**: Извлечение схемы из вопроса, определение столбцов и отношений, извлечение кортежей из фрагментов данных и их объединение в таблицу.
    
- **Граф**: Генерация схемы графа (типы узлов и ребер), извлечение триплетов и построение полного графа с узлами-сущностями, соединенными ребрами-отношениями.
    
- **Дерево**: Определение иерархических отношений, извлечение кортежей родитель-ребенок и их объединение в древовидную структуру.
    
- **Описание**: Трансформация фрагментов в краткое, релевантное текстовое описание.
    

Каждая структура данных поддерживает специфический набор операций:

- **Таблица**: create_table, add_row, get_row, delete_row, add_column и другие
    
- **Граф**: add_node, get_nodes, delete_node, add_edge, get_neighbor и другие
    
- **Дерево**: add_node, get_nodes, delete_node, add_child и другие
    
- **Описание**: transform[15](https://arxiv.org/html/2504.10036v1)
    

## Мыслитель (Thinker)

Этот компонент выступает в роли критического контролера, оценивающего качество и достаточность результатов на каждом этапе анализа[15](https://arxiv.org/html/2504.10036v1). Функции Мыслителя включают:

- **Оптимизация подвопросов**: Оценка адекватности сформулированных подвопросов с точки зрения объема, ясности и атомарности.
    
- **Проверка выбранной структуры**: Анализ соответствия выбранной структуры данных характеру информации, необходимой для ответа.
    
- **Оценка достаточности информации**: Определение, достаточно ли извлеченной информации для ответа на подвопрос, с возможностью инициировать дополнительные итерации поиска.
    
- **Верификация структурированных данных**: Проверка корректности организации структурированных данных и наличия всех необходимых элементов для рассуждения[15](https://arxiv.org/html/2504.10036v1).
    

## Рассуждение (Reasoning)

Заключительный модуль интегрирует структурированные данные для генерации ответов на подвопросы:

$A_i = \text{Reasoning}(Q_i, D_i')$

и формирования финального ответа на исходный вопрос:

$A = \text{Integrate}(A_1, A_2, \ldots, A_n)$

Процесс интеграции учитывает взаимосвязи между подвопросами (параллельные или последовательные) и объединяет частичные ответы в целостное, связное решение[15](https://arxiv.org/html/2504.10036v1).

## Открытые проблемы в области мультимодальной аналитики данных

Авторы выделяют несколько ключевых открытых проблем, требующих дальнейших исследований:

## Извлечение информации из новых модальностей данных

Проблема извлечения информации из нестандартных модальностей (3D-облака точек, потоки сенсорных данных в реальном времени) остается нерешенной из-за специфических особенностей представления данных[15](https://arxiv.org/html/2504.10036v1). Например, согласование пространственно-временных сенсорных данных с текстовыми логами требует новых пространств вложений, сохраняющих причинно-временные зависимости. Современные методы (контрастное обучение) испытывают трудности с кросс-модальными инвариантами, например, при отображении геометрии LiDAR в естественный язык без потери структурной точности[15](https://arxiv.org/html/2504.10036v1).

## Извлечение информации из новых доменов

Переход между доменами (от финансов к биомедицине) выявляет уязвимость в индукции схем и связывании сущностей[15](https://arxiv.org/html/2504.10036v1). Ключевой проблемой является доменно-агностическое обучение схем: автоматизация обнаружения доменно-специфических онтологий (юридические положения, геномные варианты) без размеченных данных. Это требует переноса правил извлечения без дополнительного обучения, избегая при этом семантического дрейфа (например, смешения понятия "риск" в финансах и здравоохранении)[15](https://arxiv.org/html/2504.10036v1).

## Извлечение информации для новых структур данных

Для инновационных структур, таких как гиперграфы (многосторонние научные отношения) или топологические карты, отсутствуют формализованные грамматики извлечения[15](https://arxiv.org/html/2504.10036v1). Ключевые задачи включают определение минимальных схемных ограничений для малоизученных структур (клеточных комплексов в пространственных данных) и обеспечение композиционности при их объединении. Например, остается открытым вопрос, как извлечь гиперграф из сочетания текста и математических уравнений с сохранением n-арных отношений без использования эвристик или ручной курации[15](https://arxiv.org/html/2504.10036v1).

## Проверка полноты: "неизвестные неизвестные"

Существующие методы проверки полноты (подсчет нулевых значений, метрики покрытия) не позволяют эффективно выявить контекстуальные пропуски в мультимодальных данных[15](https://arxiv.org/html/2504.10036v1). Например, верификация того, что таблица медицинского отчета не опускает критические находки из изображений, требует рассуждений о кросс-модальных импликациях. Открытыми задачами остаются формализация сертификатов полноты (доказательств, что все релевантные данные извлечены) с использованием вероятностной логики и обнаружение "неизвестных неизвестных" через адверсариальные возмущения схем или информационно-теоретические границы покрытия[15](https://arxiv.org/html/2504.10036v1).
![[Pasted image 20250509152418.png]]
## Заключение

Предложенный фреймворк DataMosaic представляет собой комплексное решение для создания объяснимой и проверяемой мультимодальной аналитики данных. На основе многоагентной архитектуры и итеративного рабочего процесса извлечения-рассуждения-проверки, фреймворк преодолевает ключевые ограничения существующих подходов на основе LLM.

Основные преимущества DataMosaic включают прозрачность процесса анализа через структурированное представление данных, повышенную точность за счет верификации промежуточных результатов, обеспечение полноты и согласованности при интеграции данных из различных источников, защиту конфиденциальной информации через локальное развертывание и повышенную эффективность благодаря оптимизированным компонентам.

Значимость работы подчеркивается идентификацией ключевых открытых проблем в области мультимодальной аналитики данных, которые определяют направления для будущих исследований. DataMosaic представляет собой важный шаг к новой парадигме обоснованной, точной и объяснимой мультимодальной аналитики данных, расширяющей возможности LLM для решения реальных задач аналитики в различных доменах.

### Citations:

1. [https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/54300082/4579227e-f92f-4b85-b573-9f336983d3a4/paste.txt](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/54300082/4579227e-f92f-4b85-b573-9f336983d3a4/paste.txt)
2. [https://arxiv.org/abs/2504.10036](https://arxiv.org/abs/2504.10036)
3. [https://arxiv.org/pdf/2504.10036.pdf](https://arxiv.org/pdf/2504.10036.pdf)
4. [https://www.semanticscholar.org/paper/0497f9b25b8ad00984016711cd8a2a97daa167b4](https://www.semanticscholar.org/paper/0497f9b25b8ad00984016711cd8a2a97daa167b4)
5. [https://arxiv.org/abs/2503.15520](https://arxiv.org/abs/2503.15520)
6. [https://www.semanticscholar.org/paper/f419e8ac311a89684c2d40f14938a2576fcea082](https://www.semanticscholar.org/paper/f419e8ac311a89684c2d40f14938a2576fcea082)
7. [https://arxiv.org/html/2502.09956v1](https://arxiv.org/html/2502.09956v1)
8. [https://arxiv.org/abs/2006.08159](https://arxiv.org/abs/2006.08159)
9. [https://arxiv.org/abs/2406.06399](https://arxiv.org/abs/2406.06399)
10. [https://arxiv.org/html/2406.05804v1](https://arxiv.org/html/2406.05804v1)
11. [https://arxiv.org/abs/2212.02896](https://arxiv.org/abs/2212.02896)
12. [https://www.semanticscholar.org/paper/22fb012c718ab799a446a91c2f82510bd2c8c7d7](https://www.semanticscholar.org/paper/22fb012c718ab799a446a91c2f82510bd2c8c7d7)
13. [https://arxiv.org/abs/2503.13514](https://arxiv.org/abs/2503.13514)
14. [https://www.semanticscholar.org/paper/9f202274016074dd38bc6d4afe7cc7000d85b2a9](https://www.semanticscholar.org/paper/9f202274016074dd38bc6d4afe7cc7000d85b2a9)
15. [https://arxiv.org/html/2504.10036v1](https://arxiv.org/html/2504.10036v1)
16. [https://arxiv.org/abs/2501.14892](https://arxiv.org/abs/2501.14892)
17. [https://arxiv.org/html/2406.08100v1](https://arxiv.org/html/2406.08100v1)
18. [https://paperswithcode.com/paper/multi-modal-retrieval-of-tables-and-texts](https://paperswithcode.com/paper/multi-modal-retrieval-of-tables-and-texts)
19. [https://www.semanticscholar.org/paper/da234405fcbba9abb5dd37847f127507ea3e8015](https://www.semanticscholar.org/paper/da234405fcbba9abb5dd37847f127507ea3e8015)
20. [https://www.semanticscholar.org/paper/e974c3697da3a2c9794d1b98aa4c8ac05955f60a](https://www.semanticscholar.org/paper/e974c3697da3a2c9794d1b98aa4c8ac05955f60a)
21. [https://www.semanticscholar.org/paper/61ee910a35de8f3b7a37c13908f0299b893890db](https://www.semanticscholar.org/paper/61ee910a35de8f3b7a37c13908f0299b893890db)
22. [https://www.semanticscholar.org/paper/ac9a70fcb663ec44a2a480fc8e880248ca9692f6](https://www.semanticscholar.org/paper/ac9a70fcb663ec44a2a480fc8e880248ca9692f6)
23. [https://www.zerna.io/page/security/presentation_set/security-llm-research/presentation/security-trust-reliability/slide/security-paper-2504_10036](https://www.zerna.io/page/security/presentation_set/security-llm-research/presentation/security-trust-reliability/slide/security-paper-2504_10036)
24. [https://fugumt.com/fugumt/paper_check/2504.10036v1](https://fugumt.com/fugumt/paper_check/2504.10036v1)
25. [https://www.themoonlight.io/fr/review/datamosaic-explainable-and-verifiable-multi-modal-data-analytics-through-extract-reason-verify](https://www.themoonlight.io/fr/review/datamosaic-explainable-and-verifiable-multi-modal-data-analytics-through-extract-reason-verify)
26. [https://paperswithcode.com/author/zhuowen-liang](https://paperswithcode.com/author/zhuowen-liang)
27. [https://www.themoonlight.io/en/review/explainable-multi-modal-data-exploration-in-natural-language-via-llm-agent](https://www.themoonlight.io/en/review/explainable-multi-modal-data-exploration-in-natural-language-via-llm-agent)
28. [https://arxiv.org/abs/2406.05412](https://arxiv.org/abs/2406.05412)
29. [https://www.aimodels.fyi/author-profile/zhengxuan-zhang-8248aa90-f37d-470b-abfd-b6cf0c10e08a](https://www.aimodels.fyi/author-profile/zhengxuan-zhang-8248aa90-f37d-470b-abfd-b6cf0c10e08a)
30. [https://www.aimodels.fyi/author-profile/teng-lin-c8bb879f-04bd-4843-a0d5-336bba177b46](https://www.aimodels.fyi/author-profile/teng-lin-c8bb879f-04bd-4843-a0d5-336bba177b46)
31. [https://arxiv.org/abs/2210.14602](https://arxiv.org/abs/2210.14602)
32. [https://arxiv.org/abs/2504.07830](https://arxiv.org/abs/2504.07830)
33. [https://arxiv.org/html/2405.13326v1](https://arxiv.org/html/2405.13326v1)
34. [https://www.semanticscholar.org/paper/8a16671576f2400e3cd5d3910b14abeba85cfa46](https://www.semanticscholar.org/paper/8a16671576f2400e3cd5d3910b14abeba85cfa46)
35. [https://www.semanticscholar.org/paper/0cecd4e2f30a104f3ab9dbfda90e87a03f669332](https://www.semanticscholar.org/paper/0cecd4e2f30a104f3ab9dbfda90e87a03f669332)
36. [https://www.semanticscholar.org/paper/d4e933a5669312503d8ed2a1ae8285bafcfd2414](https://www.semanticscholar.org/paper/d4e933a5669312503d8ed2a1ae8285bafcfd2414)
37. [https://www.semanticscholar.org/paper/b13703c775e2b0d8582aa189cea5703ff9214d56](https://www.semanticscholar.org/paper/b13703c775e2b0d8582aa189cea5703ff9214d56)
38. [https://supportwiki.thinkautomation.com/Reference/Automation%20Action%20Types/](https://supportwiki.thinkautomation.com/Reference/Automation%20Action%20Types/)
39. [https://milvus.io/ai-quick-reference/what-is-cosine-similarity-and-why-is-it-used-in-semantic-search](https://milvus.io/ai-quick-reference/what-is-cosine-similarity-and-why-is-it-used-in-semantic-search)
40. [https://github.com/Sudhanshu1304/table-transformer](https://github.com/Sudhanshu1304/table-transformer)
41. [https://zilliz.com/ai-faq/what-are-the-limitations-of-explainable-ai](https://zilliz.com/ai-faq/what-are-the-limitations-of-explainable-ai)
42. [https://www.dqc.ai/post/why-agentic-workflows-are-transforming-data-quality-management](https://www.dqc.ai/post/why-agentic-workflows-are-transforming-data-quality-management)
43. [https://enoch3712.github.io/ExtractThinker/getting-started/](https://enoch3712.github.io/ExtractThinker/getting-started/)
44. [https://www.pinecone.io/learn/vector-similarity/](https://www.pinecone.io/learn/vector-similarity/)
45. [https://www.reddit.com/r/LangChain/comments/1ds4rnu/recommendation_for_table_extraction/](https://www.reddit.com/r/LangChain/comments/1ds4rnu/recommendation_for_table_extraction/)
46. [https://falkordb.com/blog/what-is-graphrag/](https://falkordb.com/blog/what-is-graphrag/)
47. [https://fractal.ai/post-llm-era-agentic-ai-workflow-transformation/](https://fractal.ai/post-llm-era-agentic-ai-workflow-transformation/)
48. [https://n8n.io/integrations/think-tool/and/waveline-extract/](https://n8n.io/integrations/think-tool/and/waveline-extract/)
49. [https://en.wikipedia.org/wiki/Cosine_similarity](https://en.wikipedia.org/wiki/Cosine_similarity)
50. [https://nanonets.com/blog/table-extraction-using-llms-unlocking-structured-data-from-documents/](https://nanonets.com/blog/table-extraction-using-llms-unlocking-structured-data-from-documents/)
51. [https://www.linkedin.com/posts/svpino_the-limitations-of-rag-are-becoming-very-activity-7213513972916891648-TVR8](https://www.linkedin.com/posts/svpino_the-limitations-of-rag-are-becoming-very-activity-7213513972916891648-TVR8)
52. [https://www.semanticscholar.org/paper/a8c1f2f1ca939c2f354c3db238b7e69a64040b0c](https://www.semanticscholar.org/paper/a8c1f2f1ca939c2f354c3db238b7e69a64040b0c)
53. [https://arxiv.org/abs/2409.14192](https://arxiv.org/abs/2409.14192)
54. [https://www.semanticscholar.org/paper/dc37bc82bb9d4155b419ad62c29f5f69bff0ce73](https://www.semanticscholar.org/paper/dc37bc82bb9d4155b419ad62c29f5f69bff0ce73)
55. [https://arxiv.org/abs/2410.20494](https://arxiv.org/abs/2410.20494)
56. [https://github.com/HazyResearch/TreeStructure](https://github.com/HazyResearch/TreeStructure)
57. [https://arxiv.org/abs/2502.09956](https://arxiv.org/abs/2502.09956)
58. [https://www.isi.edu/websites/ijcai03/papers/chidlovski-subtreeDelimiter03.pdf](https://www.isi.edu/websites/ijcai03/papers/chidlovski-subtreeDelimiter03.pdf)
59. [https://www.reddit.com/r/MachineLearning/comments/1jnjfaq/d_why_is_table_extraction_still_not_solved_by/](https://www.reddit.com/r/MachineLearning/comments/1jnjfaq/d_why_is_table_extraction_still_not_solved_by/)
60. [https://neo4j.com/blog/genai/text-to-knowledge-graph-information-extraction-pipeline/](https://neo4j.com/blog/genai/text-to-knowledge-graph-information-extraction-pipeline/)
61. [https://aclanthology.org/2024.findings-emnlp.628.pdf](https://aclanthology.org/2024.findings-emnlp.628.pdf)
62. [https://docs.sensible.so/docs/nlp-table](https://docs.sensible.so/docs/nlp-table)
63. [https://github.com/stair-lab/kg-gen](https://github.com/stair-lab/kg-gen)
64. [https://www.dcc.uchile.cl/~cgallegu/ie/kosala02information.pdf](https://www.dcc.uchile.cl/~cgallegu/ie/kosala02information.pdf)
65. [https://www.nlplanet.org/course-practical-nlp/02-practical-nlp-first-tasks/16-knowledge-graph-from-text](https://www.nlplanet.org/course-practical-nlp/02-practical-nlp-first-tasks/16-knowledge-graph-from-text)
66. [https://www.sciencedirect.com/science/article/pii/S0952197623001069](https://www.sciencedirect.com/science/article/pii/S0952197623001069)
67. [https://blog.langchain.dev/constructing-knowledge-graphs-from-text-using-openai-functions/](https://blog.langchain.dev/constructing-knowledge-graphs-from-text-using-openai-functions/)
68. [https://arxiv.org/pdf/2212.02896.pdf](https://arxiv.org/pdf/2212.02896.pdf)
69. [https://www.ncbi.nlm.nih.gov/pmc/articles/PMC10402318/](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC10402318/)
70. [https://www.ncbi.nlm.nih.gov/pmc/articles/PMC10259999/](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC10259999/)
71. [https://www.semanticscholar.org/paper/fd3ee97c59241af252058ef7a11895c6258a7169](https://www.semanticscholar.org/paper/fd3ee97c59241af252058ef7a11895c6258a7169)
72. [https://www.themoonlight.io/review/datamosaic-explainable-and-verifiable-multi-modal-data-analytics-through-extract-reason-verify](https://www.themoonlight.io/review/datamosaic-explainable-and-verifiable-multi-modal-data-analytics-through-extract-reason-verify)
73. [https://chatpaper.com/chatpaper/fr/paper/129041](https://chatpaper.com/chatpaper/fr/paper/129041)
74. [https://www.aimodels.fyi/papers/arxiv/datamosaic-explainable-verifiable-multi-modal-data-analytics](https://www.aimodels.fyi/papers/arxiv/datamosaic-explainable-verifiable-multi-modal-data-analytics)
75. [https://www.themoonlight.io/en/review/datamosaic-explainable-and-verifiable-multi-modal-data-analytics-through-extract-reason-verify](https://www.themoonlight.io/en/review/datamosaic-explainable-and-verifiable-multi-modal-data-analytics-through-extract-reason-verify)
76. [https://arxiv.org/abs/2405.13326](https://arxiv.org/abs/2405.13326)
77. [https://arxiv.org/abs/2410.20142](https://arxiv.org/abs/2410.20142)
78. [https://arxiv.org/abs/2501.16382](https://arxiv.org/abs/2501.16382)
79. [https://www.datasciencecentral.com/understanding-graphrag-2-addressing-the-limitations-of-rag/](https://www.datasciencecentral.com/understanding-graphrag-2-addressing-the-limitations-of-rag/)
80. [https://www.harrisonclarke.com/blog/challenges-and-future-directions-in-rag-research-embracing-data-ai](https://www.harrisonclarke.com/blog/challenges-and-future-directions-in-rag-research-embracing-data-ai)
81. [https://arxiv.org/html/2401.05856v1](https://arxiv.org/html/2401.05856v1)
82. [https://rainbird.ai/beyond-rag-why-retrieval-augmented-reasoning-rar-is-a-game-changer/](https://rainbird.ai/beyond-rag-why-retrieval-augmented-reasoning-rar-is-a-game-changer/)
83. [https://milvus.io/ai-quick-reference/what-are-the-limitations-of-explainable-ai](https://milvus.io/ai-quick-reference/what-are-the-limitations-of-explainable-ai)
84. [https://www.trantorinc.com/blog/understanding-retrieval-augmented-generation](https://www.trantorinc.com/blog/understanding-retrieval-augmented-generation)
85. [https://www.semanticscholar.org/paper/60e3cb93de79abcd626882872051d5ca16f6e6f9](https://www.semanticscholar.org/paper/60e3cb93de79abcd626882872051d5ca16f6e6f9)
86. [https://www.semanticscholar.org/paper/5e5c2b3bfde5f5d60d7f27cc049b629308a2a8dc](https://www.semanticscholar.org/paper/5e5c2b3bfde5f5d60d7f27cc049b629308a2a8dc)
87. [https://www.semanticscholar.org/paper/d9615461615154050142423a4ed2e9ae90d25b72](https://www.semanticscholar.org/paper/d9615461615154050142423a4ed2e9ae90d25b72)
88. [https://auto.gluon.ai/0.6.1/tutorials/tabular_prediction/tabular-multimodal.html](https://auto.gluon.ai/0.6.1/tutorials/tabular_prediction/tabular-multimodal.html)
89. [https://aclanthology.org/2020.acl-tutorials.6/](https://aclanthology.org/2020.acl-tutorials.6/)
90. [https://www.youtube.com/watch?v=7FMzD4AYOCY](https://www.youtube.com/watch?v=7FMzD4AYOCY)
91. [https://docs.llamaindex.ai/en/v0.10.18/examples/multi_modal/multi_modal_pdf_tables.html](https://docs.llamaindex.ai/en/v0.10.18/examples/multi_modal/multi_modal_pdf_tables.html)
92. [https://stackoverflow.com/questions/58254173/python-extract-ms-word-data-in-a-tree-structure](https://stackoverflow.com/questions/58254173/python-extract-ms-word-data-in-a-tree-structure)

---

Answer from Perplexity: [pplx.ai/share](https://www.perplexity.ai/search/pplx.ai/share)
