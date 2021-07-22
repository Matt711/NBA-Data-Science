# Which player was most similar to LeBron James in terms of statistical output last season? Kawhi Leonard?

### In this project, I would like to find players most similar to each of the best players in the NBA! Based on player efficiency rating, the best players in the NBA last year(2018-19) were Giannis Antetokounmpo, James Harden, Anthony Davis, Karl-Anthony Towns, Nikola Jokić, Joel Embiid, Kawhi Leonard, LeBron James, and Nikola Vučević, Rudy Gobert, Stephen Curry, Kyrie Irving, and Kevin Durant.


```python
from IPython.display import HTML, display
import tabulate
table = [["Player", "PER"],
         ["Giannis Antetokounmpo", 30.89],
         ["James Harden", 30.57],
         ["Anthony Davis", 30.26],
         ["Karl-Anthony Towns", 26.32],
        ["Nikola Jokić", 26.31],
        ["Joel Embiid", 26.14],
        ["Kawhi Leonard", 25.82],
        ["LeBron James", 25.58],
        ["Nikola Vučević", 25.45],
        ["Rudy Gobert", 24.56],
        ["Stephen Curry", 24.4],
        ["Kyrie Irving", 24.3],
        ["Kevin Durant", 24.2]]
'''
POI = [player[0] for player in table]
POI.pop(0)
print(POI)
'''
display(HTML(tabulate.tabulate(table, tablefmt='html')))
```


<table>
<tbody>
<tr><td>Player               </td><td>PER  </td></tr>
<tr><td>Giannis Antetokounmpo</td><td>30.89</td></tr>
<tr><td>James Harden         </td><td>30.57</td></tr>
<tr><td>Anthony Davis        </td><td>30.26</td></tr>
<tr><td>Karl-Anthony Towns   </td><td>26.32</td></tr>
<tr><td>Nikola Jokić         </td><td>26.31</td></tr>
<tr><td>Joel Embiid          </td><td>26.14</td></tr>
<tr><td>Kawhi Leonard        </td><td>25.82</td></tr>
<tr><td>LeBron James         </td><td>25.58</td></tr>
<tr><td>Nikola Vučević       </td><td>25.45</td></tr>
<tr><td>Rudy Gobert          </td><td>24.56</td></tr>
<tr><td>Stephen Curry        </td><td>24.4 </td></tr>
<tr><td>Kyrie Irving         </td><td>24.3 </td></tr>
<tr><td>Kevin Durant         </td><td>24.2 </td></tr>
</tbody>
</table>


### In order to find out how similar players are from one another I need to find stats on every single NBA player in the 2018-19 season. One excellent resourse I found was https://www.basketball-reference.com/. They have a page that list the per game stats for every single player in the NBA per year. So I used the request and beautiful soup libaries to get the the relavent stats from the website.


```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
import pandas as pd
import numpy as np
```


```python
# NBA season we will be analyzing
year = 2019
# URL page we will scraping 
url = "https://www.basketball-reference.com/leagues/NBA_{}_per_game.html".format(year)
# this is the HTML from the given URL
html = urlopen(url)
soup = BeautifulSoup(html, features = "lxml")
```


```python
# use findALL() to get the column headers
soup.findAll('tr', limit=2)
# use getText()to extract the text we need into a list
headers = [th.getText() for th in soup.findAll('tr', limit=2)[0].findAll('th')]
# exclude the first column as we will not need the ranking order from Basketball Reference for the analysis
headers = headers[1:]
print(headers)
```

    ['Player', 'Pos', 'Age', 'Tm', 'G', 'GS', 'MP', 'FG', 'FGA', 'FG%', '3P', '3PA', '3P%', '2P', '2PA', '2P%', 'eFG%', 'FT', 'FTA', 'FT%', 'ORB', 'DRB', 'TRB', 'AST', 'STL', 'BLK', 'TOV', 'PF', 'PTS']
    

### I wanted to use all of the headers available on Basketball-Reference except for the player's name or team,. I didn't use player name as a feature for similarity because I'm interested in the similarity between two in terms of their statistical output, not their names. For example, players like Paul George, Paul Millsap, and Chris Paul would be more similar because they each have Paul in their name: even though their statistical out put for the 2018-19 season were very different. The same argument holds for team name.


