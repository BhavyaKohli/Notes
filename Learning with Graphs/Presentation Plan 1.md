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
		![[Pasted image 20230913220919.png]]
### How do we extend the paper (potentially)
- Existing applications only include classification-type tasks, can we extend this method to link prediction
	- Why it can work: from lectures, link prediction relies on the feature representation of nodes. With a better representation using augmented features, link prediction models could possibly perform better
	- Link prediction is a method which finds applications in many other fields
- 