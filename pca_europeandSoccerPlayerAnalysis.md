
# Principal Component Analysis 
# - Preliminary work
# - getting data from an SQL type database (actually SQLite)

I am going to use the European Soccer Database (available from Kaggle) for this project.

Available here : https://www.kaggle.com/hugomathien/soccer/data

Download all - it is a sqlite database simply called database.sqlite

Have a look at the description of the data on Kaggle.

Note that typically a database consists of a number of tables.
Typically you will connect to a database and grab data from a given table
using the SELECT command. A Data Scientist needs to be familiar with getting data from 
a SQL database however typically they are not involved in the database setup and manangement - that is
usually done by a Database engineer.  But you must be able to connect and grab data.
SQLite is a "light" SQL database. Please read up on the differences between uses of SQLite and say a full server
Relational Database Management System (RDMS). Fortunately both use similar commands to aquire data from the database
so learning these commands is crucial.


```python
#Firstly we will load some libraries
from __future__ import absolute_import
from __future__ import division
from __future__ import print_function
import numpy as np
import sqlite3
import matplotlib.pylab as plt
np.set_printoptions(precision=15)

# Now we will connect to the database by creating a connection object called conn.

conn = sqlite3.connect('database.sqlite')

# We can create a cursor object using the conn object method
# The cursor object has nethods for accessing the data

c = conn.cursor()

# Get the database table list from information in the sqlite_master table
# It is conventional to type SQL commands in all caps

# Set the execute SQL command
c.execute("SELECT name FROM sqlite_master WHERE type='table';")

print ('======')
print ('Tables')
print ('======')
# Fetch all data that accords with the execute command above
print(c.fetchall())

# Set the execute SQL command for getting table information from table called Player
c.execute("PRAGMA table_info(Player);")
# Fetch all data that accords with the execute command above
print ('=======================')
print ('Columns in Player Table')
print ('=======================')
print(c.fetchall())

# Now select the data (height and weight) from the Player Table
# Note height is in cm and weight is in lbs

# Set the execute SQL command for getting height and weight columns of data
# from table called Player
c.execute('SELECT height, weight FROM Player')

# Fetch all data that accords with the execute command above
# NOTE: This command returns a list of tuples!!

allrows = c.fetchall()

# In the code below I have created a list called soccerlist.
# Then I have populated the list with the rows gathered from 
# the database. Each row has two variables - height and weight
# so that 

soccerlist = []
i = 0
for row in allrows:
    # I choose to only grab  the first 2000 records
    if i < 2000:
        # NOTE: That row is a tuple!!
        # We can easily convert it to a list with the list() function before appending it
        # We are creating a 2000 length list of 2 length lists (height, weight)
        soccerlist.append(list(row))
    i = i + 1
    
#Change list into an array    
soccerarray = np.array(soccerlist, dtype=float)

print ('=======================================')
print ('The array of player heights and weights')
print ('=======================================')
print (soccerarray)

# I use this numpy command to make sure there are no NaNs in the array
height_weight = soccerarray[~np.isnan(soccerarray).any(axis=1)]

with plt.style.context('seaborn-whitegrid'):
    plt.figure(figsize=(6, 4))
    plt.scatter(height_weight[:,0], height_weight[:,1])
    plt.xlabel('Height (cm)')
    plt.ylabel('Weight (lbs)')
    plt.legend(loc='lower center')
    plt.tight_layout()
    plt.show()
```

    ======
    Tables
    ======
    [('sqlite_sequence',), ('Player_Attributes',), ('Player',), ('Match',), ('League',), ('Country',), ('Team',), ('Team_Attributes',)]
    =======================
    Columns in Player Table
    =======================
    [(0, 'id', 'INTEGER', 0, None, 1), (1, 'player_api_id', 'INTEGER', 0, None, 0), (2, 'player_name', 'TEXT', 0, None, 0), (3, 'player_fifa_api_id', 'INTEGER', 0, None, 0), (4, 'birthday', 'TEXT', 0, None, 0), (5, 'height', 'INTEGER', 0, None, 0), (6, 'weight', 'INTEGER', 0, None, 0)]


    No handles with labels found to put in legend.


    =======================================
    The array of player heights and weights
    =======================================
    [[182.88 187.  ]
     [170.18 146.  ]
     [170.18 163.  ]
     ...
     [180.34 176.  ]
     [185.42 170.  ]
     [180.34 172.  ]]



