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
 после 20 эпохи: ![](https://github.com/alexmasterdi/Dnn/blob/GAN/GAN/GAN_images_finally/gan_generated_image%2020.png)
 после 100 эпохи: ![](https://github.com/alexmasterdi/Dnn/blob/GAN/GAN/GAN_images_finally/gan_generated_image%20100.png)

# Запуск обученной GAN с использованием OpenVINO toolkit

Для того, чтобы использовать сохраненные на стадии обучения модели генератора (.h5), необходимо выполнить следующие действия:
   1) Сконвертировать модель из формата h5 в формат onnx, так как формат Keras не поддерживается OpenVINO.
   2) Полученную onnx модель нужно при помощи model optimizer (mo.py)  конвертировать в промежуточный формат (Intermediate representation, IR) OpenVINO.
   3) Теперь когда получены весовые и конфигурационные файлы модели, которые поддерживаются OpenVINO, мы можем написать sample с использованием этого интрумента. Далее идёт более подробное описание шагов:

* Конвертация модели из формата h5 в onnx :
    Для того, чтобы осуществить конвертацию, воспользуемся готовым модулем "keras2onnx". Почему конвертируем именно в onnx? Во-первых, это распространенный формат, поддерживаемый многими библиотеками. Во-вторых, по сравнению с другими форматами, конвертации в onnx значительно проще за счет использования готового модуля. Для конвертации модели из keras в onnx был написан скрипт "convert_to_onnx.py".
* Формат IR: 
    Для использования модели в OpenVINO, наш onnx необходимо преобразовать в формат IR. Для этого импользуется скрипт mo.py из deployment_tools OpenVINO. Единственная ошибка, возникшая в ходе преобразования - неопределенность размера входа модели. Для решения проблемы нужно просто задать размер входа через "--input_shape". В нашем случае (генерация рукописных символов) вход - это шум размера 100 x 100.
* Написание sample:
    Для загрузки модели воспользуемя API OpenVINO:
    1. Создание объекта класса IECore. 
    2. Создание объекта класса IENetwork с параметрами – путями до модели.
    3. Загрузка созданного объекта класса IENetwork в IECore, что соответствует загрузке модели в плагин. 
  После загрузки модели, мы можем сгенерировать шум и выполнить для него 'predict' с последующей отрисовкой результата. Для отрисоки используется библиотека : matplotlib.pyplot. Код примера: "sample with IP.py"
  
Таким образом, мы обучили модель GAN, которая способна генерировать рукописные цифры, и запустили обученную сеть под OpenVINO.
В ходе разработки использовалась ОС Windows 10, а запуск и обучение моделей происходили на CPU "Intel Core i5-8300H". Сам запуск модели происходит достаточно быстро, но обучение (на 200 эпохах) занимает 2 - 3 часа. Поэтому для увеличения производительности можно обучать модель, используя GPU (библиотека Keras предоставляет такую возможность).