# Team information

Student 1 12205927 Ana Terović

Student 2 12206591 Fani Sentinella-Jerbić

Student 3 12224159 Iva Pezo


# Report

## Part 1 - Test Collection Preparation
In the first part, we explore the ways of aggregating the raw judgments into labels. The raw judgments of the FiRA data from 2022 contain anonymized information about user judgments for query-passage pairs. These judgments are used to evaluate the relevance or quality of a passage in response to a given query. 
Judgments contain some additional information such as user-id, the time it took to annotate, the time of annotation, the judgment value, and the optional selection of text. 
We aim to find a more sophisticated method of aggregating raw judgment to usable and robust labels in comparison to the given baseline majority voting. 
As the name implies, each annotator in majority-voting ‘votes’ towards their label, and then we pick the label which has the most votes. This method assumes that all annotators are equally reliable and that all errors are completely random, which is usually not the case.

We decided to go with Dawid-Skene probabilistic model to aggregate the raw judgments. DS is a well-known algorithm for annotator agreement estimation and label inference based on the principle of expectation maximization. Using the Bayes rule, it predicts a label that maximizes the probability of the observed annotations. It assumes that each annotator has a certain level of accuracy and biases, and it estimates the true labels by iteratively updating the estimates based on the agreement among annotators. 

To implement the DS model, we first had to convert the raw judgments so that each judgment consists of query ID, passage ID, annotator ID, and judgment value. 
The algorithm first computes initial probability distributions of the ratings across the classes for each annotator. Then we iterate through each query-passage pair while selecting only the annotators who have contributed to the annotation of the given query-passage pair. Then the probabilities from all the annotators are summed up and normalized before updating the probability columns for that query-passage pair. The true class label is then updated by selecting the most probable class. We keep updating the values until convergence. Once the algorithm converges, we assign the label with the highest probability as the final aggregated label for each query-passage pair.

During the implementation of the algorithm, we came across a problem with the convergence. Usually, in the DS model, the convergence threshold is set to 0.001, but even after 12h of processing the given dataset, it didn't converge. 
Since the goal of this part was to find a more sophisticated method of aggregating raw judgment, we modified the algorithm in a way that it assigns the judgments based on the level of accuracy and biases of the annotators that contributed to the voting.

We analyze the results by randomly picking some examples and conducting a meta-judgement of the raw-judgements + the quality of the aggregations based on the text contents of the query and passage. 


1. rob_q_FT944-3371	rob_FR940825-1-00144	3
 -> query_id: airport
 -> passage: airports within a 30 - nautical - mile radius of the charlotte / douglas international airport . 1airport name 1former ( agl ) 1proposed ( agl ) arant airport , wingate , nc 2 , 500 2 , 000 bradley outernational airport , china grove , nc 2 , 500 1 , 500 chester municipal airport , chester , sc 2 , 500 1 , 600 china grove airport , china grove , nc 2 , 500 1 , 500 goodnight ' s airport , kannapolis , nc 2 , 500 1 , 500 knapp airport , marshville , nc 2 , 500 2 , 000 lake norman airport , mooresville , nc 2 , 500 2 , 000 lancaster county airport , lancaster , sc 2 , 500 1 , 600 little mountain airport , denver , nc 2 , 500 2 , 000 long island airport , long island , nc 2 , 500 2 , 000 miller airport , mooresville , nc 2 , 500 1 , 500 u s heliport , wingate , nc 2 , 500 1 , 600 unity aerodrome airport , lancaster , sc 2 , 500 1 , 900 wilhelm airport , kannapolis , nc 2 , 500 1 , 900 airports within a 30 - nautical - mile radius of the houston intercontinental airport and the william p . hobby airport . 1airport name 1former ( agl ) 1proposed ( agl ) ainsworth airport , cleveland , tx 1 , 200 1 , 000 ausinia ranch airport , texas city , tx 1 , 200 1 , 000 bailes airport , angleton , tx 1 , 200 1 , 000 biggin hill airport , hockley , tx 1 , 200 1 , 000 cleveland municipal airport , cleveland , tx 1 , 200 1 , 000 covey trails airport , fulshear , tx 1 , 200 1 , 000 creasy airport , santa fe , tx 1 , 200 1 , 000 custom aire service airport , angleton , tx 1 , 200 1 , 000 fay ranch airport , cedar lane , tx 1 , 200 1 , 000 flying c ranch airport , needville , tx 1 , 200 1 , 000 freeman property airport , katy , tx 1 , 200 1 , 000 garrett ranch airport , danbury , tx 1 , 200 1 , 000 gum island airport , dayton , tx 1 , 200 1 , 000 h s airfield airport , damon , tx 1 , 200 1 , 000 harbican airpark airport , katy , tx 1 , 200 1 , 000 harold freeman farm airport , katy , tx 1 , 200 1 , 000 hhi hitchcock heliport , hitchcock

Individual judgements for the given combination were: 3, 2, 1
Based on the context of the passage, the 3 - PERFECT_ANSWER aggregated judgement seems right.


