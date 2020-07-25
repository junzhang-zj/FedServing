
## Introduction

The code of combiner to do **truth discovery** for the FedServing paper.

This repository includes:
1. `combiner_sgx`: contains the program to do truth discovery in SGX;
2. `outputs`: contains predictions output by participant models, stored as  `csv` files;
3. `outputs_combined`: contains predictions combined by the combiner;
3. `image`: contains scripts to build models of task MNIST and ImageNet;
3. `nlp`: contains scripts to build models of task 20News;
4. `utils`: contains tools to calculate accuracies of combined results.

 
## Tested environment and dependencies
1. Ubuntu 16.04
2. SGX SDK 2.5
3. SGX PSW
4. Pytorch 1.1.0
5. TensorFlow 1.12.2


## Truth discovery

To do truth discovery, enter `combiner_sgx` and run the following command:

```bash
./run.sh
```
The program will be compiled and executed. If executed correctly, the program will print logs as follows.
```
GEN  =>  App/Enclave_u.c
CC   <=  App/Enclave_u.c
CXX  <=  App/App.cpp
CXX  <=  App/sgx_utils/sgx_utils.cpp
LINK =>  app
GEN  =>  Enclave/Enclave_t.c
CC   <=  Enclave/Enclave_t.c
CXX  <=  Enclave/Enclave.cpp
LINK =>  enclave.so
<!-- Please refer to User's Guide for the explanation of each field -->
<EnclaveConfiguration>
    <ProdID>0</ProdID>
    <ISVSVN>0</ISVSVN>
    <StackMaxSize>0x400000</StackMaxSize>
    <HeapMaxSize>0x700000</HeapMaxSize>
    <TCSNum>10</TCSNum>
    <TCSPolicy>1</TCSPolicy>
    <DisableDebug>0</DisableDebug>
    <MiscSelect>0</MiscSelect>
    <MiscMask>0xFFFFFFFF</MiscMask>
</EnclaveConfiguration>
tcs_num 10, tcs_max_num 10, tcs_min_pool 1
The required memory is 49627136B.
Succeed.
SIGN =>  enclave.signed.so
Initialization takes 0.3644s
Task:MNIST
read data from: ../outputs/MNIST/query_4000/type_label/KNN.csv
read data from: ../outputs/MNIST/query_4000/type_label/SVM.csv
read data from: ../outputs/MNIST/query_4000/type_label/CNN.csv
read data from: ../outputs/MNIST/query_4000/type_label/RNN.csv
read data from: ../outputs/MNIST/query_4000/type_label/LR.csv
read data from: ../outputs/MNIST/query_4000/type_label/MLP.csv
Data reading takes 0.020489s
Combining results inside enclave...
Results combined! Exiting the enclave
Combination takes 0.010545s
Results are saved to ./../outputs_combined/combination_results_new/MNIST/noise_0/MNIST_4000_label_iter_16.csv
```
After the truth discovery is done, results will be saved in `outputs_combined`，saved as `csv` file.


Configuration can be found in `combiner_sgx/aggregator.h`. Please refer to the file to see the configurable fields.


## Get truth discovery accuracies



To get the truth discovery accuracy, a ground truth file should be prepared in advance.

We provide the ground truth files as well as the combined results used in our experiments in this repo. To obtain the accuracy, enter `utils` and run the following commands.
``` bash
python acc_utils.py
```
If the program executes correctly, accuracies will be printed as follows. Please refer to `acc_utils.py` for details.
```
============= MNIST =============
> noise level is : 0
> MNIST_1000_label_iter_20.csv          label - acc:    0.975000
> MNIST_4000_label_iter_20.csv          label - acc:    0.978250
> MNIST_7000_label_iter_20.csv          label - acc:    0.978000
> MNIST_1000_rank_iter_20.csv           rank - acc:     0.972000
> MNIST_4000_rank_iter_20.csv           rank - acc:     0.973500
> MNIST_7000_rank_iter_20.csv           rank - acc:     0.973286
> MNIST_1000_probs_iter_20.csv          probs - acc:    0.975000
> MNIST_4000_probs_iter_20.csv          probs - acc:    0.979500
> MNIST_7000_probs_iter_20.csv          probs - acc:    0.980429
```




## Turth discovery results

### Test accuracies of models in our Experiment

#### MNIST
| Model Name          | Test Accuracy (%) |
|---------------------|-------------------|
| KNN                 | 0.962             |
| SVM                 | 0.707             |
| Logistic Regression | 0.904             |
| MLP                 | 0.897             |
| RNN                 | 0.981             |
| CNN                 | 0.993             |

