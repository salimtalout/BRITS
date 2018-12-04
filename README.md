# README

## Prepare stage
* Install requirements for python2
* make three folders named ***raw***, ***json***, and ***result***
* unzip PhysioNet ([test-a.zip](https://physionet.org/physiobank/database/challenge/2012/set-a.zip)) and the label file [***Outcomes-a.txt***](https://physionet.org/physiobank/database/challenge/2012/Outcomes-a.txt) into raw folder.
* run input_process.py to generate the input file of NN

## Non-RNN methods
* run baseline_methods.py

## RNN-based methods
The rnn-based methods contain several parameters. Besides the epochs and batch size, we use ***hid_size*** to control the number of parameters and ***impute_weight***/***label_weight***. For example, to train RITS_I, run
```
python main.py --model rits_i --epochs 1000 --batch_size 64 --impute_weight 0.3 --label_weight 1.0 --hid_size 108
```

The imputed results will be saved in ***result*** folder. Based on the imputations, we could run lightgbm to do the two-stage test.
