# IP2Vec
In this project I implement the `IP2Vec` model ([Ring et al. (2017)](https://ieeexplore.ieee.org/document/8215725)). In `IP2Vec`, the researchers aim to embed IP addresses in numeric vectors, using a method that builds on ideas from `Word2Vec` ([Mikolov et al. (2013a)](https://arxiv.org/abs/1301.3781)). Their key idea is to learn similarities by extracting available context information from network data, and by looking at traffic flows between IP addresses, ports, and protocols. Looking at temporal information allows them to convert such movements in networks into sequences, and apply similar methods to them. Just like in `Word2Vec`, two IP addresses will be considered close if they appear in similar contexts.  

To test their results, I will be using the CIC-Darknet2020 Internet Traffic data from [here](https://www.kaggle.com/datasets/peterfriedrich1/cicdarknet2020-internet-traffic).  

Most of the online resources that discuss `Word2Vec` describes it as just an `Embedding Layer` that is optimized via `Cross Entropy`. Unfortunately, it is more complex than that. Using the naive approach that was suggested in their original paper will lead to inefficient updation.  

A crucial difference between `Word2Vec` and `IP2Vec` is in the way the `context`s have been built. In `Word2Vec`, the context of a word includes both words that came before and after it. In `IP2Vec`, since we are trying to learn an IP resresentation from *traffic flows between machines*, it makes no sense adding previous traffics into the context vecrtor, as traffic moves only in one direction.  

In the following two figures are the context creation for `Word2Vec` and for `IP2Vec`. 

![Word2Vec Observations Generation, source: [Mikolov et al. (2013)](https://arxiv.org/abs/1301.3781)](https://i0.wp.com/datascientest.com/wp-content/uploads/2020/09/Capture-de%CC%81cran-2020-09-18-a%CC%80-09.27.50.png?w=842&ssl=1)

![IP2Vec Observations Generation](https://github.com/DavidHarar/Siamese-Networks-for-name-nickname-similarity/blob/main/images/2dcnn.jpg?raw=true)


Since every sentence has a limited number of words, each sentence produces only a handful of meaningful gradients. Consequentially, updating all the weights leads to sparse updation and a very slow training time.  
In order to avoid such issues, the original paper was accompanied by another paper that surveyed different training methods for the then-considered-large model. In [Mikolov et al. (2013b)](https://arxiv.org/pdf/1310.4546.pdf)) the original writers of `Word2Vec` introduce a few methods and tricks that allowed them to train `Word2Vec`. One method involves changing the loss function into a tree-based one, called *hierarchical softmax*. Another approach involves creating artificial sequences of words and training the network in a contrastive fashion, where the model learns to tell the true cases from the false ones. This method allows us to convert the costly `Cross Entropy` loss into a `Binary Cross Entropy` loss. It is called *negative sampling*, describing how to draw examples out of the empirical distribution of words to increase generalizability and avoid overfitting. A more thorough explenation of *negative sampling* can be found [here](http://mccormickml.com/2017/01/11/word2vec-tutorial-part-2-negative-sampling/).  

In this notebook I implement `IP2Vec` using negative sampling.
