![Header Graphic](https://github.com/thegrandblooms/Information_Propagation_Analysis/blob/7fd9a9495574721da3560f385ea9f73eb71784a5/graphics/infoprop_header.jpg)
# Analyzing Information Propagation using NLP and Machine Learning
Author: Palak Bhogra

## Overview

Information propagation is one of the fundamental forces of nature shaping our world. Whether your research paper gets cited, gets funding, or crosses into commercial applications, or whether customers hear about your new idea or company all depend on the way information is communicated and shared. Political campaigns, misinformation, company cultures and social revolutions all have one thing in common: some idea or set of information has propagated. All of culture, business, and technology can be seen and possibly measured through this lens.

If, on watching a new TV show, you share it with more than one other person, and they each share it with more than one person, and that pattern continues, that show will spread exponentially. This propagation rate (spreading more than once on average) is referred to as R0 in epidemiology. ["The R0 value of COVID-19 as initially estimated by the World Health Organization (WHO) was between 1.4 and 2.4"](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7751056/) Anything that propagates (organisms, viruses, information, services, research) has an R0 value.

In this project, we see if we can use Natural Language Processing and Machine Learning to predict viral text in machine learning communities on twitter.

For more information about this topic generally, I recommend reading this [excellent blog post](https://meltingasphalt.com/interactive/going-critical/) by Kevin Simler, or listening to [this hidden brain podcast](https://hiddenbrain.org/podcast/the-snowball-effect/) with Damon Centola.

## Organizational Problems
This project is aimed at two specific stakeholders - academic lab managers and marketing professionals interested in promoting through social media.

Academic stakeholder uses:
Whether your research gets attention, citations, and funding ultimately depends on whether people hear about and share your work. For this reason every academic spends a good deal of time thinking about the effectiveness of their scientific communications, because their research impact will be a function of their R0 or propagation rate. If we can better predict which things will spread through an academic community, we might be able to steer scientific communications and research to reflect the interests of that community. 

Propagation rate has effects downstream, influencing which research efforts might escape academia and cross over into policymaking or commercial sectors. Covid-19 could be considered a case-study in what can happen when academic communications struggle to propagate into effective policy in the broader cultural context. Sharing research with the general public is of great importance for research-based policymaking, funding, and public support.

Business case:
Every organization depends on information propagation in some way. Often this is done through marketing expenditures which spread awareness of a product or service. When a product or service has a good R0 value, we say it has good "word of mouth" which lowers the customer acquisition cost and increases demand. If we could predict which things will go viral, this could economically generate demand for products or services.

![Fun image generated by Dall-E](https://github.com/thegrandblooms/Information_Propagation_Analysis/blob/7fd9a9495574721da3560f385ea9f73eb71784a5/graphics/DALL%C2%B7E%202022-10-18%2023.45.20%20-%20_word%20of%20mouth_%20an%20idea%20spreading%20through%20crowds,%20oil%20painting%20that%20reminds%20me%20of%20jazz,%20street-wise%20people,%20and%20vibrations.png)

(illustrations generated by DALL-E)

## Data

Data collection was done by looking at all of the followers/follows of machine learning researchers/educators (specifically Juergen Schmidhuber & Andrew Ng) and scraping tweets from those accounts. This could be taken further by looking at the intersection of followers of a few "celebrity" names within a particular niche, but this technique was not utilized here and we could therefore think of this analysis as slightly less targeted.

## Methods

Our analysis consisted of two main stages:
- NLP Pipelines for converting text to features for Machine Learning
- Machine learning models on NLP and tweet metadata

For our text data, three NLP processes were used: Bag of Words, Document Embeddings (via ROBERTA-Large), and Top2Vec. 

1.) - Bag of Words is the simplest here, counting word frequencies in documents. Before this, the text is cleaned and simplified.

2.) - Document Embeddings leverages the principle components of a large language model - in this case, [ROBERTA-Large](https://huggingface.co/roberta-large) from hugging face. These principle components reflect the 1024 most-descriptive dimensions in a larger corpus of text. We can convert our text data into these dimensions and use it to differentiate text styles.

3.) - [Top2Vec](https://github.com/ddangelov/Top2Vec) is another layer on top of document embeddings - Top2Vec is the combination of a document embedding pipeline with a clustering algorithm, which identifies distinct clusters of documents and assigns them a label. Top2Vec is very interpretable, we can see what these topics are and which words tend to occur within them. two  examples in our analysis were OpenAI and posts hiring PHD candidates.

After our NLP pipeline, we have about 2500 features which can be reduced based on correlation with our target or SequentialFeatureSelection. For our regression we took the top 256 features based on correlation, while for classification we just went ahead and fed the full 2500 features into our machine learning.

Ultimately 6 machine learning algorithms were tried:
- Linear and Logistic Regression
- Random Forest
- XGBoost
- Deep Learning (cut from notebooks)
- 1D CNN (cut from notebooks)
- TabNet

Deep Learning and 1D CNNs were eventually cut, as they consistently underperformed compared to TabNet; [TabNet](https://github.com/dreamquark-ai/tabnet) is a deep learning algorithm developed by Google specifically for working with tabular data. 1D CNNs are an interesting approach however, converting text data into a simplified form of "image" in order to recognize patterns in data. In some cases this can perform well, but this did not appear to be one of those cases.

## Validation

For validating our models, we split our data into train, validation and test sets. For classification, the sizes of these sets were 23310, 6475, and 2590 respectively. Training was performed using training and validation sets, validation set was used to compare models, and then after a final model was chosen, our test set was used to confirm our results. Our Random Forest model went from an F1 of .31 in our validation set to an F1 of .26 in our test set, which was better than expected considering we only have 70 positive targets in our test set.

## Findings

Viral text is definitely different in some ways from normal text. Some topics are clearly more viral, e.g. talking about OpenAI or hiring PHD candidates. With our approach, we could specifically quantify the virality of different words or topics - for example, the phrase "100daysofcode" had a 16% correlation with retweets in our machine learning niche, indicating a lot of interest in early-educational stages and personal learning journeys.

Our classification was 97% accurate at classifying tweets as over or under 50 retweets, having an F1 score of 0.26. We were able to use NLP and machine learning to explain about 11% of what makes people share text (R2).

Our best performing algorithm for regression was XGBoost, while our best performing algorithm for classification was a Random Forest. I had high hopes for TabNet in interpreting our 2500 features but this failed to beat XGBoost on our traditional feature set.

ROC Curve for classification:

![ROC Curve](https://github.com/thegrandblooms/Information_Propagation_Analysis/blob/7fd9a9495574721da3560f385ea9f73eb71784a5/graphics/roc-auc.png)

Regression Root-mean-squared-error across our baseline and our three main regression models - XGBoost on document embeddings, an ensemble of nlp and metadata, and just metadata:

![Regression rmse](https://github.com/thegrandblooms/Information_Propagation_Analysis/blob/7fd9a9495574721da3560f385ea9f73eb71784a5/graphics/regression_rmse.jpg)

A Classification report for our random forest classification on holdout data:

![Final classification report](https://github.com/thegrandblooms/Information_Propagation_Analysis/blob/6c3d95c402880f22923a6451b94f9aabc493cd22/graphics/classification_performance.jpg)

## Conclusion

We were able to predict about 11% of variance using NLP and about 28% of variance using account data in our regression models, and achieved an accuracy of 97% and an F1 score of ~0.26 in classification. Virality may always be hard to predict – like predicting the stock market, if you find something that works, it changes the landscape and may stop working. Still, these results are promising and likely have utility in the right communications dashboard.

The importance of follower counts in propagation rate reinforces the idea of 'super spreaders' and influencers in the transmission of information. Propagation from highly-connected nodes can set off a chain reaction that propagates across other highly-connected nodes. We also see that this phenomenon alone isn't enough to explain virality, and the semantic context of the communication plays an important, measurable role.

## Advice and Next Steps

General:
- Integrating our model with business processes or marketing dashboards may be the most obvious use-case
- Building a dashboard for people to play with could be a way to draw attention to this project
- Better NLP and document embeddings from newer large language models
- Reuse this NLP pipeline to make other predictions from text

Academic Advice:
- Maximize the reach of new research by considering the interests of entry-level users
- Involve a few highly-connected researchers or communicators to share or talk about your findings
- Run your communications through this model to see if they pass the virality check. Our classifier correctly identified 98% of nonviral posts.

Business Advice:
- Consider incorporating this model into a communications dashboard to target specific niches
- Think about supporting a local ecosystem of influencers which are interested in your announcements and products
- Run your communications through this model to see if they pass the virality check. Our classifier correctly identified 98% of nonviral posts.

## For More Information

Check out the code in the [regression](https://github.com/thegrandblooms/Information_Propagation_Analysis/blob/7fd9a9495574721da3560f385ea9f73eb71784a5/Information%20Propagation%20Analysis.ipynb) or [classification](https://github.com/thegrandblooms/Information_Propagation_Analysis/blob/7fd9a9495574721da3560f385ea9f73eb71784a5/Viral_Classification.ipynb) notebooks, or review the [presentation]().

For additional information, contact Blake McMeekin at blakemcme@gmail.com

## Repository Structure
```
├── data
│   └── combined_data.csv
├── graphics
├── Data_Sourcing_and_Processing_from_Twitter.ipynb
├── Roberta_data_processing.ipynb
├── Top2Vec_Data_Processing.ipynb
├── Viral_Classification.ipynb
├── Information Propagation Analysis.ipynb
├── Viral Text Prediction Presentation.pdf
└── README.md
```
