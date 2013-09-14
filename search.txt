#!/usr/bin/python -tt
import os
import re


""" Opens the directory ! Remember to change it so it fits your Korpus folder """
di = os.path.dirname(os.path.realpath('__file__')) + "/Korpus/";  
d  = os.listdir(di)

""" Filters the directory so we only get .txt files and sorts the list alphabetically """
newList = [s for s in d if s.endswith('.txt')];
newList.sort();

""" Defines illegal search patterns """
illegal = ["\n","\t","\r",None,"", " "]; 


"""userinput()
 Prompts the user for input and checks for illegal search patterns
"""
def userinput():
    usersearch = raw_input('Search: ').decode('cp437').encode('cp1252');
    commands(usersearch);


"""commands(input)
Checks the user-input for either -quit, -help or any illegal input.
If none of these are found it'll prepare the search array
"""
def commands(input):
    if input == "-quit":
        print("Quit successfully"); 
        raise SystemExit();
        

    elif input in illegal:
        print "Invalid input"
        userinput(); 

    elif input == "-help":
        print "\n### PySearch Alex 1.0 alpha 2012 by Alexander Wahl-Rasmussen.";
        print "---- \n # Commands # \n Type a single word or pattern to search for it in all combinations. \n Place double-quotes (\"\") around your search-term to search for it as";
        print  " a stand-alone word or sentence. \n Use comma (,) to separate multiple search-entries.";
        print 'e.g. Search: "This, will be a standalone search",hello,queryone ### \n';
        userinput();

        """ Prepares the search array by finding the search patterns in quotes and mark them with a quote (").
            Then it grabs the rest of the search patterns and adds both variables to a single filtered searcharray """
    else:   
        arrayquotes = re.findall(r'\"(.*)\"',input); 
        standalone = map(lambda x: x + "\"", arrayquotes);
        
        singlewords = re.sub(r'\"(.*)\"',"",input);

        array = filter(lambda a: a!="", standalone + singlewords.split(",")); # removes empty list elements

        usearch(array); 


"""usearch (inputarray)
This function searches for the user specified pattern
and prints the file names in which the pattern appears 
"""
def usearch(inputarray):
    count = 0;

    for elem in newList:
        foundWords = [];
        try:
            f = open(di + elem,"r");
            text = f.read().decode('latin1').encode('cp1252'); # Reads the text and encodes it to match our script coding
        except IOError:
            print "There was an error opening " + elem + "."; # If the file is corrupt or in use, opening may fail
            f.close();
            userinput(); 

        """ For loop that searches the texts for the whole words or sentences and adds the found words to a list.
            Then searches the texts for unquoted patterns and adds the found words to the beforementioned list """

        for word in inputarray:
            if (word[-1] == "\""):  
                result = re.findall(r'\W+'+word[:-1]+"\W",text) 
                if len(result) > 0:
                    foundWords.append(word[:-1]); 
                
            elif (text.find(word) <> -1): 
                foundWords.append(word);     

        if len(foundWords) == len(inputarray):
            print "Pattern(s) "+ str(foundWords) + " were found in document "+ str(newList.index(elem)+1) + " (" +  str(elem) + ")";
        else:
            count = count+1;

    # If count reaches the full length of texts it means no results were found 
    if count == len(newList):
        print "No results were found"

    userinput();

    
"""main()
 Starts the programme by calling the userinput-function
"""
def main():
    print ">>> PySearch Alex v1.0 (Dec 2012) \n Type -help for commands and about information. Type -quit to quit. \n";
    userinput();
if __name__ =='__main__':
    main();  
