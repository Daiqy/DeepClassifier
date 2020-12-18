# **DeepClassifer** 
DeepClassifier is a python package based on pytorch, which is easy-use and general for text classification task. You can install DeepClassifier by `pip install -U deepclassifier`。
If you want to know more information about DeepClassifier, please see the [**documentation**](https://deepclassifier.readthedocs.io/en/latest/). So let's start!🤩
> If you think DeepClassifier is good, please star and fork it to give me motivation to continue maintenance！🤩 And it's my pleasure that if Deepclassifier is helpful to you!🥰

## **installation**
Just like other Python packages, DeepClassifier also can be installed through pip.The command of installation is `pip install -U deepclassifier`.
## **Models**
Here is a list of models that have been integrated into DeepClassifier. In the future, we will integrate more models into DeepClassifier. Welcome to join us!🤩
1. **TextCNN:** [Convolutional Neural Networks for Sentence Classiﬁcation](https://www.aclweb.org/anthology/D14-1181.pdf) ,2014 EMNLP
2. **RCNN:** [Recurrent Convolutional Neural Networks for Text Classification](https://www.deeplearningitalia.com/wp-content/uploads/2018/03/Recurrent-Convolutional-Neural-Networks-for-Text-Classification.pdf),2015,IJCAI
3. **DPCNN:** [Deep Pyramid Convolutional Neural Networks for Text Categorization](https://ai.tencent.com/ailab/media/publications/ACL3-Brady.pdf) ,2017,ACL
4. **HAN:** [Hierarchical Attention Networks for Document Classiﬁcation](https://www.aclweb.org/anthology/N16-1174.pdf), 2016,ACL
5. **BERT:** [BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding](https://arxiv.org/pdf/1810.04805.pdf),2018, ACL
6. **BertTextCNN:** BERT+TextCNN
7. **BertRCNN:** BERT+RCNN
8. **BertDPCNN:** BERT+DPCNN
9. **BertHAN:** BERT+HAN
...
   
## Quick start
I wiil show you that how to use DeepClassifier below.🥰
```python
from __future__ import print_function
import torch
import torch.nn as nn
import numpy as np
from torch.utils.data import Dataset, DataLoader
import torch.optim as optim
from deepclassifer.models import BertTextCNN
from deepclassifer.trainers import Trainer
from tensorboardX import SummaryWriter

# ------------------------construct data-------------------------------#

# for train,dev,test data
class MyDataset(Dataset):
    def __init__(self, data, mask, label):
        self.data = data
        self.label = label
        self.mask = mask

    def __len__(self):
        return len(self.data)

    def __getitem__(self, item):
        item_data = self.data[item]
        item_mask = self.mask[item]
        item_label = self.label[item]

        return item_data, item_mask, item_label

# for predict
class my_dataset1(Dataset):
    def __init__(self, data, mask):
        self.data = data
        self.mask = mask

    def __len__(self):
        return len(self.data)

    def __getitem__(self, item):
        item_data = self.data[item]
        item_mask = self.mask[item]
        return item_data, item_mask

# train set
batch_size = 20
train_data = np.random.randint(0, 100, (100, 60))
train_mask = np.random.randint(0, 1, (100, 60))  # Avoiding padding token to participate in gradient calculation
train_label = torch.from_numpy(np.array([int(x > 0.5) for x in np.random.randn(100)]))
my_train_data = MyDataset(train_data, train_mask, train_label)
train_loader = DataLoader(my_train_data, batch_size=batch_size, shuffle=True)

# dev set
dev_data = np.random.randint(0, 100, (100, 60))
dev_mask = np.random.randint(0, 1, (100, 60))  # Avoiding padding token to participate in gradient calculation
dev_label = torch.from_numpy(np.array([int(x > 0.5) for x in np.random.randn(100)]))
my_dev_data = MyDataset(dev_data, dev_mask, dev_label)
dev_loader = DataLoader(my_dev_data, batch_size=batch_size, shuffle=True)

# test set
test_data = np.random.randint(0, 100, (100, 60))
test_mask = np.random.randint(0, 1, (100, 60))  # Avoiding padding token to participate in gradient calculation
test_label = torch.from_numpy(np.array([int(x > 0.5) for x in np.random.randn(100)]))
my_test_data = MyDataset(test_data, test_mask, dev_label)
test_loader = DataLoader(my_test_data, batch_size=batch_size, shuffle=True)
# ------------------------construct data-------------------------------#


# -------------------------define model--------------------------------#
# parameters of model
embedding_dim = 768  # if you use bert, the default is 768.
dropout_rate = 0.2
num_class = 2
bert_path = "/Users/codewithzichao/Desktop/bert-base-uncased/"

# model
my_model = BertTextCNN(embedding_dim=embedding_dim,
                       dropout_rate=dropout_rate,
                       num_class=num_class,
                       bert_path=bert_path)

# optimization
optimizer = optim.Adam(my_model.parameters())
# loss function
loss_fn = nn.CrossEntropyLoss()

# -------------------------define model--------------------------------#

# -------------------training testing,predicting-----------------------#

# parameters for training,dev,test
model_name = "berttextcnn"
save_path = "best.ckpt"
writer = SummaryWriter("logfie/1")
max_norm = 0.25
eval_step_interval = 20

my_trainer = Trainer(model_name=model_name, model=my_model, train_loader=train_loader, dev_loader=dev_loader,
                     test_loader=test_loader, optimizer=optimizer, loss_fn=loss_fn,
                     save_path=save_path, epochs=1, writer=writer, max_norm=max_norm,
                     eval_step_interval=eval_step_interval)

# training
my_trainer.train()
# print the best F1 value on dev set
print(my_trainer.best_f1)

# testing
p, r, f1 = my_trainer.test()
print(p, r, f1)

# predict
pred_data = np.random.randint(0, 100, (100, 60))
pred_mask = np.random.randint(0, 1, (100, 60))
pred_data = my_dataset1(pred_data, pred_mask)  # Avoiding padding token to participate in gradient calculation
pred_data = DataLoader(pred_data, batch_size=1)
pred_label = my_trainer.predict(pred_data)
print(pred_label)

# -------------------training testing,predicting-----------------------#
```

## **Contact**
If you want any questions about DeepClassifier, welcome to submit issue or pull requests!