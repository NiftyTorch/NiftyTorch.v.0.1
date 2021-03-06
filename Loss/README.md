# Loss

The Loss module consists a collection of loss functions which can be used for learning model and optimizing the weights.

## Modules 

### Weighted Cross Entropy

The `crossentropyloss` loss function weights probabilities for each class with weighted assigned by the user and then normalized by total weight.<br>

Parameters:
<ul>
<li>weight (torch.FloatTensor,default = None) : The alpha is the weight to be used as a multiplier for probabilities of each class.
</ul>

Usage:

```python
from niftytorch.loss.losses import crossentropyloss
import torch
import numpy as np
num_classes = 32
weight = np.random.rand(num_classes)
input = torch.random.rand(64,num_classes)
output = torch.zeroes(64,num_classes)
loss = crossentropyloss(weight = weight)
print(loss(input,output))
```

### Focal Loss

The idea behind `focalloss` is to not only weight the probabilities according to class imbalance, but also take into consideration the difficulty of classifying the example. 

Parameters:
<ul>
<li>num_class (int,required): The number of classes in the problem.
<li>alpha (float,default = None) : The alpha is the weight to be used as a multiplier for probabilities of each class.
<li>gamma (float,default = 2.0): The gamma is the exponent to be used in the probabilities.
<li>balance_index (int,default = -1): This is the index which needs to be balanced or has the imbalanced (This is used if alpha is a float instead of ndarray).
<li>smooth (float,required): The smoothening factor is used to smoothen ground truth labels.
<li>size_average (boolean,default = True): The size_average tells whether to average the loss if true else it the sum is returned.
</ul>

Usage:

```python
from niftytorch.loss.losses import focalloss
import torch
import numpy as np
num_classes = 32
alpha = np.random.rand(num_classes)
input = torch.random.rand(64,num_classes)
output = torch.zeroes(64,num_classes)
loss = focalloss(num_class = num_classes,alpha = alpha,gamma = 2.0,balance_index = 1,smooth = 0.1,size_average = True)
print(loss(input,output))
```

### Focal Dice Loss

The idea behind `focaldiceloss` is to not only weight the probabilities according to class imbalance, but also take into consideration the difficulty of classifying the example by 

Parameters:
<ul>
<li>alpha (float,required): The alpha is the weight to be used as a multiplier for dice loss coefficient of each class.
<li>beta (float,required): The beta is the exponent to be used in the probabilities.
<li>eps (float,required): It is a really small number which decides which used along with denominator in dice loss to avoid division by zero error. 
<li>num_class (int,required): The number of classes in the problem.
</ul>

Usage:

```python
from niftytorch.loss.losses import focaldiceloss
import torch
import numpy as np
num_class = 32
alpha = torch.random.rand(num_class)
input = torch.random.rand(64,num_class)
output = torch.zeroes(64,num_class)
loss = focaldiceloss(alpha = alpha,gamma = 2.0,eps = 1e-8,num_class = num_class)
print(loss(input,output))
```

### Tversky Loss

The `tverskyloss` function is used to weight false positive and false negative in the loss.

Parameters:
<ul>
<li>alpha (float,required): The alpha is the weight to be used as a multiplier for dice loss coefficient of each class.
<li>beta (float,required): The beta is the exponent to be used in the probabilities.
<li>eps (float,required): It is a really small number which decides which used along with denominator in dice loss to avoid division by zero error.
</ul>

Usage:

```python
from niftytorch.loss.losses import tverskyloss
import torch
import numpy as np
num_class = 32
alpha = torch.random.rand(num_class)
input = torch.random.rand(64,num_class)
output = torch.zeroes(64,num_class)
loss = tverskyloss(alpha = alpha,gamma = 2.0,eps = 1e-8)
print(loss(input,output))
```

### Contrastive Loss

The `contrastiveloss` function is used in few shot learning paradigm. The inputs of the contrastive loss are two input tensors and target tensor. The target is 0 if they're of different class else it is 1.

Parameters:
<ul>
<li>margin (float,required): The margin is the maximum allowed to distance between the input distances.
<li>eps (float,default = 1e-9): It is a really small number which decides which used along with denominator in dice loss to avoid division by zero error. 
<li>size_average (Boolean,default = True): If true, then the loss is averaged or else the sum of the loss is returned.
</ul>

Usage:

```python
from niftytorch.loss.losses import contrastiveloss
import torch
margin = 10.0
input1 = torch.random.rand(64,1024)
input2 = torch.random.rand(64,1024)
output = torch.zeroes(64) #assuming they're from different class if you they're from same class use torch.ones(64,num_class)
loss = contrastiveloss(margin = margin,eps = 1e-8,size_average = True)
print(loss(input1,input2,output))
```

