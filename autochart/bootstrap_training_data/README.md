# Bootstrap Training Data

Data2Vis, our precedent for an automatic visualization model, bootstraps 215,000 pairs of datasets and charts from 11 datasets and 4300 human-made charts. 
Here's how they put it:
	Our training dataset is constructed from 4300 Vega-Lite visualizations examples, based on 11 distinct datasets. The exam- ples were originally compiled by [52] where the authors use the CompassQL [76] recommendation engine within Voyager2 [77] to generate charts with 1-3 variables, filtered to remove problematic instances. These charts are generated based on heuristics and rules which enumerate, cluster and rank visualizations according to data properties and perceptual principles [77]. While these examples contain a simplified range of transformations and do not encode any interactions, they represent valid Vega-Lite examples and conform to important perceptual principles enforced by rules within Voyager2. These characteristics make the dataset a suitable, low-complexity test bed for benchmarking our model’s performance on the task of learning to generate visualizations given only input data.
	We iteratively generate a source (a single row from the dataset) and target pair from each example file. Each example is then sampled 50 times (50 different data rows with the same Vega-Lite specification) resulting in a total of 215,000 pairs which are then used to train our model.
	
[CompassQL - Paper](https://idl.cs.washington.edu/papers/compassql/)
[CompassQL - Source](https://github.com/vega/compassql)




### Preprocessing
- From data2vis: Achieving these objectives using a character based sequence model can be resource intensive. While character based models re- sult in smaller vocabulary size and are more accurate for specialized domains, they also present challenges — a character tokenization strategy requires more units to represent a sequence and requires a large amount of hidden layers as well as parameters to model long term dependencies [8]. To address this issue and scaffold the learning process, we perform a set of transformations. First, we replace string and numeric field names using a short notation —
“str” and “num” in the source sequence (dataset). Next, a similar backward transformation (post processing) is eplicated in the tar- get sequence to maintain consistency in field names
