I. НАЗВАНИЕ ПРОЕКТА
****************
Исследование учета данных о глубине в функции потерь при обучении NeRF


II. УЧАСТНИКИ
****************
1. Никита Дмитриевич Дискулцу
2. Дмитрий Игоревич Маслов


III.ОПИСАНИЕ ПРОВЕДЕННОГО ИССЛЕДОВАНИЯ
******************************************
В рамках проекта исследуется влияние включения данных о глубине сцены (depth information) в функцию потерь при обучении нейронных полей излучения (NeRF).
Рассматриваются различные способы интеграции depth-сигнала в процесс оптимизации, включая:

-использование различных функций потерь по глубине (L1, L2, Huber);

-стратегии взвешивания depth-loss:
CONSTANT, LINEAR, DECAY (экспоненциальное затухание), PEAK (гауссов профиль 0→1→0).

Целью исследования является анализ влияния depth-loss на:

1. точность восстановления геометрии сцены;

2. фотореалистичность синтезируемых изображений.

Эксперименты проводятся на реальной сцене с доступной информацией о глубине при:

1. фиксированной архитектуре модели NeRF;

2. одинаковых параметрах обучения;

3. идентичном датасете для всех сравнений.

Основные изменения реализованы в модуле models/depth_nerfacto, где были добавлены новые функции потерь и механизмы управления их вкладом в общий лосс в процессе обучения.

IV.ДЕМОНСТРАЦИЯ
******************
Скринкаст с исполнением кода на Youtube: (ДОБАВИТЬ ССЫЛКУ)


V.УСТАНОВКА И РАЗВЕРТЫВАНИЕ
*******************************
Для воспроизведения экспериментов требуется стандартная среда для обучения NeRF-моделей.

Среда выполнения

ОС: Linux

Аппаратное обеспечение: GPU (рекомендуется NVIDIA с CUDA)

Шаги установки:

1. Установить nerfstudio согласно официальной инструкции:
~~~~~~~~~~~~~~~~~~~~
https://docs.nerf.studio/quickstart/installation.html
~~~~~~~~~~~~~~~~~~~~
2. Установить зависимости (PyTorch, NumPy, tqdm и др.) — автоматически через nerfstudio.

3. Скачать датасет с данными о глубине (используется SDFStudio):

~~~~~~~~~~~~~~~~~~~~
ns-download-data sdfstudio
~~~~~~~~~~~~~~~~~~~~

Запуск обучения:

~~~~~~~~~~~~~~~~~~~~
ns-train depth-nerfacto data <path>/dtu-scan65 sdfstudio-data
~~~~~~~~~~~~~~~~~~~~

VI.ЗАПУСК И ИСПОЛЬЗОВАНИЕ
********************
Пример запуска:
~~~~~~~~~~~~~~~~~~~~
ns-train depth-nerfacto \
--max-num-iterations 30000 \
--vis tensorboard \
--steps-per-eval-all-images 2000 \
--pipeline.model.depth-loss-mult 0.001 \
--pipeline.model.depth-supervision L1 \
--pipeline.model.depth_weight_mode PEAK \
--project-name depth-supervision-study \
--experiment-name dtu-scan65-depth-nerfacto \
--method-name dsnerf-constant \
data /media/.../dtu-scan65 sdfstudio-data
~~~~~~~~~~~~~~~~~~~~

NB: data (...) sdfstudio-data должны указываться в конце команды.


Основные параметры

--vis tensorboard — логирование метрик и изображений

--pipeline.model.depth-supervision — выбор функции depth-loss
(L1 / L2 / HUBER / DSNERF)

--pipeline.model.depth_weight_mode — стратегия взвешивания
(CONSTANT / LINEAR / DECAY / PEAK)

--max-num-iterations — число итераций обучения

--pipeline.model.depth-loss-mult — коэффициент depth-loss

--pipeline.model.depth_ramp_up_ratio — параметр LINEAR стратегии

--pipeline.model.depth_decay_final — нижняя граница DECAY

--pipeline.model.depth_peak_ratio — центр пика PEAK

--pipeline.model.depth_peak_width_ratio — ширина пика PEAK


Полный список параметров:

~~~~~~~~~~~~~~~~~~~~
ns-train depth-nerfacto --help
~~~~~~~~~~~~~~~~~~~~


VII.ОПИСАНИЕ ПОЛУЧЕННЫХ РЕЗУЛЬТАТОВ
***********************************
Проведено сравнение методов с постоянным коэффициентом при depth-loss, 
а также анализ их поведения при различных стратегиях включения глубинной информации в процессе обучения.

Выявлен наиболее устойчивый и эффективный вариант, обеспечивающий улучшение геометрической согласованности реконструкции без деградации фотометрического качества. 
Результаты сопоставлены по метрикам ошибки глубины и визуальному качеству синтезируемых представлений сцены.


Проведено сравнение:

1. различных функций depth-loss при постоянном коэффициенте;

2. стратегий динамического включения depth-информации в процесс обучения.


Выявлен наиболее устойчивый и эффективный вариант, обеспечивающий:

1. улучшение геометрической согласованности реконструкции;

2. отсутствие деградации фотометрического качества изображений.


Сравнение выполнено по следующим метрикам:

1. depth MSE — среднеквадратичная ошибка глубины;

2. PSNR — Peak Signal-to-Noise Ratio;

3. SSIM — Structural Similarity Index;

4. LPIPS — Learned Perceptual Image Patch Similarity.


Описание визуальных материалов

All methods comparison
~~~~~~~~~~~~~~~~~~~~
![Depth MSE](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/all%20methods%20comparison/depth_mse.png)

![LPIPS](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/all%20methods%20comparison/lpips.png)

![PSNR](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/all%20methods%20comparison/psnr.png)

![SSIM](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/all%20methods%20comparison/ssim.png)



Constant comparison
~~~~~~~~~~~~~~~~~~~
![Depth alpha](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/constant%20comparison/depth_alpha.png)

![Depth MSE](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/constant%20comparison/depth_mse.png)

![LPIPS](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/constant%20comparison/lpips.png)

![PSNR](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/constant%20comparison/psnr.png)

![SSIM](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/constant%20comparison/ssim.png)

![Результаты](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/constant%20comparison/results.png)


DSNeRF strategy comparison
~~~~~~~~~~~~~~~~~~~~~~~~~~
![Depth alpha](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/dsnerf%strategy%comparison/depth_alpha.png)

![Depth MSE](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/dsnerf%strategy%comparison/depth_mse.png)

![LPIPS](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/dsnerf%strategy%comparison/lpips.png)

![PSNR](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/dsnerf%strategy%comparison/psnr.png)

![SSIM](https://github.com/Morfubex/nerf/blob/mini-nerfacto/pictures/dsnerf%strategy%comparison/ssim.png)

DASDSA