![png](output_2_3.png)



```python
c.execute("PRAGMA table_info(Player_Attributes);")
# Fetch all data that accords with the execute command above
print ('=======================')
print ('Columns in Player_attributes Table')
print ('=======================')
print(c.fetchall())
```

    =======================
    Columns in Player_attributes Table
    =======================
    [(0, 'id', 'INTEGER', 0, None, 1), (1, 'player_fifa_api_id', 'INTEGER', 0, None, 0), (2, 'player_api_id', 'INTEGER', 0, None, 0), (3, 'date', 'TEXT', 0, None, 0), (4, 'overall_rating', 'INTEGER', 0, None, 0), (5, 'potential', 'INTEGER', 0, None, 0), (6, 'preferred_foot', 'TEXT', 0, None, 0), (7, 'attacking_work_rate', 'TEXT', 0, None, 0), (8, 'defensive_work_rate', 'TEXT', 0, None, 0), (9, 'crossing', 'INTEGER', 0, None, 0), (10, 'finishing', 'INTEGER', 0, None, 0), (11, 'heading_accuracy', 'INTEGER', 0, None, 0), (12, 'short_passing', 'INTEGER', 0, None, 0), (13, 'volleys', 'INTEGER', 0, None, 0), (14, 'dribbling', 'INTEGER', 0, None, 0), (15, 'curve', 'INTEGER', 0, None, 0), (16, 'free_kick_accuracy', 'INTEGER', 0, None, 0), (17, 'long_passing', 'INTEGER', 0, None, 0), (18, 'ball_control', 'INTEGER', 0, None, 0), (19, 'acceleration', 'INTEGER', 0, None, 0), (20, 'sprint_speed', 'INTEGER', 0, None, 0), (21, 'agility', 'INTEGER', 0, None, 0), (22, 'reactions', 'INTEGER', 0, None, 0), (23, 'balance', 'INTEGER', 0, None, 0), (24, 'shot_power', 'INTEGER', 0, None, 0), (25, 'jumping', 'INTEGER', 0, None, 0), (26, 'stamina', 'INTEGER', 0, None, 0), (27, 'strength', 'INTEGER', 0, None, 0), (28, 'long_shots', 'INTEGER', 0, None, 0), (29, 'aggression', 'INTEGER', 0, None, 0), (30, 'interceptions', 'INTEGER', 0, None, 0), (31, 'positioning', 'INTEGER', 0, None, 0), (32, 'vision', 'INTEGER', 0, None, 0), (33, 'penalties', 'INTEGER', 0, None, 0), (34, 'marking', 'INTEGER', 0, None, 0), (35, 'standing_tackle', 'INTEGER', 0, None, 0), (36, 'sliding_tackle', 'INTEGER', 0, None, 0), (37, 'gk_diving', 'INTEGER', 0, None, 0), (38, 'gk_handling', 'INTEGER', 0, None, 0), (39, 'gk_kicking', 'INTEGER', 0, None, 0), (40, 'gk_positioning', 'INTEGER', 0, None, 0), (41, 'gk_reflexes', 'INTEGER', 0, None, 0)]



