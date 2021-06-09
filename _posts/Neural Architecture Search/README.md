# Neural Architecture Search

This is a rising topics in Deep Learning. Normally there is a myth that with the increasing numbers of parameters, the network will work better. This is both true and false, because Deep Learning network will work better with more parameters **only** if all their parameters are being used to the best. That is to say, the network we often see everyday that is design for a general task have many redundant parameters that could both make our network heavier and harm the network performance due to the noise they might make.

Network searching has been people interest long ago even before Deep Learning. With basic Machine Learning algorithm, we often see algorithm like *Grid Search* or *A-star search* being used to select parameters. However, the fact that deep learning architecture has a long training time as well as a tons of hyper-parameters to be optimized, brute-force searching or grid-search might not be the best options. Ever since the first paper of Neural Architecture Search really took off which takes around 2000 GPU days (on a V100 GPU) to finish, there are many improvement and now it may take only 0.1 days (around 2-3 hours) to search and output a better performed network, making NAS easier than ever.

In short, it worths our attention to dive into the subject of Neural Architecture Search (NAS) due to reasons:

- It makes the **performance better** due to reduction of redundant parameters that inject noises into our model.
- It makes our model **lighter** due to fewer number of parameters in the network.
- It is becoming more and **more efficient** and can be used in industry.

****
## Some terms that are often coming up in NAS paper

### 1. Search space

A search space is the space that we will work on. It includes set of operations that might appear in the network along with the set of hyper-parameters like depth and width in a convolutional network. The goal of NAS is to find the best set of operations and hyper-parameters in the search space that are both light and highly-performed.

### 2. Search Strategy

This often refers to how we are going to update our algorithm with the information that we get from current candidate test, i.e. architecture improving algorithm. 

### 3. Evaluation Strategy

This refers to how we are going to evaluate the candidates at current time steps. For example: are we going to train each architecture from scratch and wait for it until convergence, or are we gonna early stop at some point or might share some parameters among the models.
