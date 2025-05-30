# Обзор статьи "HM-RAG: Hierarchical Multi-Agent Multimodal Retrieval Augmented Generation"

В данной статье представлен HM-RAG (Hierarchical Multi-Agent Multimodal Retrieval Augmented Generation) - новаторский фреймворк, разработанный для решения сложных задач в системах Retrieval-Augmented Generation (RAG). HM-RAG предлагает иерархическую многоагентную архитектуру, которая интегрирует мультимодальные данные (текст, изображения, графы) и обеспечивает динамический синтез знаний. Этот обзор подробно анализирует ключевые аспекты статьи, включая проблему, архитектуру фреймворка, методологию, экспериментальные результаты и выводы, с акцентом на технические и математические детали.

## Проблематика и мотивация

Современные системы RAG, несмотря на успехи в интеграции внешних знаний с большими языковыми моделями (LLM), сталкиваются с ограничениями при обработке сложных запросов, требующих координированного рассуждения в гетерогенных данных. Традиционные одноагентные системы RAG не способны эффективно обрабатывать мультимодальные данные (текст, изображения, графы) из-за отсутствия механизмов для кросс-модального синтеза и согласования. Это приводит к потере информации и снижению точности ответов в задачах, требующих комплексного анализа1[2](https://arxiv.org/abs/2504.12330)[3](https://arxiv.org/html/2504.12330v1).

**Ключевые вызовы:**

- **Модальная изоляция:** Текстовые системы не могут эффективно обрабатывать визуальные данные, а визуальные системы теряют точность в текстовом анализе.
    
- **Гетерогенность данных:** Необходимость интеграции структурированных, неструктурированных и графовых данных.
    
- **Сложность запросов:** Традиционные системы не справляются с многонамеренными запросами, требующими декомпозиции и параллельной обработки.
    

HM-RAG предлагает решение этих проблем через иерархическую архитектуру с специализированными агентами, обеспечивающими совместную работу для синтеза знаний и повышения точности ответов1[2](https://arxiv.org/abs/2504.12330).

## Архитектура HM-RAG

HM-RAG основан на трехуровневой архитектуре, включающей три типа агентов, каждый из которых выполняет специализированные функции. Эта структура позволяет декомпозировать сложные запросы, выполнять параллельный поиск по различным модальностям и интегрировать результаты для формирования точных ответов1[3](https://arxiv.org/html/2504.12330v1)[4](https://arxiv.org/pdf/2504.12330.pdf).
![[Pasted image 20250509152711.png]]
**1. Агент декомпозиции (Decomposition Agent):**

- **Функция:** Разбивает сложные многонамеренные запросы на контекстно-согласованные подзадачи. Использует семантически-осведомленную переформулировку запросов и контекстное обогащение на основе схем.
    
- **Методология:** Агент сначала определяет, является ли запрос многонамеренным, с помощью бинарной классификации через LLM. Если запрос сложный, он декомпозируется на подзапросы q={q1,…,qn}q = \{q_1, \dots, q_n\}q={q1,…,qn} с использованием структурированных промптов, сохраняющих ключевые слова исходного запроса1[4](https://arxiv.org/pdf/2504.12330.pdf).
    
- **Значение:** Этот этап позволяет обрабатывать сложные запросы, требующие анализа различных аспектов, что недоступно традиционным системам.
    

**2. Агенты мультиисточникового поиска (Multi-source Retrieval Agents):**

- **Функция:** Выполняют параллельный поиск, специфичный для каждой модальности, используя модули plug-and-play для векторных, графовых и веб-баз данных.
    
- **Типы агентов:**
    
    - **Векторный агент:** Использует семантические эмбеддинги для поиска в неструктурированных текстовых корпусах. Для запроса qqq вычисляется эмбеддинг hq=Etext(q)h_q = \mathcal{E}_{text}(q)hq=Etext(q), затем определяется косинусное сходство с эмбеддингами документов sj=hqThj∥hq∥∥hj∥s_j = \frac{h_q^T h_j}{\|h_q\| \|h_j\|}sj=∥hq∥∥hj∥hqThj, и выбираются топ-k документов Rk={c1,…,ck}\mathcal{R}_k = \{c_1, \dots, c_k\}Rk={c1,…,ck}1[4](https://arxiv.org/pdf/2504.12330.pdf).
        
    - **Графовый агент:** Основан на LightRAG для обработки мультишаговых семантических запросов через мультимодальные графы знаний (MMKG). Формирует контекстно-зависимый подграф Gq⊆GG_q \subseteq GGq⊆G, используя механизм внимания для выбора релевантных триплетов (h,r,t)(h, r, t)(h,r,t)1[4](https://arxiv.org/pdf/2504.12330.pdf).
        
    - **Веб-агент:** Использует API Google Serper для получения актуальной информации в реальном времени. Формализуется как R=Google(q;θsearch)\mathcal{R} = Google(q; \theta_{search})R=Google(q;θsearch), где θsearch\theta_{search}θsearch задает параметры поиска (например, количество результатов)1[4](https://arxiv.org/pdf/2504.12330.pdf).
        
- **Значение:** Параллельная обработка данных из разных источников обеспечивает полноту и разнообразие информации, что критично для мультимодальных задач.
    
![[Pasted image 20250509152730.png]]
**3. Агент принятия решений (Decision Agent):**

- **Функция:** Интегрирует ответы от мультиисточниковых агентов через механизм голосования по согласованности (Consistency Voting) и уточнение с помощью экспертной модели (Expert Model Refinement).
    
- **Методология:** Использует метрики ROUGE-L и BLEU для оценки семантической согласованности между ответами {Av,Ag,Aw}\{\mathcal{A}_v, \mathcal{A}_g, \mathcal{A}_w\}{Av,Ag,Aw}. ROUGE-L определяется как RL=LCS(Si,Sj)max⁡(∣Si∣,∣Sj∣)R_L = \frac{LCS(\mathcal{S}_i, \mathcal{S}_j)}{\max(|\mathcal{S}_i|, |\mathcal{S}_j|)}RL=max(∣Si∣,∣Sj∣)LCS(Si,Sj), где LCS - наибольшая общая подпоследовательность, а BLEU оценивает точность n-грамм. Если согласованность ниже порога, применяется уточнение через LLM или мультимодальные модели (MLLM)1[4](https://arxiv.org/pdf/2504.12330.pdf).
    
- **Значение:** Этот агент обеспечивает финальную точность и согласованность ответов, устраняя расхождения между результатами поиска.
    

## Методология

Методология HM-RAG включает предварительную обработку мультимодальных данных и детализированные процессы для каждого агента.

**1. Предварительная обработка мультимодальных данных:**

- **Генерация текстовых знаний:** Используется модель BLIP-2 для преобразования визуальной информации в текстовые описания Tv=Dblip2(falign(Eblip2(Iv)))T_v = \mathcal{D}_{blip2}(f_{align}(\mathcal{E}_{blip2}(I_v)))Tv=Dblip2(falign(Eblip2(Iv))), которые затем объединяются с исходным текстовым корпусом Tm=Concatenate(T,Tv)T_m = Concatenate(T, T_v)Tm=Concatenate(T,Tv)1[4](https://arxiv.org/pdf/2504.12330.pdf).
    
- **Построение мультимодальных графов знаний (MMKG):** Применяется LightRAG для создания графов G=LightRAG(Tv,T)G = LightRAG(T_v, T)G=LightRAG(Tv,T), где извлекаются сущности и отношения, а визуальные данные встраиваются для кросс-модального обоснования1[4](https://arxiv.org/pdf/2504.12330.pdf).
    

**2. Детали работы агентов:**

- Агент декомпозиции использует LLM для анализа намерений и разбиения запросов на подзадачи, что позволяет эффективно обрабатывать сложные вопросы.
    
- Агенты поиска работают параллельно, каждый оптимизирован для своей модальности. Например, векторный агент использует авторегрессивное декодирование для генерации ответов p(y∣q,RK)=∏t=1Tplm(yt∣y<t,q,RK)p(y|q, \mathcal{R}_K) = \prod_{t=1}^T p_{lm}(y_t|y_{<t}, q, \mathcal{R}_K)p(y∣q,RK)=∏t=1Tplm(yt∣y<t,q,RK), минимизируя галлюцинации через детерминированное декодирование1[4](https://arxiv.org/pdf/2504.12330.pdf).
    
- Агент решений применяет взвешенную комбинацию метрик для оценки согласованности и, при необходимости, использует экспертные модели для уточнения.
    

## Экспериментальные результаты

HM-RAG был протестирован на двух бенчмарках: ScienceQA (мультимодальное научное вопрос-ответ) и CrisisMMD (классификация кризисных событий). Результаты демонстрируют значительное превосходство над базовыми методами1[3](https://arxiv.org/html/2504.12330v1)[4](https://arxiv.org/pdf/2504.12330.pdf).

**1. Результаты на ScienceQA:**

- HM-RAG достигает средней точности 93.73%, что на 4.11% выше, чем у LLaMA-SciTune, и на 2.82% выше, чем у GPT-4o.
    
- По сравнению с одноагентными RAG-системами (векторными, графовыми, веб-базированными) улучшение составляет 12.95%, 12.71% и 12.13% соответственно.
    
- Особенно заметны улучшения в задачах по общественным наукам (SOC), где точность выше на 24.38% по сравнению с веб-базированными системами1[4](https://arxiv.org/pdf/2504.12330.pdf).
    

**2. Результаты на CrisisMMD:**

- Средняя точность HM-RAG составляет 58.55%, что на 2.44% выше, чем у GPT-4o, и на 3.44% выше, чем у текстового варианта Qwen2.5-72B.
    
- Эффективность параметров: HM-RAG с 7B параметрами превосходит модели с 72B параметрами, демонстрируя улучшение на 2.3% по сравнению с Qwen2.5-72B.
    
- Мультимодальная интеграция дает прирост точности на 5.7% по сравнению с текстовыми вариантами1[4](https://arxiv.org/pdf/2504.12330.pdf).
    

**3. Абляционные исследования:**

- Удаление агента решений приводит к наибольшему снижению точности (10.82%), что подчеркивает его критическую роль.
    
- Отключение веб-агента снижает точность на 5.63%, особенно в сложных задачах для старших классов.
    
- Полная конфигурация HM-RAG обеспечивает наилучшие результаты, превосходя частичные конфигурации на 2.44%1[4](https://arxiv.org/pdf/2504.12330.pdf).
    
![[Pasted image 20250509152740.png]]
## Анализ и обсуждение

**1. Преимущества HM-RAG:**

- **Модульность:** Архитектура plug-and-play позволяет легко интегрировать новые модальности данных, сохраняя гибкость и масштабируемость.
    
- **Эффективность:** Параллельная обработка через специализированных агентов сокращает время поиска и повышает точность.
    
- **Кросс-модальная интеграция:** HM-RAG успешно сочетает текстовую, визуальную и графовую информацию, устраняя проблему модальной изоляции1[2](https://arxiv.org/abs/2504.12330)[3](https://arxiv.org/html/2504.12330v1).
    

**2. Ограничения и потенциальные улучшения:**

- Хотя HM-RAG демонстрирует впечатляющие результаты, его производительность зависит от качества базовых моделей (LLM, VLM), что может быть узким местом в менее развитых доменах.
    
- Высокая вычислительная сложность, особенно при использовании экспертных моделей для уточнения, может ограничивать применение в реальном времени на устройствах с ограниченными ресурсами.
    
- Возможное направление улучшения - оптимизация механизмов голосования и снижение зависимости от пороговых значений для согласованности1[4](https://arxiv.org/pdf/2504.12330.pdf).
    

**3. Сравнение с базовыми методами:**

- HM-RAG превосходит одноагентные системы RAG благодаря многоуровневой архитектуре и совместной работе агентов.
    
- По сравнению с современными LLM и VLM (например, GPT-4o), HM-RAG демонстрирует лучшую точность в мультимодальных задачах, особенно в условиях zero-shot1[4](https://arxiv.org/pdf/2504.12330.pdf).
    

## Заключение

HM-RAG представляет собой значительный шаг вперед в развитии систем RAG, решая ключевые проблемы мультимодального рассуждения и синтеза знаний. Его иерархическая многоагентная архитектура, включающая агентов декомпозиции, мультиисточникового поиска и принятия решений, обеспечивает беспрецедентную точность и гибкость в обработке сложных запросов. Экспериментальные результаты на бенчмарках ScienceQA и CrisisMMD подтверждают его превосходство над существующими методами, устанавливая новые стандарты в области мультимодального поиска и генерации. Несмотря на некоторые ограничения, связанные с вычислительной сложностью, HM-RAG открывает перспективы для создания более адаптивных и мощных систем информационного поиска, применимых в самых разных областях, от научных исследований до реагирования на кризисы1[2](https://arxiv.org/abs/2504.12330)[3](https://arxiv.org/html/2504.12330v1)[4](https://arxiv.org/pdf/2504.12330.pdf).
