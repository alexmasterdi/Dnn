#  Обучение сети GAN c использованием библиотеки Keras
Структура файла GAN:
* model training.py - скрипт для обучения сети GAN
* sample.py - пример применения обученной модели
* GAN_images_finally - изображения которые создает генеротор после n эпох обучения (в качестве n - числа кратные 20 от 1 до 200)
* GAN_images_models - сохраненные конфигурации моделей дискриминатора и генератора после n эпох обучения
# Цель - генерация рукописных цифр неотличимых от реальных
Короткий план обучения модели: 
1. Собрать тестовые данные и сделать их предварительныю обработку (используется база данных MNIST).
2. Создание сети дискриминатора и генератора
3. Создание общей сети GAN объединяющей дискриминатор и генератор (когда происходит тренировка генератора, дискриминатор временно "замараживается").
4. Сам процесс обучения. Состоит из следующих этапов: 
    * Генерация фейковых изображений из шума
    * Формирование набора реальных изображений рукописных цифр
    * Тренировка дискриминатора на фейковых и реальных изображениях
    * Заморозка дискриминатора и тренировка генератора.
   Обучение происходит на 200 эпохах.
5. С переодичностью в 20 эпох сохраняются модели, а также результат вывода сети генератора в виде png. Сами модели дискриминатора и генератора сохраняются в формате .h5 и в дальнейшем могут быть использованы в приложениях (что продемонстрировано в sample.py).

В работе использовались следующие инструменты: 
 * https://keras.io/ - библиотека Keras
 * https://neurohive.io/ru/tutorial/simple-gan-python-keras/ - инструкция по обучению сети GAN
 
 Наглядные примеры работы генератора:
 после 1 эпохи: ![](https://github.com/alexmasterdi/Dnn/blob/GAN/GAN/GAN_images_finally/gan_generated_image%201.png)

