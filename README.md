# Neural style transfer for images.
Команда и задачи: [google doc](https://docs.google.com/document/d/1teKxYaVdZJcRRitxQp1_dz0ni_dZii58REJTjPg87E4/edit?usp=sharing)

Реализация бэйзлайна
--------------------
За основу взята статья Gatys et al.: [https://arxiv.org/pdf/1508.06576.pdf](https://arxiv.org/pdf/1508.06576.pdf)

Основная идея
--------------------

Определим два расстояния: D<sub>S</sub> и D<sub>C</sub>. Первое — расстояние между стилями двух изображений, второе — расстояние между их содержанием. Тогда если у нас есть изображение, из которого хотим взять стиль, и изображение, из которого хотим взять содержание, то возьмем какое-то третье изображение и будем так его менять, чтобы минимизировать эти два расстояния: с изображением со стилем — D<sub>S</sub>, с содержанием — D<sub>C</sub>.

Загрузка изображений
------------------

Загрузим изображения со стилем и с содержанием. Во-первых, изображения для дальнейшего использования должны иметь одинаковые размеры, поэтому приведем оба к какому-то фиксированному размеру. Также предобученные сети, выдающие признаки изображений, используют диапазон значений [0,1], поэтому при загрузке изображений приведем значения к такому виду.

Content Loss
--------------
Content Loss — функция, возвращающая взвешенную разницу по содержанию на каком-то слое между текущим изображением и изображением, к содержанию которого стремимся. D<sub>C</sub><sup>L(X,C)</sup>, X — изображение, которое модифицируем, C — изображение с нужным содержанием. Пусть расстоянием будет средняя квадратичная ошибка: ``nn.MSELoss``.

Добавляем слой с этой функцией после каждого convolution слоя.


Style Loss
--------------
Для подсчета потерь для стиля тоже сделаем отдельный слой. Для подсчета ошибки здесь надо посчитать матрицу грамма: произведение матрицы фич на транспонированную. Эта матрица содержит корреляции между признаками, и есть предположение, что у изображений с похожим стилем эти корреляции будут близки. Поэтому на этом слое будем считать среднюю квадратичную ошибку между матрицами грамма.

Использование модели
-------------------

Хотим использовать предобученную модель. Будем использовать VGG19 — предобученная модель для выделения признаков изображения. В PyTorch у модели VGG есть модуль ``features`` состоящий из convolution и pooling слоев, который как раз и возвращает признаки изображений. 

Будем использовать градиентный спуск для минимизации функций потерь. При этом можем случайно выйти за границы диапазона [0,1], поэтому после каждого шага будем корректировать значения.




