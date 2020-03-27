# ludwig学习

[TOC]

## install
install it by building the source code from the repository:

```
git clone https://github.com/uber/ludwig
cd ludwig
virtualenv -p python3 venv
source venv/bin/activate
pip install -r requirements.txt
python -m spacy download en
python setup.py install
```



## demo

ludwig主要提供了模型训练和模型预测，输出输入的载体是csv文件，csv文件保存了表格形式的数据，第一行标示了每列数据的名字。项目配置记录在配置文件，配置文件记录了对应csv文件每列的输入列名和输出列名，以及数据类型。

### config&csv

配置和数据两者分别为yaml文件和csv文件。配置文件记录了对应csv文件每列的输入列名和输出列名，以及数据类型（包括二进制数，数据，类别标签，文本，图片等）

常用的数据类型：

- binary
- numerical     数字
- category      类别
- set
- bag
- sequence
- text
- timeseries
- image

配置文件包含了输入输出和对应的预处理函数，还有训练超参数，例如epochs和训练批次大小batch_size。

csv文件保存了表格形式的数据，第一行标示了每列数据的名字。




### train

``` bash

python ludwig/ludwig/cli.py train --data_csv data/boston_house_price/boston_housing.csv --model_definition "{input_features: [{name: CRIM, type: numerical},{name: ZN, type: numerical},{name: INDUS, type: numerical},{name: CHAS, type: numerical},{name: NOX, type: numerical},{name: RM, type: numerical},{name: AGE, type: numerical},{name: DIS, type: numerical},{name: RAD, type: numerical},{name: TAX, type: numerical},{name: PTRATIO, type: numerical},{name: B, type: numerical}, {name: LSTAT, type: numerical}], output_features: [{name: PRICE, type: numerical}]}"

python ludwig.py train --data_csv data/titanic/train.csv --model_definition "{input_features: [{name: class, type: category},{name: age, type: category},{name: sex, type: category}], output_features: [{name: survived, type: category}]}"

python ludwig/ludwig/cli.py  train --data_csv data/titanic/train.csv --model_definition "{input_features: [{name: class, type: category},{name: age, type: category},{name: sex, type: category}], output_features: [{name: survived, type: category}]}" --model_name titan --output_directory res_titan 

ludwig train --data_csv path/to/file.csv --model_definition "{input_features: [{name: doc_text, type: text}], output_features: [{name: class, type: category}]}"
ludwig train --data_csv data/titanic/train.csv --model_definition "{input_features: [{name: class, type: category},{name: age, type: category},{name: sex, type: category}], output_features: [{name: survived, type: category}]}"

ludwig train --data_csv "E:\nnCollect\cifar10b.csv" --model_definition "{input_features: [{name: image_path, type: image,encoder: stacked_cnn}], output_features: [{name: tags, type: category}]}"

```

### predict
``` bash
ludwig predict --data_csv data/train.csv --model_path results/experiment_run_2/model
```
### visualize

```
ludwig visualize --visualization learning_curves --training_statistics results/experiment_run_2/training_statistics.json
ludwig visualize --visualization compare_performance --training_statistics results/experiment_run_2/training_statistics.json
```

* learning_curves
* compare_performance
* compare_classifiers_performance_from_prob
* compare_classifiers_performance_from_pred
* compare_classifiers_performance_subset
* compare_classifiers_performance_changing_k
* compare_classifiers_multiclass_multimetric
* compare_classifiers_predictions
* compare_classifiers_predictions_distribution
* confidence_thresholding
* confidence_thresholding_data_vs_acc
* confidence_thresholding_data_vs_acc_subset
* confidence_thresholding_data_vs_acc_subset_per_class
* confidence_thresholding_2thresholds_2d
* confidence_thresholding_2thresholds_3d
* binary_threshold_vs_metric
* roc_curves
* roc_curves_from_prediction_statistics
* calibration_1_vs_all
* calibration_multiclass
* confusion_matrix
* frequency_vs_f1

