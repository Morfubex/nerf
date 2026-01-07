Было изменено только models/depth_nerfacto, а именно были добавлены функции потерь L1, L2, HUBER, а также стратегии их внедрения в обучение (с постоянным весом, линейном возрастающим, с экспонцианальным затуханием и как гауссово распределение 0->1->0)

Для того, чтобы запустить обучение, необходимо:
1) Поставить себе nerfstudio (см. инструкцию https://docs.nerf.studio/quickstart/installation.html; рекомендуется использовать ОС Linux)
2) Скачать датасет с имеющимися данными о глубине. Рекомендуется использовать датасет SDFStudio (на нем и было протестировано). Для скачивания датасета необходимо сначала активировать среду nerfstudio, а после прописать "ns-download-data sdfstudio"
3) Для запуска обучения прописать "ns-train depth-nerfacto data /*путь/dtu-scan65 sdfstudio-data"

Полезные команды:

--vis tensorboard - отключение визуализации и включение графиков, картинок и тп (после обучения можно перейти в меню просмотра командой tensorboard --logdir outputs/)

--pipeline.model.depth-supervision (L1/L2/HUBER/DSNERF) - выбор лосс функции

--pipeline.model.depth_weight_mode (CONSTANT, LINEAR, DECAY, PEAK) - выбор страгеии включения в обучение

--max-num-iterations 6000 - кол-во шагов

--steps-per-eval-all-images 2000 - интервал построения оценок

--pipeline.model.depth-loss-mult (default 0.001) - коэффициент включения лоссов по глубине в общий лосс

--steps-per-eval-all-images (default 2000) - коэффициент для LINEAR стратегии (через сколько шагов выйдет на коэффициент alpha)

--pipeline.model.depth_decay_rate (default 0.99985) - коэффициент экспоненты для DECAY стратегии

--pipeline.model.depth_peak_step (default 10000) - шаг пика для PEAK стратегии

--pipeline.model.depth_peak_sigma (default 5000) - ширина пика для PEAK стратегии

Больше команд с описанием можно найти по ns-train depth-nerfacto --help

Пример запуска (data (...) sdfstudio-data обязательно должны быть в конце!): ns-train depth-nerfacto   --max-num-iterations 6000   --vis tensorboard  --steps-per-eval-all-images 2000  --pipeline.model.depth-loss-mult 0.001 --pipeline.model.depth-supervision L1 --pipeline.model.depth_weight_mode PEAK   --project-name depth-supervision-study  --experiment-name dtu-scan65-depth-nerfacto  --method-name dsnerf-constant   data /media/mrfx/Mrfx/MRFX/Programms/anaconda3/data/sdfstudio/sdfstudio-demo-data/dtu-scan65 sdfstudio-data

Предлагаю протестировать все 4 режима с 4 страгеиями для каждого, итого 16 запусков.