### Triplet Loss

The `tripletloss` is used in few shot learning paradigm. The inputs of the triplet loss are two tensor of different classes and an anchor tensor. The distance between the anchor and positive, the distance between the anchor and negative is used assign the class for the anchor.

Parameters:
<ul>
<li>margin (float,required): The margin is the maximum allowed to distance between the input distances.
<li>size_average (Boolean,default = True): If true, then the loss is averaged or else the sum of the loss is returned.
</ul>

Usage:

```python
from niftytorch.loss.losses import tripletloss
import torch
anchor =  torch.random.rand(64,1024) #embedding for anchor
positive = torch.random.rand(64,1024) #embedding for positive sample
negative = torch.random.rand(64,1024) #embedding for negative sample
loss = tripletloss(margin = margin,size_average = True)
print(loss(anchor,positive,negative))
```

### Lovaz softmax loss

Parameters:

<ul>
<li>per_image (bool,default = True): The parameters tells if the loss has to be calculated for all the parameters.
<li>ignore (int,default = None): The labels which have value same as ignore is removed.
<li>classes (list/string,default = 'present'): If the value is equal to 'all' then all classes are considered else if for 'present' the loss is computed for classes present in labels, or a list of classes to average.
</ul>

Usage:

```python
from niftytorch.loss.losses import lovaszsoftmaxloss
import torch
batch = 16
height = 128
width = 128
logits =  torch.random.rand(batch,height,width) #logits
labels = torch.zeros(batch,height,width) #labels
loss = lovaszsoftmaxloss()
print(loss(logits,labels))
```

### Soft N cut Loss

Parameters:

<ul>
<li>k (int,required): The number of classes.
<li>input_size (int,required): The height/width of the image.
</ul>

Usage:

```python
from niftytorch.loss.losses import softncutloss
import torch
k = 4
input_size = 128
batch = 16
logits =  torch.random.rand(batch,k,input_size,input_size) #logits
labels = torch.zeros(batch,k,input_size,input_size) #labels
loss = softncutloss(k = k,input_size = input_size)
print(loss(logits,labels))
```

### Soft N cut Loss

Parameters:

<ul>
<li>k (int,required): The number of classes.
<li>input_size (int,required): The height/width of the image.
</ul>

Usage:

```python
from niftytorch.loss.losses import softncutloss
import torch
k = 4
input_size = 128
batch = 16
logits =  torch.random.rand(batch,k,input_size,input_size) #logits
labels = torch.zeros(batch,k,input_size,input_size) #labels
loss = softncutloss(k = k,input_size = input_size)
print(loss(logits,labels))
```

### TotalVariation

Parameters:

<ul>
  <li>tv_weight (torch.FloatTensor,required): the weight to be given in each dimension.
</ul>

Usage:

```python
from niftytorch.loss.losses import totalvariation
import torch
tv_weight = torch.FloatTensor([0.2,0.3,0.4])
logits =  torch.random.rand(batch,k,input_size,input_size) #logits
labels = torch.zeros(batch,k,input_size,input_size) #labels
loss = totalvariation(tv_weight = tv_weight)
print(loss(logits,labels))
```

### PSNR

Parameters:

<ul>
  <li>max_val (Float,required): the signal value.
</ul>

Usage:

```python
from niftytorch.loss.losses import PSNR
import torch
max_val = 11.2
batch = 16
logits =  torch.random.rand(batch,10) #logits
labels = torch.zeros(batch,10) #labels
loss = PSNR(max_val = max_val)
print(loss(logits,labels))
```

### Large-Margin Softmax Loss

Parameters:

<ul>
  <li>input_dim: The size of features.
  <li>num_classes: The number of classes in classifier
  <li>margin: The margin size
</ul>


Usage:

```python
from niftytorch.loss.losses import lsoftmax
import torch
batch = 16
input_dim = 128
num_classes = 2
margin = 2
features =  torch.random.rand(batch,input_dim) #logits
labels = torch.zeros(batch,num_classes) #labels
loss = lsoftmax(input_dim = input_dim,num_classes = num_classes,margin = 2)
print(loss(features,labels))
```

### Additive Margin Softmax

Parameters:

<ul>
  <li>in_feats (int,required): The size of features.
  <li>num_class (int,default = 10): The number of classes in classifier.
  <li>gamma (float,default = 0.3): The margin size.
  <li>it (float,default = 0): 
  <li>lambdamin = 5.0
  <li>lambdamax = 1500
  <li>lamb = 1500
  <li>device (string,default = 'cuda:2'): The device where gpu is present.
