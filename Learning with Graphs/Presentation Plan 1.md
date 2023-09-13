# Official poster

<img src="https://icml.cc/media/PosterPDFs/ICML%202022/6974ce5ac660610b44d9b9fed0ff9548.png?t=1657490665.5703084">
# What to include
## Basic stuff
- Base explanation of the paper -  
	1. method: benefits over normal methods, basic flow 
	2. loss used: structure, motivation, about the optional consistency regularized loss
	3. workflow: training and inference steps
	4. etc
-  
## What we'll do
### Reproduce what has been done
Currently used on -
	1. **Semi-supervised**: 
		Basic citation networks: Cora, Citeseer, Pubmed (Node classification task)
	2. **Full-supervised**: 
		Open Graph Benchmark (OGB) datasets: ogbn-products, ogbn-proteins, ogbn-arxiv, and ogbl-collab (Node and Link classification tasks)
		Note: Fetching the OGB datasets takes far too long, possibly due to a server limitation (downloads from http://snap.stanford.edu/ogb/data/nodeproppred/). Till we find possible alternatives, we will aim to reproduce the results only from the first part. Even then papers100M will be skipped.
		![[ogb-server-dir.png]]
### How do we extend the paper (potentially)
- Existing applications only include classification-type tasks, can we extend this method to link prediction
	- Why it can work: from lectures, link prediction relies on the feature representation of nodes. With a better representation using augmented features, link prediction models could possibly perform better
	- Link prediction is a method which finds applications in many other fields
- Current implementation of the generative model uses $X_u$, which is one-hop neighbour information for node $v$, and learns the distribution of local information conditioned on $X_v$. This can be thought of as a probabilistic version of message passing. We could possibly extend this further to include information from two/three-hop neighbours. We will try to better formulate this problem to show if it is better to go beyond one-hop neighbours. (The authors have also expressed their interest in a similar direction in the conclusion section.)
- Experiment with generative models other than CVAEs. Normalizing Flows, using autoregressive-model-like methods after appropriate conditioning (introduce directed-ness, choose neighbourhood which follows 1-directional parent relations), etc.
- 