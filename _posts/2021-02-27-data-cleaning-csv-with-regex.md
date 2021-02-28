---
title: 'Wrangling Humanities Data: Using Regex to Clean a CSV'
date: 2021-02-28
published: false
permalink: /posts/2021/data-cleaning-csv-with-regex/
excerpt: 'Have you heard of regular expressions before and wondered how to make use of them? This post is for someone who has asked this question. It assumes a basic understanding of "regex" and shows how to use a full-featured text editor to cleanup plain text data.'
header:
  overlay_image: "binary-1280w.jpg"
  overlay_filter: 0.3
  image_description: "An image representing binary data with rows and columns of 0s and 1s with a blue background."
  caption: "Photo by [Gerd Altmann](https://pixabay.com/illustrations/binary-digitization-null-one-pay-1377017/) on Pixabay"
  teaser: "binary-th.jpg"
  og_image: "binary-th.jpg"
categories:
  - data curation
tags:
  - regex
  - humanities data
---

Have you heard of regular expressions before and wondered how to make use of them? This post is for someone who has asked this question. It assumes a basic understanding of "regex" and shows how to use a full-featured text editor to cleanup plain text data. The data in question comes from a larger project, which is pulling bibliographic data from a major citation database in CSV form, transforming the data and extracting certain elements (DOIs of publications), then feeding the information into Zotero to create a shared bibliography. 

* Explain some of the regex patterns and techniques to clean a broken CSV. 

The Covid-19 pandemic, now underway for nearly a year, has brought about major reorientations in health and bioscience research. This has been seen in the work to develop new vaccines, investigations about the effectiveness of public health measures, and the direct impact as well as cascading effects of the disease on particular communities, among many other areas. At research institutions, like many large state universities, active research into the novel coronavirus has ramped up, and many existing research projects and labs have been reoriented to investigate the virus and the disease. This has resulted in an outpouring of medical, scientific, and social research publications. At the University of Michigan, the Office of Research has been tracking these publications since April. As with so many things involving the Covid pandemic, our work has been responsive, but quickly changing and developing to the new situation; in the last ten months, we have honed the process of identifying citations, identified multiple ways to present the list of publications, and reworked the workflow to gather and process the data. 