```python
# avoid the first header row
rows = soup.findAll('tr')[1:]
player_stats = [[td.getText() for td in rows[i].findAll('td')]
            for i in range(len(rows))]
stats = pd.DataFrame(player_stats, columns = headers)
positions = stats['Pos']
stats.replace('', 0, inplace=True)
#PG-1, SG-2, SF-3, PF-4, C-5
stats.replace('PG', 1, inplace=True)
stats.replace('SG', 2, inplace=True)
stats.replace('SF-SG', 2.5, inplace=True)
stats.replace('SG-SF', 2.5, inplace=True)
stats.replace('SF', 3, inplace=True)
stats.replace('PF-SF', 3.5, inplace=True)
stats.replace('SF-PF', 3.5, inplace=True)
stats.replace('SG-PF', 3, inplace=True)
stats.replace('PF-SG', 3, inplace=True)
stats.replace('PF', 4, inplace=True)
stats.replace('C-PF', 4.5, inplace=True)
stats.replace('PF-C', 4.5, inplace=True)
stats.replace('C', 5, inplace=True)
names = stats['Player']
stats.drop(columns  = ['Tm', 'Player', 'Age'], inplace = True)
from scipy.spatial.distance import pdist
print(stats)
```

         Pos   G  GS    MP   FG   FGA   FG%   3P  3PA   3P%  ...   FT%  ORB  DRB  \
    0    2.0  31   2  19.0  1.8   5.1  .357  1.3  4.1  .323  ...  .923  0.2  1.4   
    1    4.0  10   0  12.3  0.4   1.8  .222  0.2  1.5  .133  ...  .700  0.3  2.2   
    2    1.0  34   1  12.6  1.1   3.2  .345  0.7  2.2  .338  ...  .778  0.3  1.4   
    3    5.0  80  80  33.4  6.0  10.1  .595  0.0  0.0  .000  ...  .500  4.9  4.6   
    4    5.0  82  28  23.3  3.4   5.9  .576  0.0  0.2  .200  ...  .735  2.0  5.3   
    ..   ...  ..  ..   ...  ...   ...   ...  ...  ...   ...  ...   ...  ...  ...   
    729  5.0   4   1  20.5  4.0   7.0  .571  0.0  0.0     0  ...  .778  2.3  2.3   
    730  5.0  59  25  18.3  3.1   5.6  .553  0.0  0.0     0  ...  .705  1.8  3.6   
    731  5.0  59  37  17.6  3.6   6.4  .559  0.0  0.0     0  ...  .802  1.9  4.2   
    732  5.0  33  12  15.6  3.4   5.8  .580  0.0  0.0     0  ...  .864  1.6  3.3   
    733  5.0  26  25  20.2  3.8   7.2  .538  0.0  0.0     0  ...  .733  2.3  5.3   
    
         TRB  AST  STL  BLK  TOV   PF   PTS  
    0    1.5  0.6  0.5  0.2  0.5  1.7   5.3  
    1    2.5  0.8  0.1  0.4  0.4  2.4   1.7  
    2    1.8  1.9  0.4  0.1  0.8  1.3   3.2  
    3    9.5  1.6  1.5  1.0  1.7  2.6  13.9  
    4    7.3  2.2  0.9  0.8  1.5  2.5   8.9  
    ..   ...  ...  ...  ...  ...  ...   ...  
    729  4.5  0.8  0.3  0.8  1.0  4.0  11.5  
    730  5.4  0.9  0.2  0.4  1.0  1.9   7.8  
    731  6.1  1.1  0.2  0.9  1.2  2.3   8.9  
    732  4.9  0.8  0.1  0.8  1.0  2.2   8.5  
    733  7.7  1.5  0.4  0.9  1.4  2.5   9.4  
    
    [734 rows x 26 columns]
    

### After getting the per game stats for every NBA player in the 2018-19 season, I needed to find the similarity between the stats. I did this by viewing each player as a feature vector with entries corresponding to the 27 per game stats that I kept track of. I then used a metric called cosine similarity which measures the cosine of the angle between two vectors in multi-dimensional space. The smaller the cosine similarity, the closer the feature vectors, the more similar the players are to each other. 


