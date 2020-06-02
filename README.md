# Automated Data Visualization for COVID patient and virus data
## Automatically translate tabular data on patients and virus strains into graphs and charts for clinicians treating COVID-19 patients

### Need
COVID-19 cases vary in severity and clinical need from person to person, and even among people with similar ages and comorbidities. We are only beginning to understand the causes drive how the disease is expressed in a given patient. There is some emergent evidence that genetic differences bewteen patients interact with differently with different strains of the COVID-19 virus. And those interact with patient age, history, and comobidities.  

A visual representation of the patient's (and the virus') data can help clinicians make life-saving decisions. But visualizing this data takes time: selecting the most important fields, transforming and scaling data, selecting a useful chart type, selecting colors, etc. This is generally repetitive but highly skilled work. 

In this consulting project, the aim is to build a model that takes tabular data on patient and virus genomics in a variety of formats and automatically generates the most appropriate visualization (or set of visualizations) for the clinician. 

### Approach
Data2Vis ([github](https://github.com/victordibia/data2vis), [paper](https://arxiv.org/abs/1804.03126)) provides a precident for automatically translating tabular data into visualizations. I intend to modify Data2Vis for this purpose, specifically, to understand and generate visualizations of genomic data as well as other tabular data.  

Data2Vis in turn implements [seq2seq](https://github.com/google/seq2seq), a general model for translating a sequence of one thing to a sequence of another thing. It is a recurrent neural network, and the cannonical application is language translation. It runs on TensorFlow.

In the case of data2vis, the input is tabular data and the output are attributes of a chart: the most important data fields, the type of chart, data transformations and scales, colors, labels, etc.  Those chart attributes are then translated into a visualization grammer, like seaborn, ggplot2, D3, etc. Here is the model architecture, lifted from the data2vis github page: 

![data2vis architecture](https://github.com/victordibia/data2vis/blob/master/static/assets/datatransform.jpg?raw=true)

I've run into some difficulty so far in getting data2vis to run -- it requires old versions of TensorFlow, Python, and more, and setting up a compatible environment is tedious. Also, using more recent software will be more useful for the client. Finally, the old versions prohibit using google colab.
Right now, we're leaning towards building an LSTM model from scratch and training it on the same data sources as Data2Vis. 

### Outcome
We're trying for a working model capable of generating 6 different kinds of charts from diverse data inputs. 
A *minimum viable product* (1) identifies chart type and (2) selects features to chart. 
Client has requested that I focus on getting a model working and not on putting it into production. 
However, I'll want to make a compelling demo.  The data is private patient data and under NDA but I have written permission to share both the trained model and source code.

### Steps and things to try
- fork data2vis and get it working on example data, and some of my past research data
- try it out with some of the collaborative COVID data that the client has (this won't show up on github!) 
- train a model on client's training data
- transfer learning to adapt trained data2vis model to client's training data, compare to model trained on client's data alone
- validate model on collaborative COVID data 
- get clinician feedback on visualizations? 

## TL;DR

### Precedent 
- https://arxiv.org/pdf/1804.03126.pdf
- https://github.com/victordibia/data2vis
- https://idl.cs.washington.edu/papers/voyager2/


### Input data
Omic's search engine allows a user to ask a *question* and the returns *data*, in ???? format (note: check what format this comes in).  The world of possible data that it can return is very large. The challenge is to visualize lots of kinds of data.

### Model goals
Following the example from Data2Vis, the learning objectives are
- select a subset of fields to visualize
	- data2vis identifies 6 chart types: area, bar, circle, line, point, tick
- learn differences in data types across fields (numeric, string, temporal, ordinal, categorical etc; 
	- in our case, also types specific to genomic data)
- learn appropriate transformations to apply to a field given it's data type
	- data2vis identifies 3 transforms: aggregate, bin, time-unit

### Training data
We'll draw on two sources to train the model: 
- the same pairs of data (in JSON format) and chart specifications that data2vis used. (4300 examples up-sampled to 215,000 training pairs)
	- This is how data2vis generates these pairs: 
		Our training dataset is constructed from 4300 Vega-Lite visualizations examples, based on 11 distinct datasets. The exam- ples were originally compiled by [52] where the authors use the CompassQL [76] recommendation engine within Voyager2 [77] to generate charts with 1-3 variables, filtered to remove problematic instances. These charts are generated based on heuristics and rules which enumerate, cluster and rank visualizations according to data properties and perceptual principles [77]. While these examples contain a simplified range of transformations and do not encode any interactions, they represent valid Vega-Lite examples and conform to important perceptual principles enforced by rules within Voyager2. These characteristics make the dataset a suitable, low-complexity test bed for benchmarking our model’s performance on the task of learning to generate visualizations given only input data.
		We iteratively generate a source (a single row from the dataset) and target pair (see Figure 3) from each example file. Each example is then sampled 50 times (50 different data rows with the same Vega-Lite specification) resulting in a total of 215,000 pairs which are then used to train our model.
- 200-300 data-visualization pairs from Omic

### Preprocessing
- From data2vis: Achieving these objectives using a character based sequence model can be resource intensive. While character based models re- sult in smaller vocabulary size and are more accurate for specialized domains, they also present challenges — a character tokenization strategy requires more units to represent a sequence and requires a large amount of hidden layers as well as parameters to model long term dependencies [8]. To address this issue and scaffold the learning process, we perform a set of transformations. First, we replace string and numeric field names using a short notation —
“str” and “num” in the source sequence (dataset). Next, a similar backward transformation (post processing) is eplicated in the tar- get sequence to maintain consistency in field names

### Validation
Data2Vis compared the model outputs to charts generated with [Voyager 2](https://idl.cs.washington.edu/papers/voyager2/), which requires a user to manually select features but does the rest. 
We'll have 200-300 human-made visualizations to validate our model. Or to further train the model? 
How can we generate thousands of additional visualizations of genomic data to train the model? 