2. trip_146268	trip_340170	2
  -> query_id: asthma and food allergies
  -> passage: prevalence of food allergy and its relationship to asthma and allergic rhinitis in schoolchildren . background : food allergy ( fa ) is an important health problem . however , epidemiological studies at the population level are scarce . we assessed the prevalence of fa and its associations with respiratory manifestations among schoolchildren . methods : a total of 6672 schoolchildren aged 9 - 11 years recruited from 108 randomly chosen schools in six french cities underwent a clinical examination including skin prick test ( spt ) to common food and aeroallergens and the standardized protocol of the run test to assess exercise - induced bronchial hyper - responsiveness ( eib ) . asthma , allergic rhinitis ( ar ) and clinical symptoms of fa were determined using a standardized questionnaire completed by parents . results : about 2 . 1 % of the children reported symptoms of fa , 1 . 9 % were sensitized to food allergens , and 0 . 1 % had sp - tested fa . the ar was more prevalent than asthma ( 20 . 0 % lifetime , 11 . 9 % past year vs 9 . 8 % , 8 . 7 % respectively ) . reported fa , food sensitization and sp - tested fa were all positively associated with asthma and ar ( p < 0 . 001 ) . these associations persisted also for fa not manifesting as respiratory symptoms ( p < 0 . 001 ) . asthma and ar remained significantly associated with reported symptoms of fa and allergic sensitization to food allergens after adjustment for confounders as well as for sensitization to aeroallergens . no relationship existed between eib ( 9 . 0 % ) and fa . conclusion : the relationships between fa and asthma and ar could be totally explained neither by the existence of respiratory manifestations of fa nor by sensitization to aeroallergens . the fa might intervene differently in asthma and ar .

Individual judgements: 3, 2, 0
The passage is relevant to the given query, but an average user probably wouldn't be interested in a scientific study but rather in a simple text on the topic.

3. db_q_<dbpedia:2011_Grand_Prix_motorcycle_racing_season>	db_<dbpedia:2011_Speedway_Grand_Prix_of_Croatia>	3
  -> query_id: 2011 fim road racing world championship grand prix
  -> passage: 2011 speedway grand prix of croatia the 2011 fim nice croatian speedway world championship grand prix was the tenth race of the 2011 speedway grand prix season . it was take place on september 24 at the stadium milenium in donji kraljevec , croatia .

Individual judgements: 3, 3, 2
The passage seems to be perfectly aligned with the query.

## Part 2 - Neural Re-Ranking

For the implementation of the two models we found a lot of help in the original papers. KNRM was much simpler to implement and most of the trouble was with understanding of the shapes of tensors. One interesting problem was with tensor.CosineSimilarity function, which had wrong documentation online on how it works which resulted in wrong tensor dimensions. For the implementation of TK we based the code on https://github.com/sebastian-hofstaetter/matchmaker/blob/master/matchmaker/models/published/ecai20_tk.py, while adapting it to the given code and adding comments to showcase understanding. Here, most of the time was spent looking at specific lines to gain better understand of the finer details of the implementation. Nice thing was that the two models have some similar parts so the transition from a simpler, KNRM to a more complex, TK was easier and code more understandable.

Train, validation and evaluation implementation was quite straightforward since its part of all neural models. There wasn't a set threshold for early stopping so we used 2000 iteration. Helper code from code_metrics came in very useful for calculating mrr and ndcg scores. 

### Evaluation 
#### MSMARCO
```
KNRM using 2 epochs:
final validation 
	loss 0.4918834933638573,
	MRR@10: 0.158, nDCG@10: 0.198, MRR@20: 0.165, nDCG@20: 0.226
test set
	MRR@10: 0.157, nDCG@10: 0.201, MRR@20: 0.165, nDCG@20: 0.233
```
```
TK using 2 epocks:
final validation
	loss 0.0875627768971026,
	MRR@10: 0.265, nDCG@10: 0.317, MRR@20: 0.269, nDCG@20: 0.331
test set
	MRR@10: 0.279, nDCG@10: 0.331, MRR@20: 0.284, nDCG@20: 0.348
```

All the code was run using google colab because of the access to the cuda gpu. This made the training faster but it caused some trouble in the terms of the access to the gpu, memory issues and having to rerun the entire notebook occasionally.

## Part 3 - Extractive QA

We selected a model from deepset called `tinyroberta-squad2`, which is a distilled version of `roberta-base-squad2`. We used default hyperparameters for both the tokenizer and the prediction model. 

The model receives question and context pairs, and outputs text from context which answers the question.

Example input:

```
QA_input = {
	'question': 'how many oscars has clint eastwood won?pdrijgheposrgijapeoikgjpesoar',
    'context': 'Clint Eastwood -- five-time Oscar winner and eleven-time nominee -- has some advice for people who got snubbed at the Academy Awards this year ... quit whining. Eastwood -- an Academy stalwart -- isn't buying into the protests over the so-called black snub.'
}
```

Example output:

```
QA_output = {
	'score': 0.9574741125106812, 
	'start': 18, 
	'end': 22, 
	'answer': 'five'
	}
```

The biggest issue for this part was the size of the MSMARCO FIRA 21, which made the computation longer, which is why we decided to use the distilled model.

### Evaluation

#### MSMARCO FIRA 21

Using the provided evaluation code, we compute the F1 score for each tuple. As some tuples contain multiple gold-label answers, we take the maximum of the F1 score between each of them and the predicted answer. Afterward, we aggregate the results using mean, standard deviation, min, and max. As this model also outputs a confidence score, we report the same aggregations for this one as well.

The retrieved results are provided below:

```
f1 score
MEAN:  0.4194
STD:   0.3270
MIN:   0.0000
MAX:   1.0000

confidence score
MEAN:  0.3237
STD:   0.3111
MIN:   0.0000
MAX:   0.9986
```

We investigate some of the examples where the model fails:

```
what level is blood pressure too low     if it causes noticeable symptoms
define uncut?                            Adjective
what is the actual us independence day   the national holiday
```
We can see that the model is arguably not wrong in many of the cases, but the answers are often reminiscent of a child pretending to be a *smartass*. It is also interesting to look at the confidence scores of these examples where the model failed. For example, for this one which is completely wrong, the model also outputs a very low confidence score of 4.718824e-06:
```
what do you put basil on                 spices to enhance the taste of pasta
```


