# Method
## General framework
- The generative model (CVAE) works independently from the main GNN, and is also trained separately, using just $A$ and $X$.
- The final output of the GNN is obtained by either:
	1. concatenating the forward pass computed for $X$ and $\bar{X}$ (Ex. forward pass for a GCN: $\sigma(\tilde{A}XW)$)
	2. computing the forward pass on the average of $X$ and $\bar{X}$
* Note: For more complex models such as the GAT, there are some additional steps are required before the concatenation or averaging step.
## Loss functions
Two kinds of loss functions are used for training the composite model:
#### Supervised Loss ($\mathcal{L}_{1}$):
$$\mathcal{L}_1=\frac{-1}{S}\sum_{s=1}^{S}\sum_{i\in K}{}Y_i\log Z_i^{(s)}$$
* Standard crossentropy-type loss
* $Z_i$'s are the output of the final GNN using both $X_v$ and $\bar{X}_v$ as the inputs
* $K$ is the set of training labels
* $S$ is the number of times augmented features $\bar{X}$ are generated
#### Consistency Regularized Loss ($\mathcal{L}_2$):
$$\mathcal{L}_2=\frac{1}{S}\sum_{s=1}^{S}\sum_{i=1}^{N} ||\bar{Z}_{i}'-Z_{i}^{(s)}||_{2}^{2}$$Where$$ \bar{Z}_{i}=\frac{1}{S}\sum_{s=1}^{S}Z_{i}^{(s)}\;\;,\;\;
\bar{Z}_{i}'=\frac{(Z_{i})^\frac{1}{T}}{\sum_{c=1}^{C}(Z_{ic})^{\frac{1}{T}}}$$
* This loss uses the "sharpening trick" defined in [3], which aims to minimize the entropy of predictions
* $T$ is a hyperparameter which adjusts the temperature of this categorical distribution
## Training and Inference
1. Training
	- First, the generative model is trained using $A$ and $X$
	- In each epoch, subsequently, $S$ samples of $\bar{X}$ are generated 
	- Losses $\mathcal{L}_1$ (and optionally $\mathcal{L}_2$) are computed using the $S$ outputs, with a regularization weight $\alpha$ (hyperparameter)
2. Validation
	- For computing validation loss, $\bar{X}$ is generated again (post gradient descent) and then passed through the forward pass along with the original features $X$
3. Testing/Inference
	* The $\bar{X}$ with the best validation performance is chosen for the inference step using the trained NN
# What we plan on doing with it
1. Existing applications only include classification-type tasks, given the generalizability of the augmentation workflow, we can extend this method to edge level and graph level tasks.
	* Why it can work: from lectures, link prediction relies on the feature representation of nodes. With a better representation using augmented features generated using this method, link prediction models could possibly show improved performance.
	* Link prediction is a a very base-level task, and has applications in many other tasks, such as sub-graph representation learning [1], [2].
	* Additionally, the authors themselves suggest using the method on inductive learning tasks such as graph classification. We can evaluate its performance (after suitable tweaks to the training algorithm) on the standard molecule datasets (TU) to start with, and work towards applying it on larger datasets.
3. Current implementation of the generative model uses $X_u$, which is one-hop neighbour information for node $v$, and learns the distribution of local information conditioned on $X_v$. 
	- This can be thought of as a probabilistic version of message passing.
	- We see 2 potential ways to improve using this idea:
		1. We could possibly extend this further to include information from two/three-hop neighbours. We will try to better formulate this problem to show if it is better to go beyond one-hop neighbours.
		2. This generation is done once for each node and used as feature for that particular node. In regular GNNs, the message generation step is an identity mapping from node features to itself.
			- Can we leverage this CVAE to generate a different message for each neighbour of a given node, which can break the symmetry even better? 
4. Experiment with generative models other than CVAEs.
# Code
The repository can be found at https://github.com/SongtaoLiu0823/LAGNN
## Status
- We are able to run the scripts provided in the official repository, even though the library versions do not match exactly (torch 2.0.1+cu117 instead of torch 1.9.0+cu102, python 3.8.17 instead of 3.6.8)
- Due to the (very) slow transfer speeds from the Stanford OGB dataset library, we will have to look for alternative sources for the datasets.
- Even if we do find alternative sources, however, we will work only with the ogb-arxiv, ogb-protein and ogb-products datasets (79MB, 215MB, and 1.4GB respectively), since the size of the ogb-papers100 dataset is very large (56GB).
# References
[1] Louis, Paul, Shweta Ann Jacob, and Amirali Salehi-Abari. "Simplifying Subgraph Representation Learning for Scalable Link Prediction." _arXiv preprint arXiv:2301.12562_ (2023).
[2] Kwak, Heeyoung, and Hyunkyung Bae Kyomin Jung. "Subgraph representation learning with hard negative samples for inductive link prediction." _ICASSP 2022-2022 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)_. IEEE, 2022.
[3] Berthelot, David, et al. "Mixmatch: A holistic approach to semi-supervised learning." _Advances in neural information processing systems_ 32 (2019).