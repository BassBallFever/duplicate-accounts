# Helping Moderators
## Identifying Who A Duplicate Account Belongs To

## Introduction

Online forum moderators face a number of issues. They monitor the posts to make sure the rules are followed, keeping the forum free of spam, and answering user questions (Internet forums, n.d.). One rule that is common in online forums is that each user has only one account. Occasionally, a user will create a second account for the purposes of spamming the network or to validate their ideas. Moderators need a tool to help them investigate a suspicious account.

The purpose of this exercise is a proof of concept to see if we can correctly identify a potentially fraudulent user based on the text of their post.

A random post was selected 20 separate times and the username was replaced with “target” to test whether the original username could be identified. The target was correctly identified in 90% of the time.

## Objective

Online forum moderators face a number of issues. They monitor the posts to make sure the rules are followed, keeping the forum free of spam, and answering user questions (Internet forums, n.d.). One rule that is common in online forums is that each user has only one account. Occasionally, a user will create a second account for the purposes of spamming the network or to validate their ideas. Moderators need a tool to help them investigate a suspicious account.

The purpose of this exercise is a proof of concept to see if we can correctly identify a potentially fraudulent user based on the text of their post.

## Dataset
### Source

The dataset was collected from TalkBass (TalkBass, n.d.), a forum created for bass players to share their knowledge, experience, and expertise. The collection of users contains beginners all the way up to touring professionals. As a group, their opinions and comments are generally relevant and useful. This is in contrast to opinions and comments gathered from other sources, such as Facebook or Twitter. 

This specific dataset was collected from the thread titled “Noble Amps Preamp,” started on August 11, 2013 (Noble Amps Preamps | TalkBass.com, n.d.). This discussion is about a peripheral that a bass player can use. The conversation has been active since then, with over 2,000 posts. This dataset included posts made in 2019 up through noon on November 20. 

### Data Cleaning

The text was copied from the forum into a CSV file. It was loaded into R where it was processed to prepare it for analysis. 
The first step was to identify how to recognize when a post starts and when it ended, as well as how to extract the username. Inspecting the first 30 lines helped with this endeavor. The first username is on line 2. The last line of each post is “ReportBookmarkLike+ QuoteReply” which represents the buttons that users can use to report, bookmark, like, quote, or reply to the post. A vector was created to store the line numbers of these lines to mark the end of each post. The start of the next post is 2 lines after the last line of the previous post. A vector was created to store these line numbers, starting with 2 and then adding 2 to each end line. The last value of the vector represents the starting position of the post after the last post, so it was dropped.

The starting points still needed to be adjusted. If the post before it had been liked by one or more users, then the post had an additional line that mentioned it. This additional line would mean that the starting point would be the date of the post instead of the username, as was intended. Therefore, it was necessary to check the line before the start. If it ended in “this.”  then the starting point was adjusted by one. Now, the starting vector pointed to the username for each post.

Sometimes another user’s post was quoted. Since these are quotes of somebody else’s post, they need to be removed from the current post. These lines started contained “said: ?” and were removed from the post.

### Examining the Data

The data contains 400 posts written by 77 users.

```r
> length(unique(unlist(usernames)))
[1] 77
> unique(unlist(usernames))
 [1] "pbass6811"         "Spreck76"          "shadowrider"       "g4string"         
 [5] "Flabzilla"         "nbsipics"          "bass08053"         "loopee"           
 [9] "MTN.bass72"        "Ewo"               "Lasse Vuorsola"    "600 Ohms"         
[13] "ExaltBass"         "Jeff Bonny"        "nateynate"         "SteveC"           
[17] "Admiral Akbar"     "Burwabit"          "HD007"             "hennessybass"     
[21] "AngelCrusher"      "Bassheart365"      "Ukiah Bass"        "Judge Nickels"    
[25] "mcnaughb"          "TonyRo"            "ahc"               "Doner Designs"    
[29] "Christian Houmann" "DrewinHouston"     "silvermaneZ"       "jebmd"            
[33] "Chris Fitzgerald"  "jondiener"         "Levin S"           "been_effected"    
[37] "ericsmith"         "jmone"             "Wallow"            "pfschim"          
[41] "extreme"           "Jerry Ziarko"      "Bassist30"         "Bass Boy"         
[45] "Kevan Campbell"    "Kevinlane"         "target"            "Rivoli"           
[49] "Blue Swede"        "jpeeze"            "Lowendtech"        "PFAC"             
[53] "Rocky55"           "campbems"          "rohi"              "Durden26"         
[57] "Sgt007"            "jmbjandfam"        "gkbass13"          "brandinstroy"     
[61] "scubaduba"         "krcriado1990"      "bassdrop"          "Mushroo"          
[65] "Infidelity"        "tedw"              "Lovep"             "5StringPocket"    
[69] "Tom F"             "BenHack"           "agedhorse"         "billyofcourse"    
[73] "She-Ra"            "Slim Pickins"      "Fixitinpost"       "Avalon5"          
[77] "Iv@N"     

```
The following wordcloud shows the most used words in the thread. The most popular word is “noble,” which is no surprise as the thread is about the Noble Amps Preamp. This also explains why amp and preamp are among the top 25 words. Other terms are bass related, such as bass, fender, tone, and sound. Cali76 is a compressor pedal that could be used with or in place of the Noble preamp. 

![](/images/wordcloud.png)

There are several positive terms, such as like, well, great, and good. There are no obvious negative words in this wordcloud.

## Methodology

A post was selected randomly, and the username was replaced with “target” in order to see if we could correctly identify the user based on the text of the post. A list of word frequencies then generated for each user. These frequencies were then compiled into a dataframe which was passed to the xtabs function to deliver a contingency table. 

The contingency table produced a dendrogram of each of the users. The target’s username and ‘target’ were both colored red to aid in identification in the dendrogram. The dendrogram was examined to see if the target was successfully identified. Successful identification is defined as one of two outcomes. The first is if the random post is most closely identified with the targeted user at a height less than 15. The second is if the random post is the only post from the targeted user and it is not related to any other post at a height less than 15. 

## Findings

A random post was selected 20 separate times, with 20 dendrograms produced. The target was successfully identified in 90% of the dendrograms. The dendrograms are located in the repository.

Here we see the result when the random seed was set to 1000.

![](/images/dendat1000.png)

The random post was written by the user pschim. As you can see in the dendrogram, the target was correctly identified as being most closely related to pschim. The closeup shows that the two branches converge at h=12. 

![](/images/closeup.png)

## Conclusions

Online forum moderators need help in order to combat a user with multiple accounts. One way to do this is to use text mining tools to compare suspicious accounts to other user accounts. The use of dendrograms can help identify these accounts.

The findings in this paper are based on viewing the contents of one thread in the forum. This proof of concept looks to be successful, and if it is put into production, the source text can be expanded. All of the posts from the suspicious account can be used to compare to all other posts in the forums to see if there is indeed a link between it and any other account.

## References

Internet forums. (n.d.). Retrieved from Wikipedia: https://en.wikipedia.org/wiki/Internet_forum#Moderators

Noble Amps Preamps | TalkBass.com. (n.d.). Retrieved from TalkBass: https://www.talkbass.com/threads/noble-amps-preamp.1006334/

TalkBass. (n.d.). Retrieved from TalkBass: www.TalkBass.com
