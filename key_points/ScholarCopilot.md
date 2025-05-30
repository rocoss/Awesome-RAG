# ScholarCopilot: Обучение больших языковых моделей для академического письма с точными цитированиями

ScholarCopilot представляет собой инновационную разработку в сфере генеративного искусственного интеллекта, созданную для улучшения процесса академического письма путем объединения генерации текста и точного цитирования научной литературы. В данном обзоре рассматриваются ключевые аспекты этого исследования, предложенного командой исследователей из Университета Ватерлоо и Университета Карнеги-Меллон.

## Решаемые проблемы и предлагаемый подход

Академическое письмо требует не только логически связного генерирования текста, но и точного цитирования соответствующей литературы. Существующие системы Retrieval-Augmented Generation (RAG) улучшили фактическую точность в генерации текста общего назначения, однако их способность поддерживать профессиональное академическое письмо остается ограниченной[2](https://arxiv.org/abs/2504.00824)[3](https://arxiv.org/html/2504.00824v2).

Традиционные системы RAG обычно следуют статичному подходу "сначала поиск, затем генерация", что приводит к трем основным проблемам:

1. Разделенная оптимизация моделей поиска и генерации ведет к несоответствию в намерениях запроса
    
2. Предопределенные решения по поиску не обладают гибкостью и контекстной осведомленностью
    
3. Статичный конвейер ограничивает контроль пользователя над генерацией контента и потребностями в цитировании1
    

ScholarCopilot предлагает принципиально новый подход "边生成、边检索" (генерация и поиск одновременно), в котором модель динамически определяет, когда необходимо цитирование, и генерирует специальный токен поиска [RET]. Затем система приостанавливает процесс генерации, ищет релевантные научные ссылки и интегрирует их содержимое в последующие шаги генерации[8](https://www.qbitai.com/2025/04/272886.html)[9](https://huggingface.co/TIGER-Lab/ScholarCopilot-v1).

## Архитектура и обучение модели

ScholarCopilot построен на основе модели Qwen-2.5-7B и обучен на 500 тысячах научных статей из архива arXiv[2](https://arxiv.org/abs/2504.00824)[3](https://arxiv.org/html/2504.00824v2). Архитектура системы объединяет генерацию текста и поиск цитат в рамках единой модели, используя следующие компоненты:

1. **Предсказание следующего токена** (Next Token Prediction Loss L_g): стандартная авторегрессивная языковая модель, максимизирующая правдоподобие каждого токена x_t при условии предыдущих токенов x_<t и извлеченного содержимого c1.
    
2. **Контрастное обучение для поиска цитат** (Contrastive Loss L_r): оптимизирует представления токенов поиска, поощряя более высокое сходство между представлениями токенов поиска q и положительными (релевантными) представлениями цитат d+, и более низкое сходство с отрицательными (нерелевантными) цитатами d-1.
    
3. **Совместная оптимизация**: минимизирует комбинированную потерю L_total = L_g + λL_r, где λ уравновешивает цели генерации и поиска (в экспериментах λ=1)1.
    

## Сбор и обработка данных

Процесс создания набора данных для обучения ScholarCopilot состоял из пяти основных этапов1:

1. **Сбор статей**: 670 тысяч компьютерных научных статей, опубликованных на arXiv с 2007 по 2024 год, из которых успешно получен LaTeX-код для 570 тысяч статей.
    
2. **Анализ структуры**: извлечение структурированных компонентов, включая заголовки, аннотации, введения, разделы о связанных работах и библиографии с использованием эвристических методов.
    
3. **Извлечение цитирований**: извлечение информации о цитировании из библиографических записей с использованием модели Qwen-2.5-3B-Instruct для надежного извлечения заголовков статей.
    
4. **Сопоставление ссылок**: сопоставление извлеченных заголовков цитат с академическими базами данных - 10 миллионов соответствий в репозитории метаданных arXiv и дополнительно 6,8 миллиона в базе данных Semantic Scholar.
    
5. **Интеграция набора данных**: объединение проанализированных структур статей с их соответствующими цитированиями, в среднем 38 цитирований на статью, из которых 33 (87%) успешно сопоставлены.
    

## Экспериментальные результаты

ScholarCopilot был оценен по двум основным критериям: качеству генерации и точности поиска16.

## Качество генерации текста

Модель достигла общего балла 16,2 из 25 в оценке качества генерации, превосходя как Qwen-2.5-7B-Instruct с улучшением поиска (13,94), так и стандартный Qwen-2.5-7B-Instruct с цитатами из эталонных данных (14,44). Примечательно, что ScholarCopilot даже превзошел гораздо более крупную модель Qwen-2.5-72B-Instruct с улучшением поиска (15,81), имея всего около 10% её параметров1[4](https://www.themoonlight.io/en/review/scholarcopilot-training-large-language-models-for-academic-writing-with-accurate-citations).

Особые преимущества были продемонстрированы в релевантности (3,63 из 5) и связности (3,66 из 5), что сопоставимо с 72-миллиардными моделями. Улучшение академической строгости (2,87 против 2,26 для Qwen-2.5-7B-re) подчеркивает способность модели включать соответствующие цитаты и научные конвенции1.

## Точность поиска

ScholarCopilot достиг точности поиска по Top-1 в 40,1%, значительно превосходя базовые методы BM25 (9,8%) и E5-Mistral-7B-Instruct (15,0%). Это преимущество сохраняется на всех уровнях поиска, при этом ScholarCopilot достигает 64,8% Recall@10, что более чем вдвое превышает показатель E5-Mistral-7B-Instruct и втрое – BM251[8](https://www.qbitai.com/2025/04/272886.html).

## Пользовательское исследование

Для оценки практической пользы ScholarCopilot было проведено исследование с участием 10 студентов (5 аспирантов, 4 магистрантов и 1 студента бакалавриата) со средним опытом академического письма 4,2 года1.

ScholarCopilot получил наивысшие оценки за точность цитирования (4,6/5), ясность интерфейса (4,5/5) и стиль письма (4,5/5). Метрики качества цитирования в среднем составили 4,3/5. При сравнении с ChatGPT, ScholarCopilot показал явное преимущество в качестве цитирования, где все участники оценили его выше. Что касается общей полезности, 70% оценили ScholarCopilot выше1[20](https://www.linkedin.com/pulse/scholarcopilot-training-large-language-models-academic-vlad-bogolin-3ltae).

## Ограничения и направления будущей работы

Несмотря на многообещающие результаты, ScholarCopilot в настоящее время поддерживает только разделы введения и связанных работ в области компьютерных наук. Будущие исследования планируется направить на расширение поддержки дополнительных разделов статей и различных академических дисциплин1.

Пользовательское исследование также выявило ограничения в генерации инновационных идей (оценка 2,5/5). Решение этой проблемы требует изучения более крупных моделей, расширенных наборов данных и целевых методов обучения для повышения креативности1[20](https://www.linkedin.com/pulse/scholarcopilot-training-large-language-models-academic-vlad-bogolin-3ltae).

## Выводы

ScholarCopilot представляет собой значительный прорыв в области искусственного интеллекта для академического письма, объединяя динамический поиск с генеративным процессом. В отличие от традиционных статических конвейеров поиска-генерации, ScholarCopilot адаптивно извлекает цитаты на основе развивающихся контекстов генерации, значительно улучшая точность цитирования и связность1.

Обширная оценка и пользовательские исследования продемонстрировали эффективность системы, особенно в отношении релевантности цитирования, эффективности письма и общего пользовательского опыта. Несмотря на текущие ограничения в масштабе, возможностях инноваций и дизайне взаимодействия, ScholarCopilot знаменует собой первый шаг к будущим достижениям в области академического письма с поддержкой искусственного интеллекта1[9](https://huggingface.co/TIGER-Lab/ScholarCopilot-v1).

Важно отметить, что основное преимущество ScholarCopilot заключается в его способности объединять точные цитирования с генерацией текста, что было подтверждено в исследовании, где 100% пользователей отметили превосходство ScholarCopilot над ChatGPT в качестве цитирования1[9](https://huggingface.co/TIGER-Lab/ScholarCopilot-v1)[20](https://www.linkedin.com/pulse/scholarcopilot-training-large-language-models-academic-vlad-bogolin-3ltae).
