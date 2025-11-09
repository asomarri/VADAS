# Valence Aware worD Embedding for web Application Security (VADAS)
Valence Aware WorD Embedding for Web Application Security (VADAS) leverages the power of Natural Language Processing (NLP) techniques and sentiment classification, or in our case "maliciousness classification", that allows the detection of web applications attacks by leveraging a set of revised vocabularies that contain common words found in different attack vectors. Output from VADAS can be used to classify between benign and malicious HTTP requests.

VADAS, will leverage the power of FastText unsupervised training capabilities. FastText will represent words by vectors and these vectors capture hidden information about a the training text, like word analogies or semantics. Is it important to mention that before training each model, each log in the dataset was modified, by inserting the appropriate groupo label at the beginning of the log (sqli, xss, cmdi, etc.). This label is used as an "anchor" word, to calculate valence score for each word using the cosine similarity function. Since this output returns a value between -1 and 1, this will represent the word similarity against the anchor word. 

The following models were trained separately using labels from the training dataset: 

| Model        | Descriptionn       |
|---------------|:---------------------------------------------|
| Benign        | contains words from benign logs       |
| SQLI        | contains words from sql injection logs       |
| CMDI        | contains words from command injection logs       |
| Protocol        | contains words from protocol anomalies logs       |
| XSS        | contains words from Cross Site Scripting logs       |
| XXE        | contains words from XML external entities logs       |
| LFI        | contains words from local file injection logs       |
| RFI        | contains words from remote file injection logs       |
| WAT        | contains words from web application attack tools logs       |
| General        | contains words from all malicious logs       |


> [!IMPORTANT]
> VADAS dictionary contains a total of 151,779 words.

SO VADAS dictionary provides a way of representing word's maliciousness for each classification group. This means that we can represent each word with a 10-dimensional *valence aware vector*, containing the following valence scores: benign, SQL injection, command injection, protocol anomalies, XML external entities, local file inclusion, remote file inclusion, web attack tools, and general.

To show this with a concrete example, we can look at a common Local File Inclusion attack, "../../etc/passwd", and we get the following results:

| etc        | Valence Score       |
|---------------|:---------------------------------------------|
| Benign        | 0.24713496976300683       |
| SQLI        | 0       |
| CMDI        | 0.6968856574329236       |
| Protocol        | 0       |
| XSS        | 0       |
| XXE        | 0       |
| LFI        | 0.9997131546027924       |
| RFI        | 0       |
| WAT        | 0       |
| General        | 0.07689472947692488       |


| passwd        | Valence Score       |
|---------------|:---------------------------------------------|
| Benign        | 0       |
| SQLI        | 0       |
| CMDI        | 0.7030303958182751       |
| Protocol        | 0       |
| XSS        | 0       |
| XXE        | 0       |
| LFI        | 0.9998158326189873       |
| RFI        | 0       |
| WAT        | 0       |
| General        | 0.14571284009440577       |


Few interesting things about these results:
- We can see that both words appear in the same malicious dictionaries: local file inclusions (LFI), command injection (CMDI) and the General model.
- Highest cosine score for both words comes from Local File Inclusion (LFI) model.
- Interesting enough, word *passwd* is not found in the benign dictionary. However, *etc* was found in the benign dictionary as this is commonly used to represent the word *etcetera*.

VADAS purpose is to represent words' maliciousness in the context of **Web Application Security** with a minimal footprint vector (10-dimensional vector) that can be used in downstream NLP classification tasks.