```python
print(plt.style.available)
```

    ['_classic_test', 'bmh', 'classic', 'dark_background', 'fast', 'fivethirtyeight', 'ggplot', 'grayscale', 'seaborn-bright', 'seaborn-colorblind', 'seaborn-dark-palette', 'seaborn-dark', 'seaborn-darkgrid', 'seaborn-deep', 'seaborn-muted', 'seaborn-notebook', 'seaborn-paper', 'seaborn-pastel', 'seaborn-poster', 'seaborn-talk', 'seaborn-ticks', 'seaborn-white', 'seaborn-whitegrid', 'seaborn', 'Solarize_Light2', 'tableau-colorblind10']



```python
# Now select the data (height and weight) from the Player Table
# Note height is in cm and weight is in lbs

# Set the execute SQL command for getting height and weight columns of data
# from table called Player
c.execute('SELECT overall_rating, potential, long_passing, short_passing, sprint_speed, stamina FROM Player_Attributes')

# Fetch all data that accords with the execute command above
# NOTE: This command returns a list of tuples!!

jons_allrows = c.fetchall()

# In the code below I have created a list called soccerlist.
# Then I have populated the list with the rows gathered from 
# the database. Each row has two variables - height and weight
# so that 

jons_soccerlist = []
i = 0
for row in jons_allrows:
    # I choose to only grab  the first 3000 records
    if i < 3000:
        # NOTE: That row is a tuple!!
        # We can easily convert it to a list with the list() function before appending it
        # We are creating a 3000 length list of 5 length lists (height, weight)
        jons_soccerlist.append(list(row))
    i = i + 1
    
#Change list into an array    
jons_soccerarray = np.array(jons_soccerlist, dtype=float)

print ('=======================================')
print ('The array of player attributes')
print ('=======================================')
print (jons_soccerarray)

# I use this numpy command to make sure there are no NaNs in the array
five_attributes = jons_soccerarray[~np.isnan(jons_soccerarray).any(axis=1)]

#Make A pretty Visiualization
plt.style.use('ggplot')

plt.rcParams['font.family'] = 'serif'
plt.rcParams['font.serif'] = 'Ubuntu'
plt.rcParams['font.monospace'] = 'Ubuntu Mono'
plt.rcParams['font.size'] = 8
plt.rcParams['axes.labelsize'] = 8
plt.rcParams['axes.labelweight'] = 'bold'
plt.rcParams['axes.titlesize'] = 10
plt.rcParams['xtick.labelsize'] = 8
plt.rcParams['ytick.labelsize'] = 8
plt.rcParams['legend.fontsize'] = 10
plt.rcParams['figure.titlesize'] = 10

# Set an aspect ratio
width, height = plt.figaspect(0.05)
fig = plt.figure(figsize=(12,8), dpi=400)

# Overall Rating Vs Potential
ax1 = plt.subplot(331)
ax1.scatter(five_attributes[:,0], five_attributes[:,1])
#plt.xticks([1, 2, 3, 4], ['Q1', 'Q2', 'Q3', 'Q4'])
plt.xlabel('Overall Rating (Rating Assesment)')
plt.ylabel('Potential (Score Assesment)')
# Font style isn't accessible through rcParams
ax1.set_title("Overall Rating Vs Potential", fontstyle='italic')


# Short Passing Vs Speed
ax2 = plt.subplot(332)

ax2.scatter(five_attributes[:,3], five_attributes[:,4])
plt.xlabel('Short Passing')
plt.ylabel('Speed')
ax2.set_title('Short Passing Vs Speed', fontstyle='italic')


    
# Long Passing Vs Speed
ax3 = plt.subplot(333)
ax3.scatter(five_attributes[:,2], five_attributes[:,4])
    
plt.xlabel('Long Passing')
plt.ylabel('Speed')
ax3.set_title('Long Passing Vs Speed', fontstyle='italic')

    
# Long Passing Vs Overall Rating
ax4 = plt.subplot(334)
ax4.scatter(five_attributes[:,2], five_attributes[:,0])
    
plt.xlabel('Long Passing')
plt.ylabel('Overall Rating')
ax4.set_title('Long Passing Vs Overall Rating', fontstyle='italic')


#overall Rating Vs Speed
ax5 = plt.subplot(335)
ax5.scatter(five_attributes[:,0], five_attributes[:,4])
plt.xlabel('Overall Rating')
plt.ylabel('Speed')
ax5.set_title('Overall Rating Vs Speed', fontstyle='italic')


# Overall Rating Vs Stamina
#Out of curriosity I wanted to know if there was a stonger correlation between speed and 
#overall rating or stamina and overall rating
ax6 = plt.subplot(336)
ax6.scatter(five_attributes[:,0], five_attributes[:,5])
    
plt.xlabel('verall Rating')
plt.ylabel('Stamina')
ax6.set_title('Overall Rating Vs Stamina', fontstyle='italic')
# Axes alteration to put zero values inside the figure Axes
# Avoids axis white lines cutting through zero values - fivethirtyeight style


# Space plots a bit
plt.subplots_adjust(hspace=0.9, wspace=0.7)

fig.suptitle("European Soccer Database")
plt.savefig('European Soccer Database.svg', bbox_inches='tight')  

```

    =======================================
    The array of player attributes
    =======================================
    [[67. 71. 64. 61. 64. 54.]
     [67. 71. 64. 61. 64. 54.]
     [62. 66. 64. 61. 64. 54.]
     ...
     [61. 65. 62. 64. 64. 59.]
     [61. 65. 62. 64. 64. 59.]
     [61. 65. 62. 64. 64. 59.]]





    Text(0.5,0.98,'European Soccer Database')




