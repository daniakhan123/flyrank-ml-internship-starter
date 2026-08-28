Prompt Ladder: From a Weak Prompt to a Reusable Analysis Prompt
Context

This prompt ladder improves a prompt I could realistically have used while working on my FlyRank capstone about predicting and prioritizing content with a higher risk of search-impression decline.

Run 0 — Weak Baseline
Weak prompt

Explain this data and tell me what model I should use.

Representative output

You should first explore the dataset, clean missing values, perform feature engineering, and then try machine learning models such as Random Forest, Logistic Regression, or XGBoost. Evaluate the models using accuracy, precision, recall, and F1 score.

Notes

What changed:
Nothing. This is the intentionally weak baseline.

What actually improved in the output:
Nothing to compare yet. It gave a general starting point.

What still failed:
The answer was too generic. It did not know what data I had, what I was trying to predict, or what decision the model was supposed to support.

What I would try next:
Add a clearer goal.

Run 1 — Layer Added: Clearer Goal
Prompt

I have search-performance data for content pages. Help me design a model that predicts which pages are at risk of a future impression decline.

Representative output

You can frame this as a binary classification problem. Create a label indicating whether impressions decline beyond a chosen threshold and train a classifier using historical performance features.

Notes

What changed:
I added a clearer goal: predicting future impression decline.

What actually improved in the output:
The response stopped suggesting unrelated analyses and identified the task as a classification problem with a future outcome.

What still failed:
It still gave advice without understanding who the final result was for or how the result would be used.

What I would try next:
Define the audience and decision.

Run 2 — Layer Added: Defined Audience
Prompt

I have search-performance data for content pages. Help me design a model that predicts which pages are at risk of a future impression decline. The result will be used by a content team deciding which pages to review first.

Representative output

The model should focus on prioritization rather than automatically deciding which pages should be changed. A useful output would be a ranked list based on estimated decline risk.

Notes

What changed:
I added the specific audience and decision: a content team deciding what to review first.

What actually improved in the output:
The answer shifted from simply building a classifier to producing a prioritization system. It connected the model output to a real action.

What still failed:
The advice was still abstract because it did not know the actual structure or scale of my dataset.

What I would try next:
Add real context about the data.

Run 3 — Layer Added: Real Context
Prompt

I have search-performance data for content pages. Help me design a model that predicts which pages are at risk of a future impression decline. The result will be used by a content team deciding which pages to review first. My dataset is a pseudonymized warehouse with about 78.8 million daily performance records. Available signals include impressions, clicks, CTR, average search position, position volatility, and query-level signals. I need to work efficiently in Google Colab without loading the full warehouse into memory.

Representative output

Aggregate the daily records into content-level features using DuckDB before moving the smaller feature table into pandas. Define features using a historical window and define the decline outcome using a later time window to reduce leakage.

Notes

What changed:
I added real context about the dataset, available signals, scale, and computing constraints.

What actually improved in the output:
The response became technically relevant. Instead of telling me to load and clean a dataset, it suggested aggregation with DuckDB and explicitly addressed working with 78.8 million records in Colab.

What still failed:
The answer could still produce a long explanation instead of a concrete step-by-step design that I could implement.

What I would try next:
Specify the output format.

Run 4 — Layer Added: Specified Output Format
Prompt

I have search-performance data for content pages. Help me design a model that predicts which pages are at risk of a future impression decline. The result will be used by a content team deciding which pages to review first. My dataset is a pseudonymized warehouse with about 78.8 million daily performance records. Available signals include impressions, clicks, CTR, average search position, position volatility, and query-level signals. I need to work efficiently in Google Colab without loading the full warehouse into memory.

Give the answer in exactly these sections:

Prediction target
Features
Baseline
Validation split
Leakage checks
Final ranked output
Representative output

1. Prediction target: Define whether impressions decline by more than a selected threshold in a future period.

2. Features: Use historical impressions, clicks, CTR, position, position volatility, and query signals.

3. Baseline: Compare against a majority-class predictor.

4. Validation split: Use a grouped split by client.

5. Leakage checks: Ensure no feature contains information from the future outcome window.

6. Final ranked output: Rank content using predicted decline risk combined with historical visibility.

Notes

What changed:
I added a required output format.

What actually improved in the output:
The answer became easier to implement because the important modeling decisions were separated into clear sections instead of being mixed into a long explanation.

What still failed:
This did not help as much as expected. The structure was cleaner, but the model advice was still willing to make assumptions without checking whether those assumptions matched the actual data.

What I would try next:
Add verification requirements.

Run 5 — Layer Added: Verification Requirements
Prompt

I have search-performance data for content pages. Help me design a model that predicts which pages are at risk of a future impression decline. The result will be used by a content team deciding which pages to review first. My dataset is a pseudonymized warehouse with about 78.8 million daily performance records. Available signals include impressions, clicks, CTR, average search position, position volatility, and query-level signals. I need to work efficiently in Google Colab without loading the full warehouse into memory.

Give the answer in exactly these sections:

Prediction target
Features
Baseline
Validation split
Leakage checks
Final ranked output

Before recommending a modeling design, explicitly state the assumptions you are making and identify what should be checked in the data before relying on the recommendation. Do not claim that the model proves why search performance changed or that refreshing content will cause recovery.

Representative output

Assumptions to verify: The historical and outcome windows are available for each observation, the decline threshold is appropriate for the task, and client grouping is available for validation.

Prediction target: An observed future decline in impressions, not the cause of that decline.

Leakage checks: Features must only use data available before the future outcome period.

Validation: Hold out entire clients so the test set represents clients unseen during training.

Interpretation: Use predictions as directional decision-support signals rather than proof that a particular content change will improve performance.

Notes

What changed:
I added verification requirements and explicit assumptions.

What actually improved in the output:
The model stopped presenting every recommendation as automatically correct. It clearly separated assumptions from verified facts and gave stronger guidance about leakage, grouped validation, and careful interpretation.

What still failed:
The prompt is now longer and more detailed than necessary for some simple tasks.

What I would try next:
Clean it into a reusable prompt while keeping only the instructions that materially improved the result.

Final Reusable Prompt

I am working with search-performance data for content pages and need to build a decision-support model that estimates which pages may be at higher risk of future impression decline.

The result will be used by a content team to prioritize which pages to review first, not to automatically decide which pages should be changed.

My data may include historical impressions, clicks, CTR, average search position, position volatility, and query-level signals. The dataset can be large, so recommend an approach that aggregates data efficiently before modeling rather than assuming the full dataset can be loaded into memory.

Before recommending the methodology, state any assumptions that need to be verified in the data.

Structure your response using exactly these sections:

Prediction target
Features
Baseline
Validation split
Leakage checks
Ranked output

Make sure features use only information available before the prediction outcome window. Recommend validation that tests generalization across clients where possible. Compare the model against a simple baseline.

Use careful language: describe results as observed, predictive, directional, or decision-support. Do not claim that the analysis proves why search performance changed, proves how Google's algorithm works, or proves that refreshing content will cause recover