</ul>


Usage:

```python
from niftytorch.loss.losses import amsoftmax
import torch
batch = 16
infeat = 128
num_class = 2
device = 'cuda:1'
gamma = 0
features =  torch.random.rand(batch,input_dim) #logits
labels = torch.zeros(batch,num_classes) #labels
loss = amsoftmax(in_feats = in_feats,num_classes = num_classes,m = 2,s = 2,device = 'cuda:2')
print(loss(logits,labels))
```

### Angular Loss

Parameters:

<ul>
  <li>l2_reg (float,default = 0.02): The l2 loss coefficient.
  <li>angle (int,default = 50): The angle between positives and negatives.
  <li>lambda_ang (float,default = 0.02): The angle hyperparameter.
</ul>


Usage:

```python
from niftytorch.loss.losses import angularloss
import torch
batch = 16
l2_reg = 1e-4
angle = 20
lambda_ang = 2.5
negative_examples = 4
positive =  torch.random.rand(batch,128)
anchor =  torch.random.rand(batch,128)
negative =  torch.random.rand(batch,negative_examples,128)
loss = angularloss(l2_reg=l2_reg, angle = angle, lambda_ang = lambda_ang)
print(loss(positive,anchor,negative))
```

### Circle Loss

Parameters:

<ul>
  <li>m (float,default = 0.02): The margin.
  <li>gamma (int,default = 50): The scaling factor.
  <li>
</ul>


Usage:

```python
from niftytorch.loss.losses import circleloss
import torch
batch = 16
m = 2
gammma = 2
positive =  torch.random.rand(batch,128) #logits
negative = torch.zeros(batch,128) #labels
loss = circleloss(m = m,gamma = gamma)
print(loss(positive,negative))
```

### Fast AP Loss

Parameters:

<ul>
  <li>bin_size (int,required): The margin.
  <li>start_bin (float,required): The start bin value.
  <li>end_bin (float,required): The end bin value.
</ul>


Usage:
```python
from niftytorch.loss.losses import fastaploss
import torch
batch = 16
bin_size = 16 
start_bin = 0
end_bin = 4
num_classes = 2
output =  torch.random.rand(batch,128)
pos_output = torch.zeros(batch,128)
neg_output = torch.zeros(batch,128)
labels = torch.zeros(batch,num_classes)
loss = fastaploss(bin_size = bin_size,start_bin = start_bin,end_bin = end_bin)
print(loss(output,pos_output,neg_output,labels))
```



### Lifted Loss

Parameters:

<ul>
  <li>margin (int,default = 1): The margin.
</ul>


Usage:
```python
from niftytorch.loss.losses import liftedloss
import torch
batch = 16
margin = 1
features =  torch.random.rand(batch,128)
labels = torch.zeros(batch,num_classes)
loss = liftedloss(margin = margin)
print(loss(features,labels))
```

### Proxy Anchor

Parameters:

<ul>
<li>nb_classes: The number of classes
<li>device: The device in which classifier should be stored
<li>sz_embed: The sz_embed embedding size of the input tensor
<li>mrg (float,default = 0.1): the margin
<li>alpha (int,default = 32): the scaling factor
</ul>

Usage:

```python
from niftytorch.loss.losses import proxyanchor
import torch
batch = 16
nb_classes = 2
device = 'cuda:2'
sz_embed = 128
mrg = 0.1
alpha = 32
features =  torch.random.rand(batch,128)
labels = torch.zeros(batch,num_classes)
loss = proxyanchor(nb_classes = nb_classes,device = device,sz_embed = sz_embed,mrg = mrg,alpha = alpha)
print(loss(features,labels))
```

### Proxy NCA


<ul>
<li>nb_classes: The number of classes
<li>sz_embedding: The embedding size
<li>infeat (int, required): The size of the input features
<li>device (string, default = 'cuda:0'): The device in which the embedder is to be placed
<li>smoothing_const (float, default = 0.1): The smoothening constant
<li>scaling_x (int, default = 1): The scaling factor for norm of proxies
<li>scaling_p (int,default = 3): The scaling factor for norm of input features.
</ul>

```python
from niftytorch.loss.losses import proxynca
import torch
batch = 16
nb_classes = 2
device = 'cuda:2'
sz_embedding = 128
infeat = 128
features =  torch.random.rand(batch,infeat)
labels = torch.zeros(batch,num_classes)
loss = proxyanchor(nb_classes = nb_classes,device = device,sz_embedding = sz_embedding,infeat = infeat)
print(loss(features,labels))
```