![png](output_5_2.png)



```python
# We can use a pandas dataframe to easily get a scatter matrix plot with ease
# since pandas dataframes have lots of nice methods associated with them

import pandas as pd
df = pd.DataFrame(five_attributes, columns=['overall_rating', 
                                            'potential', 
                                            'long_passing', 
                                            'short_passing', 
                                            'sprint_speed', 
                                            'stamina'
                                           ])
#kde is a density plot it is like a smoother histogram
pd.scatter_matrix(df, alpha=0.2, figsize=(6, 6), diagonal='kde')
plt.show()


```

    /Users/jonathanconrow/anaconda3/envs/py36/lib/python3.6/site-packages/ipykernel_launcher.py:13: FutureWarning: pandas.scatter_matrix is deprecated. Use pandas.plotting.scatter_matrix instead
      del sys.path[0]



![png](output_6_1.png)



```python
print(df.info())
print(df.describe())

```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 2993 entries, 0 to 2992
    Data columns (total 6 columns):
    overall_rating    2993 non-null float64
    potential         2993 non-null float64
    long_passing      2993 non-null float64
    short_passing     2993 non-null float64
    sprint_speed      2993 non-null float64
    stamina           2993 non-null float64
    dtypes: float64(6)
    memory usage: 140.4 KB
    None
           overall_rating    potential  long_passing  short_passing  sprint_speed  \
    count     2993.000000  2993.000000   2993.000000    2993.000000   2993.000000   
    mean        67.390578    72.904778     56.546943      62.728032     69.465085   
    std          7.215421     6.754427     12.823353      12.675224     11.854942   
    min         42.000000    53.000000     11.000000      15.000000     26.000000   
    25%         63.000000    68.000000     48.000000      57.000000     63.000000   
    50%         68.000000    73.000000     58.000000      65.000000     71.000000   
    75%         72.000000    78.000000     66.000000      71.000000     78.000000   
    max         85.000000    90.000000     84.000000      85.000000     95.000000   
    
               stamina  
    count  2993.000000  
    mean     67.476445  
    std      11.950217  
    min      18.000000  
    25%      61.000000  
    50%      70.000000  
    75%      75.000000  
    max      93.000000  


It would appear that the strongest correlation out of the 6 attributes I selected would be the players overal rating vs the players potential. I belive this to be true based on the fact the in the scatter matrix the density plot for these two attributes has the most bell shapped curve