In this post, I will explain how I've been using advanced text editors and pattern recognition routines to parse and clean the data we're gathering. Specifically, I will demonstrate how I use [Virtual Studio Code (aka VSCode)](https://code.visualstudio.com/) to clear up some data quality issues, including multi-point editing and regular expression strings to identify patterns for correction. If you are looking for a text editor, the [wikipedia comparison of text editors](https://en.wikipedia.org/wiki/Comparison_of_text_editors) is a good place to start; in the past, I have used TextWrangler, Sublime, Brackets, and Atom, but at present VSCode is an excellent option. In future, I plan to add another post or two to this that will explain the data workflow in more detail, since this is only one of many steps. The outcome of the workflow is to create the list of publications that are included in a publicly-available bibliography at [https://myumi.ch/3qnOG](https://myumi.ch/3qnOG) (via Zotero).

Okay, let's get into text editors and regex!

## Using the text editor

![png]({{ site.url }}{{ site.baseurl }}/images/wrangling-humanities-data/vscode-csv-editing.png)
Above, the VSCode interface. Here displayed is the CSV file that I discuss in more detail below. Note that the rows are very easy to distinguish and individual fields as noted in the header row are color-coded, which makes the data somewhat easier to read in lower rows. Various extensions can be added to VSCode to aid in the processing of CSV files, which are available in the 

## Power editing with keyboard commands

One of the first things I noted when viewing the file is that the first line is not one that is necessary for my project (it provides information about the file and the specific search string that was used to generate the result), and the second line contains the field headers. 

So, I deleted the first line. In VSCode, you can double click on the line to highlight it, then delete. Or, if you want to use the keyboard, position the cursor on the line you want to delete, then type `Ctrl + X` (`Cmd + X` on MacOS). 

VS Code is full of handy keyboard shortcuts, and you can even create new ones! If you use shortcuts frequently, there are lists of useful shortcuts, like [this one from VS Code developers for Windows users](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf), or [this one for Mac users](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf). Print one out and keep it beside your desk until you learn them all! Or, see the many lists generated by other shortucut users, like [this list from Deepak Gupta](https://medium.com/better-programming/20-vs-code-shortcuts-for-fast-coding-cheatsheet-10b0e72fd5d), for additional useful keyboard commands in VSCode. You can even create new shortcuts, which are called "key bindings," [within VSCode using built-in features](https://code.visualstudio.com/docs/getstarted/keybindings).

## Finding errors with regular expressions

Regular expressions are a useful set of pattern-searching techniques, which allow you to find very specific patterns within text. For example, have you ever searched in multiple sites and noticed that you want to find things with both British and American spellings? For example, all of the times where the word digitize appears, but you know that it might be spelled digitize or _digitise_? Regular expressions can help! In this case, if you have a search tool that can search with regular expressions, you could input the string `digiti[sz]e`, and it would be able to match either spelling. The regular expression syntax is complicated and can be quite powerful, but I am only going to go into a few specific search expressions in this post. If you're interested to learn more about regular expressions, or "regex" as they are often called, check out the [introduction from Library Carpentry](https://librarycarpentry.org/lc-data-intro/01-regular-expressions/index.html), or search for one of the many cheatsheets available online, such as this [Regex cheat sheet from MIT](http://web.mit.edu/hackl/www/lab/turkshop/slides/regex-cheatsheet.pdf).

![png]({{ site.url }}{{ site.baseurl }}/images/wrangling-humanities-data/vscode-csv-find-replace.png)
The find and replace console (above) appears at the upper right corner of the VSCode window. You can bring up the window by typing `Ctrl + F` (`Cmd + F` on MacOS) or opening the `Edit` pull-down menu and selecting `Find`.

Once you have identified some the regex that you want to use, you can use a text editor like VSCode to search for the patterns that match your regex. Regex also offers more advanced usage that supports selecting and changing certain patterns. That is not what I am discussing here, but you can learn more about that functionality in [Bohyun Kim's post at the ACRL TechConnect Blog](https://acrl.ala.org/techconnect/post/fear-no-longer-regular-expressions/). 

In this post, I am using basic regex in this example to identify certain patterns that create clear errors in a CSV document that I received. When I opened the file, I noticed that many lines included unescaped commas, non-alphanumeric characters, tabs, or other strange formatting that caused the CSV to be inaccurate. While fixing the CSV and preserving the data would require more refined regex work, I needed to get rid of the errant lines while preserving the lines that were correct, plus the first three columns (I need a list of the DOI entries, which are in the third column). Here's how I used regular expressions and VSCode to do that:

### Identify lines that do not begin with a numerical index

The lines that were not "broken" all began with a number. Lines that did not begin with a number were causing problems in the file's format, which caused the file to be an invalid CSV. To identify these lines, I searched for any line that begin with an upper- or lower-case letter, which was not followed by a line beginning with a number:

```regex
^[A-Za-z].*\n(?!^[0-9])
```

Using VSCode, I selected each of these patterns by using the "multi-cursor" option in VSCode. To select all of the matching lines, type `Shift + Ctrl + L` (or `Shift + Cmd + L` on MacOS). I made sure the cursor was at the beginning of each of these lines, then deleted the selected text to remove the unwanted lines. 

### Identify blank lines

```regex
^\s*$
```

### Identify lines that begin with blank spaces or tabs

In this case, the tabs are represented by sequences of 6 or 8 spaces in sequence. These are not picked up by my search for blank lines since, although they have blank spaces at the beginning of the line, there are other characters later in the line. 

```regex
^[\s]{6}.*\n(?!^[0-9])
```

Upon inspecting the remaining lines, I noticed that many of the lines were not blank, but they did begin with spaces or tab characters. Most of the tabs (though not all) were converted to sequences of 6 or 8 spaces. This regex matches lines with 6 blank characters at the beginning of the line, plus any characters following these up to a line break (`\n`). Then, using the "look around" pattern (parentheses at the end of the line), the pattern checks the following line to see if it begins with a numeral; if the line does begin with a numeral, then the line is skipped and not matched. The reason for skipping the trailing line is to reduce the possibility of deleting needed information and fields. Using the multiple select, all of this text can be selected and deleted.

![png]({{ site.url }}{{ site.baseurl }}/images/wrangling-humanities-data/regex-6-blanks-not-preceeding-numeral-at-beginning-of-line.png) 
This is how [regexper visualizes](https://regexper.com/#%5E%5B%5Cs%5D%7B6%7D.*%5Cn%28%3F!%5E%5B0-9%5D%29) the match.

```regex
^[\t]
```

Match lines that begin with tabs. Should try to match so it doesn't select tabs before lines beginning with numbers.

Note: I developed this initially when I noted that some lines had two tabs (8 spaces) at the beginning. Later, I realized that many lines also had "1.5" tabs (6 spaces) at the beginning. In fact, all of these would be identified by the pattern `^[\s]{6}`, so there is not a need for the "or" operator here (`|`), but I kept it in since that reflects my initial process. As in the game of "regex golf," I was aiming for the most specific expressions that caught the patterns I was looking for while not identifying any that didn't meet the pattern. 

### Identify any remaining lines wihtout numerical indices

There are a few lines that didn't have letter or space characters at the beginning. Now that the other lines are already gone, I inverted the expression to match anything that _didn't_ have a number at the beginning.

```regex
^[^0-9]+\.(?=")  
```

Match line that does not begin with number, and match characters until it sees a period/dot, which is preceeded by a quotation mark. Strangely, this did not match many of the lines that don't begin with numbers, but it does help to select many of the lines that are broken. Just use the multi-select, then hit delete.

```regex
^"
```

Match any remaining lines that begin with a double quotation mark, use the multi-select, then backspace one to move these up to the end of the preceeding lines.

^[^0-9]*\.
Match non-digit begun lines, any characters until a dot. Multi-select, then delete.

^[^0-9][^"]+

Match lines not beginning with a digit, then match one or more characters until reaching one that is a double quotation... will select text to delete and catches most of the abstracts that do not have multiple sentences. 


## Resources

https://regex101.com/ - add text, test search patterns like `^[^0-9](.*)\.`
https://regexper.com/#%5E%5B%5E0-9%5D%2B%5C.%28%3F%3D%22%29 - put in expressions, visualize what they do
http://www.rexegg.com/regex-lookarounds.html - look ahead and behind patterns
http://web.mit.edu/hackl/www/lab/turkshop/slides/regex-cheatsheet.pdf - cheatsheet
https://acrl.ala.org/techconnect/post/fear-no-longer-regular-expressions/ - ALA intro to regex for library technical services
https://librarycarpentry.org/lc-data-intro/01-regular-expressions/index.html - library carpentry overview