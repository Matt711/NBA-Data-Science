### In this project, I wanted to find players in the NBA most similar to the best players in the world! To find the best players, I ranked ordered the players by Player Efficiency Rating (PER: A measure of per-minute production standardized such that the league average is 15.). By PER, the best players in the NBA last season were: (Source: https://www.basketball-reference.com/leagues/NBA_2019_advanced.html#advanced_stats::per)
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
<tr><td>Clint Capela         </td><td>23.8 </td></tr>
<tr><td>Damien Lillard         </td><td>23.7 </td></tr>
</tbody>
</table>

### Of course, there are problems with PER: The primary one is that is it is primarily an offensive statistic that's biased toward volume shooting and rebounding
PER formula bball-ref: https://www.basketball-reference.com/about/per.html
### But the reason for PER (from John Hollinger) is that it "sums up all a player's positive accomplishments, subtracts the negative accomplishments, and returns a per-minute rating of a player's performance." But I think PER works in a kind of casual way. So if you want to get a quick snapshot of some of the best NBA players, then PER is fine.



### To find out how similar players are, I found stats on every single NBA player in the 2018-19 season. The resource I used was https://www.basketball-reference.com/. They have a page that lists the per 100 possessions stats for every player in the NBA per year. So I used some web scraping functionality from the Pandas library to get the data.

### Once I had the data, I did some cleaning and transformation. The first transformation I did was remove the header rows. Then I filled the NaN values with zeros. I considered this safe because most NaN values came from players with 0 percentage stats (3PT%, FT%, etc.). Then I converted all data types in the array numbers to floating-point numbers. Finally, because the 'Pos' column in the table has strings, I converted that column to floats using the following conversion PG-1, SG-2, SF-3, PF-4, C-5.

### I wanted to use all of the headers available on Basketball-Reference except for the player's name or team. I didn't use the player's name as a feature for similarity because I'm interested in the similarity between two players in terms of their statistical output, not their names. So, for example, players like Paul George, Paul Millsap, and Chris Paul would be more similar because they each have Paul in their name: even though their statistical output for the 2018-19 season was very different. The same argument holds for the team name.

### The headers or features I used were:

Index(['Pos', 'Age', 'G', 'GS', 'MP', 'FG', 'FGA', 'FG%','3P', '3PA', '3P%', '2P', '2PA', '2P%', 'FT', 'FTA', 'FT%', 'ORB','DRB', 'TRB', 'AST', 'STL', 'BLK', 'TOV', 'PF', 'PTS'], dtype='object')


### After getting the per 100 possessions stats for every NBA player in the 2018-19 season, I needed to find the similarity scores between the stats. I did this by viewing each player as a feature vector with entries corresponding to the 26 stats that I kept track of. I then used a metric called cosine similarity which measures the cosine of the angle between two vectors in multi-dimensional space. The smaller the cosine similarity, the closer the feature vectors and the more similar the players are. From the scipy library,

![original image](https://cdn.mathpix.com/snip/images/qiwMKkKe3Hq55DPYuJqomFWYXFZYMxspXMGb29CQK7U.original.fullsize.png)

### Findings and Conclusion: Most of the players that I found were not suprising (as they are already well known). But I did find a few underrated players:

###  John Collins (Hawks), Lauri Markkanen (Giannis), Danilo Gallinari, TJ Warren, Tim Hardaway Jr., Buddy Hield, Kris Dunn, Josh Richardson
<table>
<tbody>
<tr><td>Player</td><td>Team</td><td>Player Similar to</td></tr>
<td>John Collins</td><td>Hawks</td><td>AD</td>
<td>Lauri Markkanen</td><td>Bulls</td><td>Giannis</td>
<td>Danilo Gallinari</td><td>Clippers</td><td>AD</td>
<td>TJ Warren</td><td>Suns</td><td>AD</td>
<td>Buddy Hield</td><td>Kings</td><td>AD</td>
<td>Kris Dunn</td><td>Bulls</td><td>AD</td>
<td>Josh Richardson</td><td>Heat</td><td>AD</td>
</tbody>
</table>

### Future Work: I want to extend this analysis to multiple seasons (perhaps find player similarity scores to the best players of all time?) Include more meaningful features (or new data entirely like player tracking data). This kind of analysis could help General Managers draft and trade for underrated players. That is very valuable!


### Feel free to play with the notebook yourself!
```bash
conda env create -f environment.yml
```