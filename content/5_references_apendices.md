---
title:
nav: References and Appendices
gallery: true
---

{% include feature/nav-menu.html sections="References;Notes;Appendices;Appendix 1. Excerpt of Python Text Mining Tool;Appendix 2. Apps Script Example for Linking Transcript to Primary Tag Sheet;About the Author" %}

<div class="symbol-container">
    <p class="symbol">&#10042;</p>
</div>

## References

Link J. Why Racial Bias Still Haunts Speech-Recognition AI. Built In; 2020 [cited 2024 Jul 8]. Available from: https://builtin.com/artificial-intelligence/racial-bias-speech-recognition-systems

Freund A. From.wav to.txt: why we still need transcripts in the digital age. Internet. 2024. [cited 2024 Jul 8].

Allen SE. Resisting the editorial ego: editing oral history. Oral Hist Rev. 1982;10(1):33-45. DOI 10.1093/ohr/10.1.33. [cited 2024 Jul 8]. Available from: https://www.tandfonline.com/doi/full/10.1093/ohr/10.1.33

## Notes

[1] Digital Collections, University of Idaho. University of Idaho Library Digital Initiatives; 2024 [cited 2024 Jul 8]. Available from: https://www.lib.uidaho.edu/digital/collections.html

[2] Speech to Text in Premiere Pro FAQ. Adobe; [cited 2024 Jul 8]. Available from: https://helpx.adobe.com/content/help/en/premiere-pro/using/speech-to-text-faq.html

[3] Department of Commerce and Labor, Bureau of Statistics. Thirteenth Census of the United States: 1910. Statistics for Idaho. Washington (DC): Government Printing Office; 1913. [cited 2024 Jul 8]. Available from: https://www2.census.gov/library/publications/decennial/1910/abstract/statistics-for-idaho.pdf

## Appendices

#### Appendix 1. Excerpt of Python Text Mining Tool

```
import pandas as pd
import string
from nltk.corpus import stopwords
from collections import Counter
import re
from textblob import TextBlob

Download NLTK stopwords data
import nltk
nltk.download('stopwords')

Define preprocess_text function
def preprocess_text(text):
    if isinstance(text, str):  # Check if text is a string
        text = re.sub(r'\b\w{1,4}\b', '', text)  # Remove short words (length <= 4)
        text = text.translate(str.maketrans('', '', string.punctuation)) 
        text = text.lower()  # Convert text to lowercase
    else:
        text = ''  # Replace NaNs with an empty string
    return text

Load stopwords for both Spanish and English
stop_words_spanish = set(stopwords.words('spanish'))
stop_words_english = set(stopwords.words('english'))

Combine both sets of stopwords
stop_words = stop_words_spanish.union(stop_words_english)

import os

Directory containing CSV files
directory = 'C:\\Users\\aweymouth\\Documents\\Github\\transcript_mining_base\\CSV'

List of CSV file names
file_names = [
    'rwhp070.csv',
    'rwhp075.csv',
    'rwhp079.csv',
    'rwhp083.csv',
    'rwhp088.csv',
    'rwhp109.csv',
    'rwhp123.csv',
    'rwhp174.csv',
    'rwhp225.csv',
    'rwhp261.csv',
    'rwhp277.csv',
    'rwhp277.csv',
    'rwhp297.csv',
    'rwhp320.csv',
    'rwhp323.csv',
    'rwhp378.csv',
    'rwhp385.csv',
    'rwhp410.csv',
    'rwhp418.csv',
    'rwhp420.csv',
    'rwhp421.csv',
    'rwhp422.csv',
    'rwhp425.csv',
    'rwhp426.csv',
    'rwhp427.csv'
]

Construct file paths using os.path.join()
file_paths = [os.path.join(directory, file_name) for file_name in file_names]

dfs = [pd.read_csv(file_path, encoding='utf-8') for file_path in file_paths]

Concatenate text data from all dataframes into a single corpus
corpus = ''
for df in dfs:
    text_series = df['text'].fillna('').astype(str).str.lower().str.strip()  # Extract and preprocess text column
    corpus += ' '.join(text_series) + ' '  # Concatenate preprocessed text with space delimiter

Preprocess the entire corpus
cleaned_corpus = preprocess_text(corpus)

Remove stopwords from the corpus
filtered_words = [word for word in cleaned_corpus.split() if word not in stop_words and len(word) >= 5]

Count the frequency of each word
word_freq = Counter(filtered_words)

Get top 100 most frequent distinctive words with occurrences
top_distinctive_words = word_freq.most_common(100)

=== General Section ===

from collections import Counter
import re

def find_agriculture_terms(corpus):
    # Define a list of agriculture-related terms
    agriculture_terms = ["harvest", "tractor", "acreage", "crop", "livestock", "farm field", "barn building", "ranch", "garden", "orchard", "dairy", "cattle", "poultry", "equipment", "fertilizer", "seed", "irrigation", "plow", "farmhand", "hoe", "shovel", "milking", "hay", "silage", "compost", "weeding", "crop rotation", "organic", "gmo", "sustainable", "farming", "rural", "homestead", "tilling", "wheat", "corn maize", "soybean", "potato", "apple fruit", "berry", "honey", "apiary", "pasture", "combine harvester", "trailer", "baler", "thresher"]

    # Initialize a Counter to tally occurrences of agriculture-related terms
    agriculture_word_freq = Counter()

    # Tokenize the corpus to handle multi-word expressions
    tokens = re.findall(r'\b\w+\b', corpus.lower())

    # Iterate over each token in the corpus
    for word in tokens:
        # Check if the token is an agriculture-related term
        if word in agriculture_terms:
            agriculture_word_freq[word] += 1

    # Return the top 50 most common agriculture-related terms
    return agriculture_word_freq.most_common(50)

Call the function to find agriculture-related terms in the corpus
top_agriculture_terms = find_agriculture_terms(corpus)

Print the top 50 agriculture-related terms
print("## agriculture")
for word, count in top_agriculture_terms:
    print(f"{word.capitalize()}: {count}")
```

