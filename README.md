# DSA_495-labs
[README.md](https://github.com/user-attachments/files/32264240/README.md)
# Lab 1: Emotion Classification and Error Analysis

**Student:** Alexus Livesey

Write concise answers supported by your executed notebook. Tables can contain
exact results; your prose should interpret them rather than repeat every number.
Aim for about **400–800 words total**, excluding tables, file descriptions, and
the AI-use statement. One short paragraph per question is enough.

## Purpose

In 2–3 sentences, describe the classification task and what the comparison among
the constant baseline, specialized DistilBERT classifier, and BART zero-shot
classifier is intended to show.

**Response:**

The classification task is to 

## Files and rerun instructions

List every file included in your submission and briefly describe it.

- `Lab1.ipynb` (or .py): A Python script to be used in Google Colab for baseline, DistilBERT, and BART zero-shot classification comparisons.
- `README.md`: This response and reproducibility document.
- [Add any other submitted files if any, or write “No additional files.”]

To run the analysis:

1. Open the notebook in Google Colab.
2. Select **Runtime → Change runtime type → T4 GPU**.
3. Confirm that the course data are available in Google Drive at
   `DSA495-2026/Labs/Lab 1`.
4. Run all notebook cells from top to bottom.
5. [Add any additional instruction needed to reproduce your submission.]

The notebook contains two designated student code blocks and one model-selection
line. Complete those sections yourself; the surrounding setup and model-inference
code is supplied.

## 1. Data and tokenization

### Q1. Development and evaluation data

Report the number of development and evaluation messages. Which evaluation
class is least common, and why does that make accuracy alone insufficient?

**Response:**

There are 30 development messages and 1970 evaluation messages. Surprise is the least common class in the evaluation messages.
Accuracy alone is insufficient because it ignores the uneven distribution of the data. 
Surprise is disproportionately less common than the other classes, which have average counts in the hundreds.

### Q2. What do the tokenizers receive?

Using specific rows from your token table, explain **two meaningful differences**
between the DistilBERT and BART tokenizers. At least one explanation must refer
to one of your two student-written examples.

**Response:**

Comparing rows 12 and 13, there are differences in how the tokenizers classified subwords and the capitalization of nouns. 
DistilBERT uses ## to classify the word cherish, in which it broke the word down into two distinct parts (wordpiece): cher, ##ish. 
In contrast, the BART model kept the word together. The  DistilBERT tokenizer also did not recognize I as a noun, nor did 
it capitalize the first word WOW, whereas the BART tokenizer did so. 

Another difference between the tokenizers can be found in example I5, which included the smiling emoji.
The DistilBERT output classified the emoji through this argument [UNK], whereas the BART tokenizer classified 
the emoji by using the following: ĠðŁĺ, Ĭ. 

### Q3. Truncation

What content was removed from the long diagnostic message at the artificial
32-token limit? Explain one way that losing this content could affect emotion
classification. Do not claim that truncation caused an observed model error
unless you test that claim.

**Response:**

The content removed from the long diagnostic messages included the following words: 
way. I described the train ride, the weather, and every stop along the way. 
I described the train ride, the weather, and every stop along the way. 
Despite the ordinary journey, I am terrified about what happens tomorrow.

The last sentence of the omitted text reflects emotional content relevant to classifying the message emotion as terrified. 
By not including that content, the message's emotional content could be classified  by the model as either neutral or indifferent. 

### Q4. Baseline and encoder results

Complete the table using the 1,970-message evaluation set.

| Method | Accuracy | Macro-F1 | Inference seconds |
|---|---:|---:|---:|
| Always predict joy | 0.350254|0.086466 | N/A |
| DistilBERT emotion classifier | 0.924365| 0.880256| 5.135587|

Which emotion has the lowest DistilBERT recall? Include its recall and support.  

**Response:**

Surprise had the lowest recall (0.7541) on the DistilBERT model across 61 messages.  
Individual classification ranges of support are between 61 and 690. 
Recall scores lie between 80 and the low 90s, with the weighted average around 92% recall.

### Q5. Three encoder errors

Record three incorrect DistilBERT predictions. Include at least one high-score
error if the notebook produces one.

| Example ID | Reference label | Prediction | Model score | Brief observation |
|---|---|---|---:|---|
| emotion_test_00468| sadness| joy| 0.995780| The model predicted joy when true label was sadness.|
| emotion_test_01314| surprise | fear| 0.998750| Fear was predicted, but the correct classification is surprise.|
| emotion_test_00816| anger| joy| 0.9984| The model predicted joy when the correct classification is anger.|

What pattern, ambiguity, or missing context do you observe? Cite language from
the messages. Remember that a high model score is not proof that the prediction
is correct or that the score is calibrated.

**Response:**

The model's predictions suggest it doesn't consider broader context. 
In the Emotion test 00468 output, the message states, "i cant help feeling this way" and the model predicted joy. 
Without context, this message could mean excitement about love but can also reflect sadness, which was the true prediction. 
Language in emotion_test_00816 also shows a similar trend: "whenever i put myself in others shoes and try to make the person happy. 
The overall pattern observed is that the model does not make predictions based on cognitive empathy that relates the 
messages to a diverse range of real-world scenarios, and that the messages contain mostly nonemotional words. 


## 3. Zero-shot classification

### Q6. Label wording

Complete the development-set comparison.

| Candidate-label formulation | Accuracy | Macro-F1 |
|---|---:|---:|
| A: emotion names | .500000| 0.464388|
| B: expanded descriptions | 0.566667| 0.552279|

Which formulation did the prespecified macro-F1 rule select? Give one example
whose prediction changed when the wording changed. Why is a conclusion based on
only five development messages per class uncertain?

**Response:**

The prespecified macro-F1 rule selected formulation B: expanded descriptions because it achieved 
a higher Macro-F1 score (0.566667) than formulation A: emotion names. For emotion_test_00518, label A predicted 
the emotion as sadness, whereas emotion B predicted the emotion as surprise. Having five development messages 
can produce uncertain conclusions because it likely does not capture the entire range of emotions that can be
associated with a message. Language is very diverse and varies over situation type and culture, and more examples
would be needed to exhaust all the possibilities of correlation between emotion and message. 

### Q7. Final model comparison

Complete the table using the same 1,970 evaluation messages for both models.

| Method | Accuracy | Macro-F1 | Inference seconds |
|---|---:|---:|---:|
| Always predict joy | 0.350254| 0.086466| N/A |
| DistilBERT emotion classifier | 0.924365| 0.880256| 6.480784|
| BART zero-shot classifier | 0.536548| 0.479474| 146.539143|

Describe the main performance difference without claiming that this is a
controlled comparison of model architectures.

**Response:**

The main performance difference occurs across the different methods of DistilBERT, BART, and always predicting joy, 
and the differences vary by the performance metric analyzed. For instance, accuracy yields around a .57 difference
between always predicting joy and DistilBERT, and an approximate .40 difference from DistilBERT to BART. The differences
across methods for Macro-F1 performance are larger than accuracy differences. Macro-F1 range between always predicting joy
and DistilBERT is approximately a .80 difference. 

### Q8. Four model disagreements

Record four examples where DistilBERT and BART disagree. Include different
correctness patterns when the notebook makes them available.

| Example ID | Reference | DistilBERT | BART | Who is correct? |
|---|---|---|---|---|
| emotion_test_00002| sadness| sadness| love| encoder|
| emotion_test_00072| suprise| fear| suprise| zero shot|
| emotion_test_00098| anger| fear| sadness| both wrong|
| emotion_test_00004| sadness| sadness| suprise| encoder|

Choose two of these messages and explain what textual evidence supports each
model’s prediction. If the reference label is debatable, explain why.

**Response:**

Message ID emotion_test_00072 was predicted by BART as surprise. The message includes text such as 
"I play billiards left-handed naturally... so trying to play right-handed feels weird." The language 
contrast between naturally and feels weird supports BART's surprise prediction because it suggests
a reaction not typically experienced by the individual. Both models incorrectly predicted Emotion_test_00098.
The message text states "i feel my heart is tortured by what i have done." The correct
reference label is anger, yet this is debatable because tortured can easily be associated with pain, and
the use of the word heart experiencing torture may relate more to sadness than anger, such as in instances
where an individual experiences loss. 

### Q9. Recommendation and limitations

Which classifier would you use for this fixed six-emotion task? Support your
choice with at least two quantitative results and one finding from error
analysis. Then identify **two limitations** that constrain what the results
establish.

**Response:**

This analysis recommends that the DistilBERT classifier be used for the six-emotion task. Comparing 
model performance, DistilBERT performed better than the BART model on both performance metrics. 
DistilBERT's accuracy was 0.924365, and its Macro-F1 score was 0.880256, compared to BART's scores, which
produced a 0.536548 accuracy score and 0.479474 Macro-F1 score. Additionally, this analysis also shows
that some of the errors occurring through the DistilBERT model are not drastic errors. 
For instance, DistilBERT's error confused, surprise, and fear. While DistilBERT did outperform BART in some
ways, the limitations are that DistilBERT still produced extreme errors, such as misclassifying sadness as 
joy. Similarly, FINISH. 

## AI-use statement

I used AI, specifically Claude, to help me with this assignment. First, 
I asked Claude to give me a breakdown of how to use and break down 
official documentation because I have not used it much before. 

Next, I used Claude to ensure I was understanding the existing code. For instance, I used Claude
to clarify what the code in the second student code block was creating.  
To ensure that the AI was correct, I also looked at past notebooks completed during
class to compare Claude output to what material I have been taught. 
State whether you used a generative or agentic AI tool for this assignment.
During the second student codeblock 

I also used Claude to understand the ## in the DistilBERT model output. I asked
the bot to clarify what the symbol meant and then checked its response with the 
official documentation on HuggingFace that confirmed Claude was accurate. 



I completed any interpretation and responses to the findings without the assistance of AI. 
I mainly incorporated AI to help me understand Python code. 


describe how you used and checked the tool.

If you did not use one, write: “I did not use a generative or agentic AI tool
for this assignment.”