```python
from scipy.spatial.distance import squareform
#print(squareform(pdist(stats.loc[[150, 258, 301]])))

similarities = pd.DataFrame(
    squareform(pdist(stats, metric = 'cosine')),
    columns = names,
    index = names
)
#POI = ['LeBron James', 'Giannis Antetokounmpo', 'Kevin Durant', 'Kawhi Leonard', 'James Harden', 'Stephen Curry', 'Russell Westbrook']
POI = ['Giannis Antetokounmpo', 'James Harden', 'Anthony Davis', 'Karl-Anthony Towns', 'Nikola Jokić', 'Joel Embiid', 'Kawhi Leonard', 'LeBron James', 'Nikola Vučević', 'Rudy Gobert', 'Stephen Curry', 'Kyrie Irving', 'Kevin Durant']
tables = []
for player in POI:
    top10 = np.argpartition(np.array(similarities[player]), 11)[:11]
    top10names = [[names[i], positions[i], similarities[player][i]] for i in top10]
    #top10names.remove([player, positions 0.0])
    tables.append(top10names)
tables = [pd.DataFrame(table, columns = ["Player", "Position", "S-Score"]).sort_values(by=['S-Score']) for table in tables]
```


```python
from matplotlib import pyplot as plt
import seaborn as sns
stats = pd.DataFrame(player_stats, columns = headers)
print(tables[0]) #John Collins is better than I thought
players = tables[0]['Player']
def indexFromPlayer(name):
    return int(stats[stats['Player']==name].index.values)
def playerFromIndex(index):
    return list(stats['Player'])[index]
somestats = stats.loc[[indexFromPlayer(name) for name in list(players)]]
stats.replace('', 0, inplace=True)
#PG-1, SG-2, SF-3, PF-4, C-5
somestats.replace('PG', 1, inplace=True)
somestats.replace('SG', 2, inplace=True)
somestats.replace('SF-SG', 2.5, inplace=True)
somestats.replace('SG-SF', 2.5, inplace=True)
somestats.replace('SF', 3, inplace=True)
somestats.replace('PF-SF', 3.5, inplace=True)
somestats.replace('SF-PF', 3.5, inplace=True)
somestats.replace('SG-PF', 3, inplace=True)
somestats.replace('PF-SG', 3, inplace=True)
somestats.replace('PF', 4, inplace=True)
somestats.replace('C-PF', 4.5, inplace=True)
somestats.replace('PF-C', 4.5, inplace=True)
somestats.replace('C', 5, inplace=True)
somestats.drop(columns  = ['Tm', 'Player', 'Age'], inplace = True)

pairwise = pd.DataFrame(
    squareform(pdist(somestats, metric = 'cosine')),
    columns = players,
    index = players
)
# plot it with seaborn
plt.figure(figsize=(10,10))
sns.heatmap(
    pairwise,
    cmap='OrRd',
    linewidth=1
)
```

                       Player  Position   S-Score
    1   Giannis Antetokounmpo         4  0.000000
    0             Joel Embiid         5  0.002512
    4      Karl-Anthony Towns         5  0.002970
    2            John Collins         4  0.003454
    7       Russell Westbrook         1  0.004210
    6            Kevin Durant         3  0.004437
    5           Blake Griffin         4  0.004755
    3           Kawhi Leonard         3  0.005227
    8             Paul George         3  0.005830
    10          Anthony Davis         5  0.006118
    9             Luka Dončić         2  0.006341
    




    <AxesSubplot:xlabel='Player', ylabel='Player'>




![png](output_11_2.png)



