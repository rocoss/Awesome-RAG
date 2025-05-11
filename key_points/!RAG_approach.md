## Тут будут архитектуры и идеи разных подходов.


# 1. [RAG-Star: Enhancing Deliberative Reasoning with Retrieval Augmented Verification and Refinement](https://aclanthology.org/2025.naacl-long.361.pdf)

![Overall framework of our proposed RAG-Star approach](https://github.com/rocoss/Awesome-RAG/blob/main/images/RAG/1.png)



# 2. [SimRAG: Self-Improving Retrieval-Augmented Generation forAdapting Large Language Models to Specialized Domains](https://aclanthology.org/2025.naacl-long.575.pdf)

![](https://github.com/rocoss/Awesome-RAG/blob/main/images/RAG/2.png)

Двухэтапная система тонкой настройки для предлагаемого нами метода Simrad. Сначала модель настраивается на основе данных, связанных с поиском. Затем он генерирует кортежи с псевдомаркировкой, сначала извлекая возможные ответы из корпуса, а затем генерируя возможные вопросы, основанные как на документе, так и на ответе. LLM дополнительно дорабатывается на примерах с псевдомаркировкой, отфильтрованных с последовательностью прохождения.



# 3. [Transform Retrieval for Textual Entailment in RAG](https://aclanthology.org/2025.naacl-long.50.pdf)

![](https://github.com/rocoss/Awesome-RAG/blob/main/images/RAG/3.png)

Общая архитектура поиска преобразований. Внедрение запроса передается в модель преобразования, и разница между внедрением преобразованного запроса и внедрением документа используется для оптимизации модели преобразования, которая преобразует исходное внедрение запроса в желаемое внедрение запроса для извлечения текстовых последствий.
