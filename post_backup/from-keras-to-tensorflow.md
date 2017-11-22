---
title: 从Keras转向TensorFlow
date: 2017-08-11 00:40:11
tags: [Keras, TensorFlow, Thinking]
categories: Deep Learning
---

写这篇的主要目的，是记录自己从Keras的使用转向TensorFlow的一个学习过程。

## 为什么要转

首先回答这个问题。在我刚刚接触Deep Learning的一段时间，觉得诸如Theano、TensorFlow的这些框架晦涩难懂，需要非常长的学习曲线。而在这时，Keras作为这两个框架的高层API实现，它对各个网络层的高层抽象，以及简单的配置过程，着实让人尝到了一些甜头。以至于，只要你按照输入输出的要求准备好数据，在按照网上的样例将网络层组合，在经过一段时间的训练后，模型就会得到非常好的结果。除去数据处理，以往TensorFlow需要数十行甚至上百行的代码，Keras只需十几行甚至几行就能搞定。因为这个原因，我在一些文本处理的任务（文本分类、语言模型等等）都使用Keras来实现。

然而，久而久之，问题出现了。Keras作为TensorFlow和Theano平台的API框架，它实在是太过抽象和贴心了。它为你包装好了所有的网络层（Convolution、RNN、Softmax等等)，为你配置并初始化了大量的参数。以至于你会越来越觉得Deep Learning就是一个黑盒子，你并不需要去理解其中的一些细节，这将会越来越是你对Deep Learning的印象越来越麻木。一个研究者，不去追求具体的实现而坐享其成，那和一条咸鱼有什么区别呢？

此外，当我在处理Seq2Seq的问题时，Keras并没有表现得十分优秀，由于其完美的包装导致这个实现变得有些难以实现，当然这也可能归结于我的编码能力的不足。而当我在这个问题难以解决时，转过头来看TensorFlow的实现，它那复杂的配置和冗长的网络层实现让我觉得晦涩难懂，TensorFlow官网示例中2个空格缩进的代码更是让人难以接受。这种心酸的感受就像是一个享受快乐生活的犹太人突然被抓进了集中营，一个华尔街的精英瞬间变成路边的乞丐。然而，为了完成当前的任务，必须接受眼前的现实。

## 为了转换所做的工作

当然，前面的这些都是闲话。俗话说，自己动手，丰衣足食。我开始频繁的翻阅TensorFlow的文档，甚至人工加Google将它翻译了一遍。然而我发现，这些示例的代码离真正运用到特定的任务还有一定的距离。这就像是典型的给你你个土豆，却要你做一顿丰盛的法国大餐一样。光凭这些示例而不去翻阅源码，很难有所掌握。需要注意的是，TensorFlow虽然对一些网络层、损失函数、优化器做了一定的包装，但是也多了一些权重、初始化的一些操作，这相比Keras来说，虽然需要写的代码更多，但是更加直观，至少能让人知道在这个网络层中发生了哪些操作，这一点是尤为重要的。

为了理解这些包装好的网络层，我需要查阅更多的资料。读论文是一件让人头疼的事情，尽管我拥有一定的读论文的能力，然而时间非常的有限。我开始关注一些网上的博客、专栏文章。然而，我估计大部分的博客撰写者自己也是一头雾水。他们的套路一般是引用一些经典的网络结构，使用几个常用的数据集，跑出一个较好的结果。关于其中的一些细节问题，鲜有人做详细的说明，我猜这大概是国内浮躁的研究环境导致的吧。在查找资料的过程中，有一些英文的博客值得学习，例如[colar's blog](http://colah.github.io/) 和 [wildml](http://www.wildml.com/)，前者给出了一些形象化的图解来说明网络的具体原理，后者通过一步步的numpy实现来讲解神经网络。此外，Stanford的[cs231n](http://cs231n.stanford.edu/)给了我巨大的帮助，我认为所有研究Deep Learning的人都应该去看一看，当然学习这个课程还需要一定的数学基础，其中的一些公式的推倒比较困难。

在偶然的依次浏览Youtube的过程中，我发现了一个[使用TensorFlow实现Neural Style Transfer](https://www.youtube.com/watch?v=LoePx3QC5Js)的视频，然后我点开up主的主页，发现了这一系列的[TensorFlow教程](https://www.youtube.com/watch?v=wuo4JdG3SvU&list=PL9Hr9sNUjfsmEu1ZniY0XpHSzl5uihcXZ)，以及整个教程的[GitHub代码](https://github.com/Hvass-Labs/TensorFlow-Tutorials)，细心观看几期视频后，我开始认识到我在长期的探索过程中终于发现了金子。他的讲解通俗易懂，他的代码拥有非常多的注释，他的一系列帮助函数能够让你更加直观的去探究网络中的一些权重，以及训练过程中发生的一些变化。这简直就是业界良心，比Udacity上的Deep Learning课程完美的多。

因此，在后续的文章中，我打算做一回搬运工，对这些代码做一定的优化，放到博客中作为记录，并且供后来者参考。

## 题外话

尽管我正在逐步探究一些原始的神经网络实现，但是Google似乎却在逐步的前进，TensorFlow 1.2开始提供了一套更加完善的API，也就是你可以像使用Keras那样来使用TensorFlow。更有甚至，Keras直接成为了TensorFlow的一个附属API模块。这样的发展大概是为了推进工业的应用考虑，降低AI开发门槛。但是，作为一个想要弄懂Deep Learning原理的学者，我们应该先去考虑它们的原理，因此，这些高层次的API就暂时搁置，等到原理弄懂之后，这些API的应用便得心应手了。