### ludwig.help
``` bash
(base) D:\projects\githubs\ludwig>ludwig --help
usage: ludwig <command> [<args>]

Available sub-commands:
   experiment            Runs a full experiment training a model and testing it
   train                 Trains a model
   predict               Predicts using a pretrained model
   visualize             Visualizes experimental results
   collect_weights       Collects tensors containing a pretrained model weights
   collect_activations   Collects tensors for each datapoint using a pretrained model

ludwig cli runner

positional arguments:
  command     Subcommand to run

optional arguments:
  -h, --help  show this help message and exit
```

##　src

命令行入口文件位于 /ludwig/cli.py

### dir

``` bash
# 目录
data , features,models,utils,contribs
# 文件
E:\NNCOLLECT\LUDWIG\LUDWIG\LUDWIG
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
│     comet.py
│     __init__.py
│   
├─data
│     concatenate_datasets.py
│     dataset.py
│     dataset_synthesyzer.py
│     postprocessing.py
│     preprocessing.py
│     split_dataset.py
│     __init__.py
│   
│
├─features
│     audio_feature.py
│     bag_feature.py
│     base_feature.py
│     binary_feature.py
│     category_feature.py
│     date_feature.py
│     feature_registries.py
│     feature_utils.py
│     h3_feature.py
│     image_feature.py
│     numerical_feature.py
│     sequence_feature.py
│     set_feature.py
│     text_feature.py
│     timeseries_feature.py
│     vector_feature.py
│     __init__.py
│   
│
├─models
│  │  combiners.py
│  │  inputs.py
│  │  model.py
│  │  outputs.py
│  │  __init__.py
│  │
│  ├─modules
│        attention_modules.py
│        convolutional_modules.py
│        date_encoders.py
│        dense_encoders.py
│        embedding_modules.py
│        fully_connected_modules.py
│        h3_encoders.py
│        image_encoders.py
│        initializer_modules.py
│        loss_modules.py
│        measure_modules.py
│        optimization_modules.py
│        recurrent_modules.py
│        reduction_modules.py
│        sequence_decoders.py
│        sequence_encoders.py
│        __init__.py
├─utils
      algorithms_utils.py
      audio_utils.py
      batcher.py
      data_utils.py
      defaults.py
      h3_util.py
      html_utils.py
      image_utils.py
      math_utils.py
      metrics_utils.py
      misc.py
      nlp_utils.py
      print_utils.py
      strings_utils.py
      tf_utils.py
      time_utils.py
      visualization_utils.py
      __init__.py

```




## run

ludwig train 图像的识别率丝毫没有提升



``` bash
# cifar10
python.exe e:/nnCollect/ludwig/ludwig/cli.py train --data_csv "E:\nnCollect\ludwig\data\cifar10b.csv" --model_definition "{input_features: [{name: image_path, type: image,encoder: stacked_cnn}], output_features: [{name: tags, type: category}]}"

python.exe e:/nnCollect/ludwig/ludwig/cli.py  predict --data_csv "E:\nnCollect\ludwig\data\cifar10b.csv" --model_path results/experiment_run_5/model

python.exe e:/nnCollect/ludwig/ludwig/cli.py visualize --visualization learning_curves --training_statistics results/experiment_run_2/training_statistics.json

# boston_house_price
# titanic
# imdb 

```

### dir

results\experiment_run_5\description.json

results\experiment_run_5\training_statistics.json

results\experiment_run_5\model_hyperparameters.json

标签字典信息：results\experiment_run_5\train_set_metadata.json

训练过程数据:results\experiment_run_5\model\training_progress.json


## misc

### todo
- [ ] design：csv格式定义
- [ ] design：project文件定义，包括输入输出列，输出输入预处理
- [ ] design: ui ,csv文件选定时，新建对话框，显示前10行数据，重点显示行头，中间是中转组，左右分别是输入列组和输出列组，
- [ ] design: ui 通过左箭头和右箭头按钮实现属性批量配置。
- [ ] design: ui 引入csv文件编辑，包括重命名，类型名，
- [ ] debug: ludwig visual
- [ ] demo ludwig server
- [x] debug: ludwig output feature is one-hot label ?
- [x] demo: ludwig visual 
- [ ] demo: ludwig train  text classify
- [ ] demo: ludwig train  ltim 
- [x] demo: ludwig train titanic
- [x] demo: ludwig train cifar10

