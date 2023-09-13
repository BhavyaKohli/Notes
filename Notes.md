https://proceedings.mlr.press/v162/liu22s/liu22s.pdf
## Introduction
* Open problem: is local neighbourhood information "good enough" to create node representations?
	* Authors argue: limited number of neighbours restrict the expressive power of GNNs
	* More issues: if larger size of neighbourhood is chosen, it can lead to over-smoothing
	* Aim of paper: enrich information obtained from local neighbourhood only
* How do they approach this?
	* Use augmentation methods to generate more samples in the local neighbourhood
	* Local Augmentation: generating neighborhood features via a generative model conditioned on local structures and node features
* Main contributions: New flexible augmentation method to be used for generating more local neighbourhood features for improving GNN expressivity. Flexibility allows it to be applied on popular backbones

## Method
* Single generative model trained to model conditional distributions for the whole graph, not node-wise.
* What are the benefits of this?
	* Reduces computation cost
	* Scalability and generalization: if a new node is added, there is no need to train a whole new network, since the big model contains generalized information which can be used
	* Node feature vector can be used as an input to generate extra feature vectors specific to this node (input-dependence)
* Generative model used: Conditional VAE. $X_v$ is the "condition" (center node), and $X_u$ is the target distribution to be learnt (u = neighbours of v)
* During training, supply $(X_v, X_v)$ and maximize ELBO (variational training)
* During inference/generation, supply $X_v$ for a node v as the condition, sample from the latent space to get Xv'
* How is the main GNN trained? Passes made using the generated features $\bar{X}_v$ are concatenated with the passes made using the original $X_v$ (equation 6). Note: concatenation done along second axis (first is nodes)
* Alternatively, averaging can be done to not increase dimensionality 
* Similar concatenation/averaging methods can be used for the different architectures (ex. GAT)
## Loss functions
1. Supervised Loss ($\mathcal{L}_1$): 
	* Standard crossentropy-type loss
	* Zi's are the output of the final GNN using both Xv and Xv' as the inputs
2. Consistency Regularized Loss ($\mathcal{L}_2$):
	* Uses the "sharpening trick"
	* "T is a hyperparameter which adjusts the ”temperature” of this categorical distribution. The sharpening trick can reduce the entropy of the predictions."
### Training/Inference
Steps:
1. Train generative model using $A, X$
2. (training gnn) 
	1. Generate $\bar{X}$, obtain prediction $Z$ using both original+augmented
	2. Compute $\mathcal{L}_1$
	3. Optionally compute $\mathcal{L}_2$, get total loss $\mathcal{L} = \mathcal{L}_1 + \beta*\mathcal{L}_2$
	4. Perform gradient descent
3. (validation)
	1. Regenerate X', compute validation performance (loss/accuracy) using this
4. (inference)
	1. Select X' with smallest val_loss or highest val_acc -----> ?
	2. Choose this in the forward pass
### Complexity analysis
* Pre-training: O(|E|(F D + LD^2))
* GNN training: O(S|E|(F D + LD^2))

Note: pre-training runs for <10 epochs, does not add too much overhead