#### Appendix 2. Apps Script Example for Linking Transcript to Primary Tag Sheet

```
function fillTags() {
  // Get the active spreadsheet
  var spreadsheet = SpreadsheetApp.getActiveSpreadsheet();
  
  // Get the transcript sheet by name
  var transcriptSheet = spreadsheet.getSheetByName("Callison3");
  if (!transcriptSheet) {
    Logger.log("Transcript sheet not found");
    return;
  }
  
  // Set the header in cell E1 to "tags"
  transcriptSheet.getRange("E1").setValue("tags");
  
  // Get the tags spreadsheet by URL
  var tagsSpreadsheet = SpreadsheetApp.openByUrl("https://docs.google.com/spreadsheets/d/1soOfgdAjik_TL8WX9dDV9BaFb1RQJc8_BVu7sBGnNUE/edit?gid=419710039#gid=419710039");
  if (!tagsSpreadsheet) {
    Logger.log("Tags spreadsheet not found");
    return;
  }
  
  // Get the tags sheet within the tags spreadsheet
  var tagsSheet = tagsSpreadsheet.getSheetByName("tags");
  if (!tagsSheet) {
    Logger.log("Tags sheet not found");
    return;
  }
  
  // Get the range of the transcript column
  var transcriptRange = transcriptSheet.getRange("D2:D" + transcriptSheet.getLastRow());
  var transcriptValues = transcriptRange.getValues();
  
  // Get the range of example words and tags in the tags sheet
  var exampleWordsRange = tagsSheet.getRange("B2:B" + tagsSheet.getLastRow());
  var tagsRange = tagsSheet.getRange("A2:A" + tagsSheet.getLastRow());
  var exampleWordsValues = exampleWordsRange.getValues();
  var tagsValues = tagsRange.getValues();
  
  // Create a map of example words to tags
  var tagsMap = {};
  for (var i = 0; i < exampleWordsValues.length; i++) {
    var word = exampleWordsValues[i][0].toLowerCase();
    var tag = tagsValues[i][0];
    tagsMap[word] = tag;
  }
  
  // Initialize an array to store the tags for each transcript entry
  var transcriptTags = [];
  
  // Loop through each transcript entry
  for (var i = 0; i < transcriptValues.length; i++) {
    var text = transcriptValues[i][0];
    var uniqueTags = [];
    
    if (typeof text === 'string') {
      // Use regular expression to extract words and handle punctuation
      var words = text.match(/\b\w+['-]?\w*|\w+['-]?\w*\b/g);
      
      // Check each word in the transcript entry against the tags map
      if (words) {
        for (var j = 0; j < words.length; j++) {
          var word = words[j].toLowerCase().replace(/[.,!?;:()]/g, '');
          var singularWord = word.endsWith('s') ? word.slice(0, -1) : word;
          
          if (tagsMap.hasOwnProperty(word) && !uniqueTags.includes(tagsMap[word])) {
            uniqueTags.push(tagsMap[word]);
          } else if (tagsMap.hasOwnProperty(singularWord) && !uniqueTags.includes(tagsMap[singularWord])) {
            uniqueTags.push(tagsMap[singularWord]);
          }
        }
      }
    }
    
    // Add the determined tags to the array
    transcriptTags.push([uniqueTags.join(";")]);
  }
  
  // Get the range of the tags column in the transcript sheet, starting from E2
  var tagsColumn = transcriptSheet.getRange("E2:E" + (transcriptTags.length + 1));
  
  // Set the values in the tags column to the determined tags
  tagsColumn.setValues(transcriptTags);
}
```
## About the Author

Andrew Weymouth is the Digital Initiatives Librarian at University of Idaho, specializing in static web design to curate the institution’s special collections and partner with faculty and students on fellowship projects. His work spans digital scholarship projects at the universities of Oregon and Washington and the Tacoma Northwest Room archives, including long form audio public history projects, architectural databases, oral history and network visualizations. He writes about labor, architecture, underrepresented communities and using digital methods to survey equity in archival collections. 