```python
from matplotlib import pyplot as plt
import seaborn as sns
stats = pd.DataFrame(player_stats, columns = headers)
print(tables[1]) #makes sense that Paul George and Devin Booker are most similar
players = tables[1]['Player']
def indexFromPlayer(name):
    return int(stats[stats['Player']==name].index.values)
def playerFromIndex(index):
    return list(stats['Player'])[index]
somestats = stats.loc[[indexFromPlayer(name) for name in list(players)]]
stats.replace('', 0, inplace=True)
#PG-1, SG-2, SF-3, PF-4, C-5
somestats.replace('PG', 1, inplace=True)
somestats.replace('SG', 2, inplace=True)
somestats.replace('SF-SG', 2.5, inplace=True)
somestats.replace('SG-SF', 2.5, inplace=True)
somestats.replace('SF', 3, inplace=True)
somestats.replace('PF-SF', 3.5, inplace=True)
somestats.replace('SF-PF', 3.5, inplace=True)
somestats.replace('SG-PF', 3, inplace=True)
somestats.replace('PF-SG', 3, inplace=True)
somestats.replace('PF', 4, inplace=True)
somestats.replace('C-PF', 4.5, inplace=True)
somestats.replace('PF-C', 4.5, inplace=True)
somestats.replace('C', 5, inplace=True)
somestats.drop(columns  = ['Tm', 'Player', 'Age'], inplace = True)

pairwise = pd.DataFrame(
    squareform(pdist(somestats, metric = 'cosine')),
    columns = players,
    index = players
)
# plot it with seaborn
plt.figure(figsize=(10,10))
sns.heatmap(
    pairwise,
    cmap='OrRd',
    linewidth=1
)
```

                Player  Position   S-Score
    9     James Harden         1  0.000000
    8    Stephen Curry         1  0.003595
    1     Devin Booker         2  0.003943
    3      Paul George         3  0.004014
    5      Zach LaVine         2  0.006140
    4    Kawhi Leonard         3  0.006257
    6     Kyrie Irving         1  0.006388
    0    Blake Griffin         4  0.006853
    2   Damian Lillard         1  0.007000
    7     Kemba Walker         1  0.008092
    10    Kevin Durant         3  0.008203
    




    <AxesSubplot:xlabel='Player', ylabel='Player'>




![png](output_12_2.png)



```python
print(tables[2]) #AD and Embiid were most similar to each other
```

                       Player  Position   S-Score
    0           Anthony Davis         5  0.000000
    3             Joel Embiid         5  0.002008
    2           Kawhi Leonard         3  0.003727
    5            LeBron James         3  0.004198
    1   Giannis Antetokounmpo         4  0.006118
    4           Tobias Harris         4  0.006344
    6         Lauri Markkanen         4  0.008222
    8          Brandon Ingram         3  0.008272
    7             Zach LaVine         2  0.009311
    9            John Collins         4  0.009650
    10           Devin Booker         2  0.009844
    


```python
print(tables[3]) #John Collins again! He's so underrated!
```

                       Player  Position   S-Score
    1      Karl-Anthony Towns         5  0.000000
    3            John Collins         4  0.001430
    0          Nikola Vučević         5  0.001857
    2           Blake Griffin         4  0.002227
    4            Nikola Jokić         5  0.002324
    6            Kevin Durant         3  0.002548
    9             Luka Dončić         2  0.002607
    7       LaMarcus Aldridge         5  0.002731
    8   Giannis Antetokounmpo         4  0.002970
    5        Danilo Gallinari         3  0.003405
    10          Tobias Harris         4  0.003514
    


```python
print(tables[4]) #the -ić's(Luka, Joker, etc.) are so good!
```

                    Player  Position   S-Score
    4         Nikola Jokić         5  0.000000
    5       Nikola Vučević         5  0.001098
    8         Jusuf Nurkić         5  0.001501
    9        Tobias Harris         4  0.001829
    6          Ben Simmons         1  0.002136
    7    LaMarcus Aldridge         5  0.002192
    1        Deandre Ayton         5  0.002315
    3   Karl-Anthony Towns         5  0.002324
    2      Khris Middleton         3  0.002417
    0        Pascal Siakam         4  0.002445
    10        Aaron Gordon         4  0.002495
    


```python
print(tables[5]) #AD and Embiid were most similar to each other
```

                       Player  Position   S-Score
    1             Joel Embiid         5  0.000000
    0           Anthony Davis         5  0.002008
    2   Giannis Antetokounmpo         4  0.002512
    3           Kawhi Leonard         3  0.003970
    4           Tobias Harris         4  0.006296
    5            John Collins         4  0.006729
    9            LeBron James         3  0.006815
    7         Lauri Markkanen         4  0.007065
    6      Karl-Anthony Towns         5  0.007768
    8       Russell Westbrook         1  0.007916
    10            Zach LaVine         2  0.008150
    