#### 20News
| Model Name    | Test Accuracy (%) |
|---------------|-------------------|
| Boost         | 0.740              |
| Bagging       | 0.660              |
| Decision Tree | 0.550              |
| Random Forest | 0.760              |
| SVM           | 0.820              |
| KNN           | 0.660              |
| CNN           | 0.730              |
| DNN           | 0.810              |
| RNN           | 0.760              |
| RCNN          | 0.720              |

#### ImageNet
| Model Name             | Test Accuracy (%) |
|------------------------|-------------------|
| AlexNet (PyTorch)      | 0.566             |
| VGG16 (PyTorch)        | 0.716             |
| VGG19 (PyTorch)        | 0.724             |
| Inception v3 (PyTorch) | 0.775             |
| GoogLenet (PyTorch)    | 0.698             |
| ResNet-50 (PyTorch)    | 0.762             |
| ResNet-101 (PyTorch)   | 0.774             |
| Densenet-169 (PyTorch) | 0.760             |
| MobileNet v2 (PyTorch) | 0.719             |
| VGG16 (Keras)          | 0.713             |
| VGG19 (Keras)          | 0.713             |
|  InceptionV3 (Keras)   | 0.779             |
|  ResNet50 (Keras)      | 0.749             |
|  DenseNet121 (Keras)   | 0.750             |
| MobileNet V2 (Keras)   | 0.713             |

### Accuracies of combined results

#### Results on MNIST (iteration = 20)
| Noise         | 0      |        |        |            | 3      |        |        |            | 5      |        |        |            |
|---------------|--------|--------|--------|------------|--------|--------|--------|------------|--------|--------|--------|------------|
| Sample number | label  | rank   | probs  | probs (KL) | label  | rank   | probs  | probs (KL) | label  | rank   | probs  | probs (KL) |
| 1000          | 0.975  | 0.972  | 0.975  | 0.875      | 0.936  | 0.742  | 0.951  | 0.324      | 0.272  | 0.253  | 0.467  | 0.124      |
| 4000          | 0.978  | 0.974  | 0.980  | 0.878      | 0.936  | 0.747  | 0.953  | 0.141      | 0.335  | 0.216  | 0.463  | 0.095      |
| 7000          | 0.978  | 0.973  | 0.980  | 0.878      | 0.937  | 0.754  | 0.954  | 0.097      | 0.274  | 0.232  | 0.469  | 0.101      |


### Results on 20News (iteration = 20)

| Noise         | 0      |        |        |            | 5      |        |        |            | 9      |        |        |            |
|---------------|--------|--------|--------|------------|--------|--------|--------|------------|--------|--------|--------|------------|
| Sample number | label  | rank   | probs  | probs (KL) | label  | rank   | probs  | probs (KL) | label  | rank   | probs  | probs (KL) |
| 1000          | 0.846  | 0.819  | 0.840  | 0.844      | 0.825  | 0.614  | 0.828  | 0.824      | 0.173  | 0.115  | 0.284  | 0.321      |
| 3000          | 0.850  | 0.826  | 0.849  | 0.847      | 0.830  | 0.610  | 0.826  | 0.821      | 0.147  | 0.111  | 0.277  | 0.320      |
| 5000          | 0.862  | 0.836  | 0.862  | 0.860      | 0.841  | 0.623  | 0.839  | 0.832      | 0.135  | 0.114  | 0.277  | 0.323      |


### Results on ImageNet (iteration = 20)
| Noise         | 0      |        |        |            | 7      |        |        |            | 14     |        |        |            |
|---------------|--------|--------|--------|------------|--------|--------|--------|------------|--------|--------|--------|------------|
| Sample number | label  | rank   | probs  | probs (KL) | label  | rank   | probs  | probs (KL) | label  | rank   | probs  | probs (KL) |
| 5000          | 0.777  | 0.752  | 0.775  | 0.720      | 0.757  | 0.067  | 0.750  | 0.718      | 0.591  | 0.0026 | 0.279  | 0.022      |
| 10000         | 0.779  | 0.754  | 0.777  | 0.735      | 0.757  | 0.067  | 0.752  | 0.704      | 0.010  | 0.0043 | 0.282  | 0.023      |
| 16000         | 0.790  | 0.764  | 0.789  | 0.713      | 0.766  | 0.071  | 0.768  | 0.713      | 0.010  | 0.0035 | 0.288  | 0.024      |



