## Тут будут архитектуры и идеи разных подходов.


# 1. [RAG-Star: Enhancing Deliberative Reasoning with Retrieval Augmented Verification and Refinement](https://aclanthology.org/2025.naacl-long.361.pdf)

![Overall framework of our proposed RAG-Star approach](https://github.com/rocoss/Awesome-RAG/blob/main/images/RAG/1.png)



# 2. [SimRAG: Self-Improving Retrieval-Augmented Generation forAdapting Large Language Models to Specialized Domains](https://aclanthology.org/2025.naacl-long.575.pdf)

![](https://github.com/rocoss/Awesome-RAG/blob/main/images/RAG/2.png)

Двухэтапная система тонкой настройки для предлагаемого нами метода Simrad. Сначала модель настраивается на основе данных, связанных с поиском. Затем он генерирует кортежи с псевдомаркировкой, сначала извлекая возможные ответы из корпуса, а затем генерируя возможные вопросы, основанные как на документе, так и на ответе. LLM дополнительно дорабатывается на примерах с псевдомаркировкой, отфильтрованных с последовательностью прохождения.



# 3. [Transform Retrieval for Textual Entailment in RAG](https://aclanthology.org/2025.naacl-long.50.pdf)

![](https://github.com/rocoss/Awesome-RAG/blob/main/images/RAG/3.png)

Общая архитектура поиска преобразований. Внедрение запроса передается в модель преобразования, и разница между внедрением преобразованного запроса и внедрением документа используется для оптимизации модели преобразования, которая преобразует исходное внедрение запроса в желаемое внедрение запроса для извлечения текстовых последствий.

# 4. [SuperRAG: Beyond RAG with Layout-Aware Graph Modeling](https://aclanthology.org/2025.naacl-long.45.pdf)

![SuperRAG framework](https://github.com/rocoss/Awesome-RAG/blob/main/images/RAG/4.png)

Показан конвейер LAQM, который объединяет несколько средств поиска и ранжирования, сочетая эвристический обход графа, поиск сходства и методы, основанные на языковой модели, для эффективного поиска и ранжирования. Конвейер является гибким в нескольких отношениях. Во-первых, он объединяет межстраничный контекст, используя графическое представление. Во-вторых, для документов со структурированной информацией предусмотрен поиск оглавления, что улучшает качество контекста для конкретных запросов.Кроме того, конвейер использует расширение диаграмм/таблиц для запросов, которым требуется информация из таблиц и диаграмм, со слоем саморефлексии, позволяющим оценить, необходима ли информация из таблиц или диаграмм, исходя из цели запроса. Он выборочно интегрирует эти элементы только тогда, когда они способствуют получению более точного ответа, сокращая поиск нерелевантного контента. Примечательно, что LAGM не зависит от конвейера и может интегрироваться в любой конвейер RAG.





# 5. [Evaluating the Performance of RAG Methods for Conversational AI in the Airport Domain](https://aclanthology.org/2025.naacl-long.61.pdf)

типичный базовый - неточный и слабопротестированный подход

![Method on Ambiguous question dataset](https://github.com/rocoss/Awesome-RAG/blob/main/images/RAG/5.png)

Основное исследование сравнивает три метода RAG в контексте аэропортов. Традиционный подход BM25+GPT-4 эффективен благодаря специализированной терминологии, но склонен к галлюцициям, что рискованно для безопасности[1][6]. SQL RAG и Graph RAG снижают эту проблему, причем Graph RAG демонстрирует высшую точность, особенно в логических и динамических сценариях (например, обработка изменений рейсов)[4][5].  

**Ограничения**:  
- Тестирование в статичной среде без реальных данных (задержки, смена выходов)[4].  
- Небольшой набор данных, требующий расширения для проверки устойчивости[6].  
- Прототип не интегрирован с реальными API и требует дополнительных проверок безопасности перед внедрением[3].  