```python
print(tables[6]) #Zach Levine, Devin Booker
```

                 Player  Position   S-Score
    0     Kawhi Leonard         3  0.000000
    1       Zach LaVine         2  0.002021
    2      Devin Booker         2  0.002265
    3     Tobias Harris         4  0.003024
    4      LeBron James         3  0.003626
    5     Anthony Davis         5  0.003727
    6       Joel Embiid         5  0.003970
    7    Brandon Ingram         3  0.004328
    8      Jimmy Butler         3  0.004593
    9       Paul George         3  0.004946
    10  Lauri Markkanen         4  0.005211
    


```python
print(tables[7]) #Kawhi Leonard
```

                  Player  Position   S-Score
    4       LeBron James         3  0.000000
    2      Kawhi Leonard         3  0.003626
    1      Anthony Davis         5  0.004198
    3       Devin Booker         2  0.005834
    0      Tobias Harris         4  0.006725
    5        Joel Embiid         5  0.006815
    6        Zach LaVine         2  0.007514
    8   Tim Hardaway Jr.         2  0.008093
    7     Brandon Ingram         3  0.008679
    9     Victor Oladipo         2  0.008818
    10   Lauri Markkanen         4  0.009316
    


```python
print(tables[8]) #the -ić's again
```

                    Player  Position   S-Score
    4       Nikola Vučević         5  0.000000
    2         Nikola Jokić         5  0.001098
    0    LaMarcus Aldridge         5  0.001411
    1        Deandre Ayton         5  0.001433
    3   Karl-Anthony Towns         5  0.001857
    5         Jusuf Nurkić         5  0.002173
    6        Tobias Harris         4  0.002290
    9       Andre Drummond         5  0.002580
    7         Aaron Gordon         4  0.003779
    8      Khris Middleton         3  0.003822
    10        John Collins         4  0.003856
    


```python
print(tables[9]) # Gobert and Jordan...I can see that 
```

                     Player  Position   S-Score
    1           Rudy Gobert         5  0.000000
    4        DeAndre Jordan         5  0.001435
    0          Jusuf Nurkić         5  0.001460
    3          Steven Adams         5  0.001945
    2        Andre Drummond         5  0.002192
    5          Paul Millsap         4  0.002283
    7          Myles Turner         5  0.002553
    6         Pascal Siakam         4  0.002923
    8   Willie Cauley-Stein         5  0.002992
    9         Deandre Ayton         5  0.003213
    10           Marc Gasol         5  0.003513
    


```python
print(tables[10]) #PG can really shoot it
```

                  Player  Position   S-Score
    5      Stephen Curry         1  0.000000
    8        Paul George         3  0.001731
    1       Kyrie Irving         1  0.002949
    3     Damian Lillard         1  0.003380
    2       Kemba Walker         1  0.003520
    6       James Harden         1  0.003595
    9      Blake Griffin         4  0.003913
    4       Bradley Beal         2  0.004063
    7   Tim Hardaway Jr.         2  0.004484
    0        CJ McCollum         2  0.004671
    10  Donovan Mitchell         2  0.004713
    


```python
print(tables[11]) # Every team wishes they had a Jrue Holiday
```

                  Player  Position   S-Score
    0       Kyrie Irving         1  0.000000
    2       Jrue Holiday         2  0.000931
    5       Bradley Beal         2  0.001443
    4        Zach LaVine         2  0.001698
    1   Donovan Mitchell         2  0.001715
    3        CJ McCollum         2  0.001774
    6     Damian Lillard         1  0.001787
    7       Kevin Durant         3  0.001961
    8        Paul George         3  0.002071
    9       Kemba Walker         1  0.002101
    10       Mike Conley         1  0.002113
    


```python
print(tables[12]) # Blake Griffen and Kevin Durant?
```

                  Player  Position   S-Score
    0       Kevin Durant       3.0  0.000000
    1      Blake Griffin       4.0  0.000736
    2       Bradley Beal       2.0  0.000833
    4     Damian Lillard       1.0  0.001143
    3   Donovan Mitchell       2.0  0.001231
    6        Luka Dončić       2.0  0.001432
    5       Kemba Walker       1.0  0.001608
    7        Mike Conley       1.0  0.001631
    8       Kyrie Irving       1.0  0.001961
    9       Jimmy Butler       2.5  0.002130
    10     DeMar DeRozan       2.0  0.002133
    


```python

```
