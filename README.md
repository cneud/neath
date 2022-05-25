# neat: named entity annotation tool
---
![Screenshot](https://user-images.githubusercontent.com/952378/76674885-6b7b9000-65b4-11ea-9a36-1f9179dc5d6b.png)
---

### Table of contents
[1. Introduction](https://github.com/qurator-spk/neat/blob/master/README.md#1-introduction) 

[2. User Guide](https://github.com/qurator-spk/neat/blob/master/README.md#2-user-guide)

&nbsp;&nbsp;&nbsp;[2.1 Installation](https://github.com/qurator-spk/neat/blob/master/README.md#21-installation) 
    
&nbsp;&nbsp;&nbsp;[2.2 Data format](https://github.com/qurator-spk/neat/blob/master/README.md#22-data-format)
    
&nbsp;&nbsp;&nbsp;[2.3 Navigation](https://github.com/qurator-spk/neat/blob/master/README.md#23-navigation)
    
&nbsp;&nbsp;&nbsp;[2.4 Saving progress](https://github.com/qurator-spk/neat/blob/master/README.md#24-saving-progress)

[3. Annotation Guidelines](https://github.com/qurator-spk/neat/blob/master/README.md#3-annotation-guidelines)

### 1. Introduction
*neat* is a simple, browser-based tool for editing and annotating text with named entities to produce labeled data for training/testing/evaluation. It can be used to add or correct named entity labels and to correct the token text or tokenization (e.g. due to OCR/segmentation errors). 

*neat* is developed at the [Berlin State Library](https://staatsbibliothek-berlin.de/) for data annotation in the [SoNAR-IDH](https://sonar.fh-potsdam.de/) project and the [QURATOR](https://qurator.ai/) project.

### 2. User Guide

#### 2.1 Installation
*neat* runs locally as a pure HTML+JavaScript webpage in your web browser. No additional software needs to be installed, but JavaScript has to be enabled in the browser.

Clone the repo using ``git clone https://github.com/qurator-spk/neat.git`` or download and extract the [ZIP](https://github.com/qurator-spk/neat/archive/master.zip). Make sure you have ``neat.html`` and ``neat.js`` in the same directory and open ``neat.html`` in a browser. Any fairly recent browser should work, but only Chrome and Firefox are tested.

#### 2.2 Data format
The source data we use for annotation are OCR results in [PAGE-XML](https://github.com/PRImA-Research-Lab/PAGE-XML) format. We provide a [Python tool](https://github.com/qurator-spk/page2tsv) for the transformation of OCR files in [PAGE-XML](https://github.com/PRImA-Research-Lab/PAGE-XML) into the [TSV format](https://github.com/qurator-spk/neat/blob/master/README.md#22-data-format) used by *neat*.

The internal data format used by *neat* is based on the format used in the [GermEval2014 ](https://sites.google.com/site/germeval2014ner/data) Named Entity Recognition Shared Task. Text is encoded as one token per line, with name spans in the [IOB2](https://en.wikipedia.org/wiki/Inside%E2%80%93outside%E2%80%93beginning_(tagging)) format as tab-separated values:
* the first column contains either 
  * `#` a comment to indicate the source the sentence is taken from, or 
  * ``>=1`` the token position within the sentence, or 
  * ``0`` to mark sentence boundaries 
* the second column contains the token ``text`` 
* outer entity spans are encoded in the third column ``NE-TAG``
* embedded entity spans are encoded in the fourth column ``NE-EMB`` 

##### Example (simple)
```tsv
No.	TOKEN	NE-TAG	NE-EMB
# https://example.url
1	Donnerstag	O	O
2	,	O	O
3	1	O	O	
4	.	O	O	
5	Januar	O	O	
6	.	O	O		
0		O	O
1	Berliner	B-ORG	B-LOC	
2	Tageblatt	I-ORG	O	
3	.	O	O		
0		O	O
1	Nr	O	O	
2	.	O	O		
3	1	O	O	
4	.	O	O	
0		O	O
1	Seite	O	O
2	3	O	O
```

For our purposes we extend this format by adding these (optional) values:
* a fifth column for an ``ID`` for the outer ``NE-TAG`` from an authority file (*neat* supports automatic linking for [Wikidata](https://www.wikidata.org) identifiers)
* column six for use as a variable ``url_id`` for [iiif](https://iiif.io/) Image API support (*neat* supports the embedding of image snippets into its interface to assist data annotation and correction if the PAGE-XML source contains word bounding boxes)
* columns 7-10 are used for storing ``left,right,top,bottom`` pixel coordinates for the image snippets 

##### Example (full)
```tsv
No.	TOKEN	NE-TAG	NE-EMB	ID	url_id	left,right,top,bottom
# https://example.url/iiif/left,right,top,bottom/full/0/default.jpg
1	Donnerstag	O	O	-	0	174,352,358,390
2	,	O	O	-	0	174,352,358,390	
3	1	O	O	-	0	367,392,361,381
4	.	O	O	-	0	370,397,352,379
5	Januar	O	O	-	0	406,518,358,386
6	.	O	O	-	0	406,518,358,386	
0
1	Berliner	B-ORG	B-LOC	Q455014	0	816,984,358,388
2	Tageblatt	I-ORG	O	Q455014	0	1005,1208,360,387
3	.	O	O	-	0	1005,1208,360,387
0
1	Nr	O	O	-	0	1237,1288,360,382
2	.	O	O	-	0	1237,1288,360,382
3	1	O	O	-	0	1304,1326,361,381
4	.	O	O	-	0	1304,1326,361,381
0
1	Seite	O	O	-	0	1837,1926,361,392
2	3	O	O	-	0	1939,1967,364,385
```

#### 2.3 Navigation
*neat* can be used both with a [keyboard](https://github.com/qurator-spk/neat#keyboard) or a [mouse](https://github.com/qurator-spk/neat#mouse), but for ergonomic reasons, we strongly recommend the use of below key combinations.  

##### Keyboard
| Key Combination|      Action      |
|:---------|:-------------------------------------------|
| Left     |  Move one cell left                        |
| Right    |  Move one cell right                       |
| Up       |  Move one row up                           |
| Down     |  Move one row down                         |
| PageDown |  Move page down                            |
| PageUp   |  Move page up                              |
| Crtl+Up  |  Move entire table one row up              |
| Crtl+Down|  Move entire table one row down            |
|----------|--------------------------------------------|
| s  t     |  Start new sentence in current row         |
| m  e     |  Merge current row with row above          |
| s  p     |  Create copy of current row                |
| d  l     |  Delete current row                        |
|----------|--------------------------------------------|
| backspace|  Set NE-TAG / NE-EMB to ``O``                |
| b  p     |  Set NE-TAG / NE-EMB to ``B-PER``            |
| b  l     |  Set NE-TAG / NE-EMB to ``B-LOC``            |
| b  o     |  Set NE-TAG / NE-EMB to ``B-ORG``            |
| b  w     |  Set NE-TAG / NE-EMB to ``B-WORK``           |
| b  c     |  Set NE-TAG / NE-EMB to ``B-CONF``           |
| b  e     |  Set NE-TAG / NE-EMB to ``B-EVT``            |
| b  t     |  Set NE-TAG / NE-EMB to ``B-TODO``           |
| i  p     |  Set NE-TAG / NE-EMB to ``I-PER``            |
| i  l     |  Set NE-TAG / NE-EMB to ``I-LOC``            |
| i  o     |  Set NE-TAG / NE-EMB to ``I-ORG``            |
| i  w     |  Set NE-TAG / NE-EMB to ``I-WORK``           |
| i  c     |  Set NE-TAG / NE-EMB to ``I-CONF``           | 
| i  e     |  Set NE-TAG / NE-EMB to ``I-EVT``            |
| i  t     |  Set NE-TAG / NE-EMB to ``I-TODO``           |
|----------|--------------------------------------------|
| enter    | Edit TOKEN or ID                           |
| esc      | Close TOKEN or ID edit field without       |
|          | application of changes                     |
|----------|--------------------------------------------|
| l a      | add one display row                        |
| l r      | remove on display row (minimum is 5)       |
|----------|--------------------------------------------|

##### Mouse
* use mouse wheel to scroll up and down

* left-click `<<` and `>>` to move 15 rows up or down

* left-click `O` in the `NE-TAG` or `NE-EMB` column to open a drop-down menu and subsequently select any of the supported NE-Tags to tag a token or change an existing tag

* left-click the `NE-TAG` or `NE-EMB` column and select `O` to remove a tag

* left-click the `TOKEN` column to edit the token text

* left-click the `POSITION` and select `split` from the drop-down menu to create a copy of the current row below

* left-click the `POSITION` and select `merge` from the drop-down menu to merge the current row with the row above

* left-click the `POSITION` and select `start-sentence` from the drop-down menu to mark the start of a new sentence

#### 2.4 Saving progress
*neat* runs fully locally in the browser. Therefore it can not automatically save any changes you made to disk. You have to use the `Save Changes` button to do so manually from time to time.

### 3. Annotation Guidelines
[Annotation Guidelines](https://zenodo.org/record/5116015) 
