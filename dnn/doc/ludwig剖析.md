# ludwig剖析

## 概览

### 目录结构
```
ludwig
└─ludwig
   └─ludwig
       ├─contribs
       ├─data
       ├─features
       ├─models
       │  └─modules
       └─utils
```



* ludwig：命令行接口
* data：dataset数据集处理
* features：处理各种特征，例如numercial/text/category
* models: 基于配置创建输入/输出/模型
  * inputs
  * outputs
* modules： 网络层定义
* utils





所有文件层次
```
E:\LUDWIG\LUDWIG\LUDWIG
│  api.py
│  cli.py
│  collect.py
│  constants.py
│  contrib.py
│  experiment.py
│  globals.py
│  NOTICE
│  predict.py
│  serve.py
│  test_performance.py
│  train.py
│  visualize.py
│  __init__.py
│
├─contribs
│  │  comet.py
│  └─  __init__.py
│
├─data
│  │  concatenate_datasets.py
│  │  dataset.py
│  │  dataset_synthesyzer.py
│  │  postprocessing.py
│  │  preprocessing.py
│  │  split_dataset.py
│  └─ __init__.py
│
├─features
│  │  audio_feature.py
│  │  bag_feature.py
│  │  base_feature.py
│  │  binary_feature.py
│  │  category_feature.py
│  │  date_feature.py
│  │  feature_registries.py
│  │  feature_utils.py
│  │  h3_feature.py
│  │  image_feature.py
│  │  numerical_feature.py
│  │  sequence_feature.py
│  │  set_feature.py
│  │  text_feature.py
│  │  timeseries_feature.py
│  │  vector_feature.py
│  └─ __init__.py
│
├─models
│  │  combiners.py
│  │  inputs.py
│  │  model.py
│  │  outputs.py
│  │  __init__.py
│  │
│  └─modules
│     │  attention_modules.py
│     │  convolutional_modules.py
│     │  date_encoders.py
│     │  dense_encoders.py
│     │  embedding_modules.py
│     │  fully_connected_modules.py
│     │  h3_encoders.py
│     │  image_encoders.py
│     │  initializer_modules.py
│     │  loss_modules.py
│     │  measure_modules.py
│     │  optimization_modules.py
│     │  recurrent_modules.py
│     │  reduction_modules.py
│     │  sequence_decoders.py
│     │  sequence_encoders.py
│     └─  __init__.py
└──utils
   │  algorithms_utils.py
   │  audio_utils.py
   │  batcher.py
   │  data_utils.py
   │  defaults.py
   │  h3_util.py
   │  html_utils.py
   │  image_utils.py
   │  math_utils.py
   │  metrics_utils.py
   │  misc.py
   │  nlp_utils.py
   │  print_utils.py
   │  strings_utils.py
   │  tf_utils.py
   │  time_utils.py
   │  visualization_utils.py
   └─ __init__.py

```



文件简介表格

| 文件             | 功能                     |
| ---------------- | ------------------------ |
| api              | LudwigModel定义          |
| cli              | 命令行主入口             |
| collect          | 命令行子入口             |
| predict          | 命令行子入口             |
| train            | 命令行子入口和full_train |
| serve            | 命令行子入口serve        |
| visualize        | 命令行子入口             |
| test_performance | 命令行子入口             |
| globals          |                          |
| experiment       | 命令行子入口             |
| constants        | 常量定义                 |
| contrib          |                          |
|                  |                          |
|                  |                          |
|                  |                          |
|                  |                          |
|                  |                          |
|                  |                          |
|                  |                          |
|                  |                          |
|                  |                          |
|                  |                          |
|                  |                          |



## 入口


工具函数： 
* is_on_master()区分master和slave
* merge_with_defaults深度合并字典
* train_set_metadata保存字典信息
* train/valid/test是{str:np.array}格式

1. cli.py :
   1. main()
   2. CLI(),
   3. CLI.train()
2. train.py: 
   1. cli()
   2. full_train()
      1. train.py: model_definition = merge_with_defaults(model_definition)
      2. description = get_experiment_description(model_definition )
      3. preprocessed_data = preprocess_for_training(model_definition)
         1. preprocessing.py: preprocess_for_training() //区分 dataFrame/csv/hdf5
         2. preprocess_for_training_by_type(model_definition,data_type,all_data_fp)
         3. _preprocess_csv_for_training(features=features,data_csv=all_data_fp,) 或 load_data(hdf5_file_path, input_features,output_features,)
         4. build_dataset(data_csv,features,global_preprocessing_parameters),build_dataset_df,
            1. build_metadata,CategoryBaseFeature::get_feature_meta
            2. build_data, handle_missing_values,CategoryBaseFeature::add_feature_data,feature_data
            3. get_split
            4. shuffle_training
            5. Dataset.py:: Dataset(training_set,model_definition['input_features'],model_definition['output_features'],train_set_metadata)
         5. def load_data( hdf5_file_path,input_feathers)
         6. return (train,valid,test,train_set_metadata)
      4. update_model_definition_with_metadata(model_definition, train_set_metadata) // 更新字典信息
      5. model, result = train( training_set=training_set, validation_set=validation_set, test_set=test_set,)
      6. contrib_command("train_save", experiment_dir_name)
      7. train()
         1. Model( model_definition['input_features'],model_definition['output_features'],model_definition['combiner'],model_definition['training'], model_definition['preprocessing'], use_horovod=use_horovod,random_seed=random_seed,debug=debug)
            1. use_horovod
            2. _build()
               1. topological_sort_feature_dependencies	
               2. inputs.py:build_inputs , build_single_input
               3. outputs.py: build_outputs,build_single_output
               4. optimization.py: optimize
         2. return model, model.train( training_set,validation_set=validation_set,test_set=test_set,...)
            1. initialize_session
            2. ProgressTracker
            3. initialize_batcher
            4. while progress_tracker.epoch < self.epochs:
               1. learning_rate_warmup
               2. while not batcher.last_batch():
               3. evaluation
               4. check_progress_on_validation
               5. save_weights

``` python
    # save description
    description = get_experiment_description(
        model_definition,
        data_csv=data_csv,
        data_train_csv=data_train_csv,
        data_validation_csv=data_validation_csv,
        data_test_csv=data_test_csv,
        data_hdf5=data_hdf5,
        data_train_hdf5=data_train_hdf5,
        data_validation_hdf5=data_validation_hdf5,
        data_test_hdf5=data_test_hdf5,
        metadata_json=train_set_metadata_json,
        random_seed=random_seed
    )
```

### 数据预处理

数据预处理主要分为：

1. 处理字典信息（build_metadata）
2. 生成数据（build_data）

不同type对应的特征，其多态通过查登记表base_type_registry字典实现，



CategoryBaseFeature：用于处理train的输入

CategoryInputFeature: 继承于CategoryBaseFeature，用于处理predict的输入？

CategoryOutputFeature: 继承于CategoryBaseFeature，用于处理predict的输出？

## misc

常用工具函数：

```python
def merge_dict(dct,merge_dct):
	return dct
```


Horovod简介
Horovod是Uber开源的又一个深度学习工具，它的发展吸取了Facebook "Training ImageNet In 1 Hour" 与百度 "Ring Allreduce" 的优点，可为用户实现分布式训练提供帮助。本文将简要介绍如何使用Horovod配合pytorch更高效地进行分布式训练。